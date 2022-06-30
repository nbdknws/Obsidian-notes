```
sudo /bin/neo4j start

bloodhound --no-sandbox
user: neo4j
pass: neo4jneo4j

bloodhound-python -u svc-alfresco -p 's3rvice' -ns 10.10.10.161 -d htb.local -c all
```


convert SID to username

```powershell
$objSID = New-Object System.Security.Principal.SecurityIdentifier 'S-1-5-21-2966785786-3096785034-1186376766-1108'
$objUser = $objSID.Translate( [System.Security.Principal.NTAccount]) 
$objUser.Value
```