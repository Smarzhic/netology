# Домашнее задание к занятию "6.4. PostgreSQL"
## Задача 1
```Yml
version: '3.6'


services:

  db:
    image: bitnami/postgresql:13.3.0
    container_name: psql
    ports:
     - 5432:5432
    volumes:
     - /opt/data/postgresql_data:/var/lib/psql
    environment:
     - POSTGRESQL_PASSWORD=password
    restart: always
```
```bash
docker exec -it psql bash
psql -h localhost -p 5432 -U postgres -W
```
- `\l` вывода списка БД
- `\c` подключения к БД
- `\d`вывода списка таблиц
- `\d` <table_name> вывода описания содержимого таблиц
- `\q` выхода из psql
## Задача 2
```SQL
postgres=# CREATE DATABASE test_database;
CREATE DATABASE
```

```bash
psql -U postgres -f ./pg_backup.sql test_database
```

```SQL
postgres=# \c test_database
Password:
You are now connected to database "test_database" as user "postgres".
test_database=# ANALYZE VERBOSE public.orders;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
test_database=# SELECT attname, avg_width FROM pg_stats WHERE tablename = 'orders' ORDER BY avg_width DESC LIMIT 1;
 attname | avg_width
---------+-----------
 title   |        16
(1 row)
```
## Задача 3
Шадрирование уже существующей таблицы
```SQL
test_database=# BEGIN TRANSACTION;
BEGIN
test_database=*# CREATE TABLE orders_1 (CONSTRAINT price_1 CHECK (price > 499)) INHERITS (orders);
CREATE TABLE
test_database=*# CREATE TABLE orders_2 (CONSTRAINT price_2 CHECK (price <= 499)) INHERITS (orders);
CREATE TABLE
test_database=*# INSERT INTO orders_1 SELECT * FROM orders where price > 499;
INSERT 0 3
test_database=*# INSERT INTO orders_2 SELECT * FROM orders where price <= 499;
INSERT 0 5
test_database=*#
```
