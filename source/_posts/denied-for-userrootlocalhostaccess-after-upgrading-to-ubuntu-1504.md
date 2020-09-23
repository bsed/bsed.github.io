---
title: Access denied for user 'root'@'localhost' after upgrading to Ubuntu 15.04
date: 2015-06-25 20:59:00
updated: 2016-06-25 21:01:16
tags: 
- android
categories: 
- android

---
Today, I wanted to create a database in PMA. It said: "Cannot log in to the MySQL server". I tried via a terminal, same problem, and it is because my password is wrong. And I can't understand why.

I tried the usual method to reset the root password (skip grant tables mounting and reset the passord) but it seems it doesn't works.

See that:

<script src="https://gist.github.com/d0f/be86c5dbf502da91a009c151c1b942bd.js"></script>

<!--more-->


# one shell
```bash
sudo mysqld_safe --skip-grant-tables &
```
# two shell
```bash
kelvin@gnux:~> sudo mysql -u root                               (06/25 20:51:01)
[sudo] password for kelvin: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 41
Server version: 10.0.25-MariaDB-0ubuntu0.16.04.1 Ubuntu 16.04

Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> select user,plugin from user;
+------+-------------+
| user | plugin      |
+------+-------------+
| root | unix_socket |
+------+-------------+
1 row in set (0.00 sec)

MariaDB [mysql]> UPDATE user SET plugin=""; 
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [mysql]> UPDATE user SET password=PASSWORD("password") WHERE user="root";
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [mysql]> flush privileges;
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]>
```
## end
```bash
MariaDB [mysql]> quit
Bye
kelvin@gnux:~> mysql -u root -p                                 (06/25 20:57:27)
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 42
Server version: 10.0.25-MariaDB-0ubuntu0.16.04.1 Ubuntu 16.04

Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```