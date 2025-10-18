Port 3306

MariaDB, which is often connected with MySQL, is a fork of the original MySQL code. This is because the chief developer of MySQL left the company MySQL AB after it was acquired by Oracle and developed another open-source SQL database management system based on the source code of MySQL and called it MariaDB.

#### Scanning MySQL Server

``` bash
astrocat@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*

```
#### Interaction with the MySQL Server
``` bash
astrocat@htb[/htb]$ mysql -u root -pP4SSw0rd -h 10.129.14.128

```

Some of the commands we should remember and write down for working with MySQL databases are described below in the table.

|**Command**|**Description**|
|---|---|
|`mysql -u <user> -p<password> -h <IP address>`|Connect to the MySQL server. There should **not** be a space between the '-p' flag, and the password.|
|`show databases;`|Show all databases.|
|`use <database>;`|Select one of the existing databases.|
|`show tables;`|Show all available tables in the selected database.|
|`show columns from <table>;`|Show all columns in the selected table.|
|`select * from <table>;`|Show everything in the desired table.|
|`select * from <table> where <column> = "<string>";`|Search for needed `string` in the desired table.|
