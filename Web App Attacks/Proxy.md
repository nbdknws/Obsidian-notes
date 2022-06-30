squid, find open ports 

```
ffuf -x http://192.168.155.189:3128 -u http://127.0.0.1:FUZZ -w ports.txt
```