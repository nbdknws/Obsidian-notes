
1. Enumerate without credentials

```
ldapsearch -v -x -b "DC=hutch,DC=offsec" -H "ldap://[192.168.120.108](https://192.168.120.108)" "(objectclass=*)"

ldapsearch -x -b "DC=htb,DC=local" -H ldap://10.10.10.161 "(objectclass=person)" samaccountname name

ldapsearch -x -b "DC=htb,DC=local" -H ldap://10.10.10.161 "(objectclass=person)" samaccountname | grep -i samaccountname
```


2. Pass-back (if you have access to LDAP configuration on the device)

make server to authenticate to you and send the credentials


```

```

