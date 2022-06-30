```windows
.\hashcat.exe --force --stdout pass.lst -r .\rules\best64.rule >> password_list.txt
.\hashcat.exe --force --stdout passwor_list.txt  -r .\rules\toggles1.rule
```

don't forget to check password policy 

```
crackmapexec smb 10.10.10.161 --pass-pol
```