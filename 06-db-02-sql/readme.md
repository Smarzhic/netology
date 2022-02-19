# Домашнее задание к занятию "6.2. SQL"
## Задача 1
Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.
```yml
version: '3.6'

volumes:
  data: {}
  backup: {}

services:

  postgres:
    image: postgres:12
    container_name: psql
    ports:
      - "0.0.0.0:5432:5432"
    volumes:
      - data:/var/lib/postgresql/data
      - backup:/media/postgresql/backup
    environment:
      POSTGRES_USER: "test-admin-user"
      POSTGRES_PASSWORD: "test"
      POSTGRES_DB: "test_db"
    restart: always
```   
```bash
vagrant@server1:~/SQL$ docker-compose up -d
vagrant@server1:~/SQL$ docker exec -it psql bash
root@2e7747507816:/# export PGPASSWORD=test && psql -h localhost -U test-admin-user test_db
psql (12.10 (Debian 12.10-1.pgdg110+1))
Type "help" for help.

test_db=#
```
## Задача 2
В БД из задачи 1:

- создайте пользователя test-admin-user и БД test_db  
```SQL
создано в docker-compose манифесте ранее
test_db=# CREATE USER "test-admin-user";
ERROR:  role "test-admin-user" already exists
test_db=# CREATE DATABASE test_db;
ERROR:  database "test_db" already exists
```
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)  
```SQL
test_db=# CREATE TABLE orders (id INT PRIMARY KEY, title VARCHAR(255), cost INT NOT NULL);
CREATE TABLE
test_db=# CREATE TABLE clients (id SERIAL PRIMARY KEY, last_name VARCHAR(50), country VARCHAR(50), order_id INT REFERENCES orders(id) ON DELETE CASCADE);
CREATE TABLE
```
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db  
```SQL
test_db=# GRANT CONNECT ON DATABASE test_db to "test-admin-user";
GRANT
test_db=# GRANT ALL ON ALL TABLES IN SCHEMA public to "test-admin-user";
GRANT
```
- создайте пользователя test-simple-user  
```SQL
test_db=# CREATE USER "test-simple-user" WITH PASSWORD 'test';
CREATE ROLE
```
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db  
```SQL
test_db=# CREATE USER "test-simple-user" WITH PASSWORD 'test';
CREATE ROLE
test_db=# GRANT CONNECT ON DATABASE test_db TO "test-simple-user";
GRANT
test_db=# GRANT USAGE ON SCHEMA public TO "test-simple-user";
GRANT
test_db=# GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public to "test-simple-user";
GRANT
```
Приведите:

итоговый список БД после выполнения пунктов выше  
```SQL
test_db=#  \l+
                                                                               List of databases
   Name    |      Owner      | Encoding |  Collate   |   Ctype    |            Access privileges            |  Size   |
Tablespace |                Description
-----------+-----------------+----------+------------+------------+-----------------------------------------+---------+------------+--------------------------------------------
 postgres  | test-admin-user | UTF8     | en_US.utf8 | en_US.utf8 |                                         | 7969 kB |
pg_default | default administrative connection database
 template0 | test-admin-user | UTF8     | en_US.utf8 | en_US.utf8 | =c/"test-admin-user"                   +| 7825 kB |
pg_default | unmodifiable empty database
           |                 |          |            |            | "test-admin-user"=CTc/"test-admin-user" |         |
           |
 template1 | test-admin-user | UTF8     | en_US.utf8 | en_US.utf8 | =c/"test-admin-user"                   +| 7825 kB |
pg_default | default template for new databases
           |                 |          |            |            | "test-admin-user"=CTc/"test-admin-user" |         |
           |
 test_db   | test-admin-user | UTF8     | en_US.utf8 | en_US.utf8 | =Tc/"test-admin-user"                  +| 8081 kB |
pg_default |
           |                 |          |            |            | "test-admin-user"=CTc/"test-admin-user"+|         |
           |
           |                 |          |            |            | "test-simple-user"=c/"test-admin-user"  |         |
           |
(4 rows)
```

описание таблиц (describe)  
```SQL
test_db=# \d+ clients
                                                         Table "public.clients"
  Column   |         Type          | Collation | Nullable |               Default               | Storage  | Stats target | Description
-----------+-----------------------+-----------+----------+-------------------------------------+----------+--------------+-------------
 id        | integer               |           | not null | nextval('clients_id_seq'::regclass) | plain    |              |
 last_name | character varying(50) |           |          |                                     | extended |              |
 country   | character varying(50) |           |          |                                     | extended |              |
 order_id  | integer               |           |          |                                     | plain    |              |
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
Foreign-key constraints:
    "clients_order_id_fkey" FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE
Access method: heap

test_db=# \d+ orders
                                          Table "public.orders"
 Column |          Type          | Collation | Nullable | Default | Storage  | Stats target | Description
--------+------------------------+-----------+----------+---------+----------+--------------+-------------
 id     | integer                |           | not null |         | plain    |              |
 title  | character varying(255) |           |          |         | extended |              |
 cost   | integer                |           | not null |         | plain    |              |
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Referenced by:
    TABLE "clients" CONSTRAINT "clients_order_id_fkey" FOREIGN KEY (order_id) REFERENCES orders(id) ON DELETE CASCADE
Access method: heap
```
SQL-запрос для выдачи списка пользователей с правами над таблицами test_db  
```SQL
test_db=# SELECT * FROM information_schema.table_privileges WHERE table_catalog = 'test_db' AND grantee LIKE 'test-%';
```
список пользователей с правами над таблицами test_db
```SQL
     grantor     |     grantee      | table_catalog |    table_schema    |              table_name               | privilege_type | is_grantable | with_hierarchy
-----------------+------------------+---------------+--------------------+---------------------------------------+----------------+--------------+----------------
 test-admin-user | test-admin-user  | test_db       | public             | orders                                | INSERT         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | orders                                | SELECT         | YES          | YES
 test-admin-user | test-admin-user  | test_db       | public             | orders                                | UPDATE         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | orders                                | DELETE         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | orders                                | TRUNCATE       | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | orders                                | REFERENCES     | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | orders                                | TRIGGER        | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | clients                               | INSERT         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | clients                               | SELECT         | YES          | YES
 test-admin-user | test-admin-user  | test_db       | public             | clients                               | UPDATE         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | clients                               | DELETE         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | clients                               | TRUNCATE       | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | clients                               | REFERENCES     | YES          | NO
 test-admin-user | test-admin-user  | test_db       | public             | clients                               | TRIGGER        | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_statistic                          | INSERT         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_statistic                          | SELECT         | YES          | YES
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_statistic                          | UPDATE         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_statistic                          | DELETE         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_statistic                          | TRUNCATE       | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_statistic                          | REFERENCES     | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_statistic                          | TRIGGER        | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_type                               | INSERT         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_type                               | SELECT         | YES          | YES
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_type                               | UPDATE         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_type                               | DELETE         | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_type                               | TRUNCATE       | YES          | NO
 test-admin-user | test-admin-user  | test_db       | pg_catalog         | pg_type                               | REFERENCES     | YES          | NO
--More--
```
