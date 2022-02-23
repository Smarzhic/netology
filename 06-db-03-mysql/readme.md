# Домашнее задание к занятию "6.3. MySQL"
## Задача 1
```YML
version: '3.6'

volumes:
  data: {}

services:

  db:
    image: mysql:8.0
    container_name: mysql
    command: --default-authentication-plugin=mysql_native_password
    ports:
      - 3306:3306
    volumes:
      - /opt/data/mysql_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: password
    restart: always
```


```bash
docker exec -it mysql bash
mysql -h 127.0.0.1 -u root -p
```

```MySQl
mysql> CREATE DATABASE test_db DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
````

```bash 
mysql -p test_db < /var/lib/mysql/test_dump.sql
```

```MySQL
mysql> \s
--------------
mysql  Ver 8.0.28 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          25
Current database:
Current user:           root@127.0.0.1
SSL:                    Cipher in use is TLS_AES_256_GCM_SHA384
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.28 MySQL Community Server - GPL
Protocol version:       10
Connection:             127.0.0.1 via TCP/IP
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    latin1
Conn.  characterset:    latin1
TCP port:               3306
Binary data as:         Hexadecimal
Uptime:                 49 min 57 sec

Threads: 2  Questions: 75  Slow queries: 0  Opens: 157  Flush tables: 3  Open tables: 75  Queries per second avg: 0.025
--------------
```

```MySQL

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test_db            |
+--------------------+
5 rows in set (0.01 sec)
mysql> use test_db
mysql> show tables;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.00 sec)
mysql> select count(*) from orders where price >300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```
