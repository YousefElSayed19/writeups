
![](https://cdn-images-1.medium.com/max/1000/0*1mE_jUo8gp3Edg-L.png)

I started with a default Nmap scan, but no open ports were detected because it only scans the 1,000 most common TCP ports. To perform a complete scan more efficiently, I scanned all TCP ports using `-p-` with `--min-rate 5000`, which significantly increased the scanning speed. This revealed that **Redis** was running on port **6379**, making it the primary service to investigate.


![](https://cdn-images-1.medium.com/max/1000/0*8EO2xVwNRvSTRi4s.png)

To identify the stored data, I listed all keys in the default Redis database using the `KEYS *` command. Among the available keys, I found a key named `flag`. Retrieving its value with the `GET` command revealed the flag, successfully completing the machine.

```
redis-cli -h <TARGET_IP>
```

```

KEYS *
GET flag
```

![](https://cdn-images-1.medium.com/max/1000/0*6f3_ncLku0v3FbOK.png)

flag : 03e1d2b376c37ab3f5319922053953eb