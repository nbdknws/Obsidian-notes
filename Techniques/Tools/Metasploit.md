activate the module

```
use auxiliary/scanner/portscan/tcp
```

don't forget to set options

```
show options
```

`set` or `unset` for an option
`setg` or `unsetg` for global options

MSF stores the results and finding in the postgres db, to view them

```
services

services -p 445

creds

workspace

workspace test
```

Nmap inside MSF

```
msf6 auxiliary(scanner/portscan/tcp) > db_nmap
msf6 auxiliary(scanner/portscan/tcp) > db_nmap 192.168.120.11 -A -Pn
```

```
msf6 auxiliary(scanner/portscan/tcp) > hosts
```

```
msf6 auxiliary(scanner/portscan/tcp) > services -p 445
```

search for exploits

```
search type:auxiliary name:smb
```

check exploit info

```
info exploit/windows/http/syncbreeze_bof
```

don't forget to change the payload

```
show payloads
```


**shells**

```
windows/shell_reverse_tcp - Connect back to attacker and spawn a command shell
windows/shell/reverse_tcp - Connect back to attacker, Spawn cmd shell (staged)
```