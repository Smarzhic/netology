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
CREATE USER "test-admin-user";
ERROR:  role "test-admin-user" already exists
CREATE DATABASE test_db;
ERROR:  database "test_db" already exists
```
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)  
```SQL
CREATE TABLE orders (id INT PRIMARY KEY, title VARCHAR(255), cost INT NOT NULL);
CREATE TABLE
CREATE TABLE clients (id SERIAL PRIMARY KEY, last_name VARCHAR(50), country VARCHAR(50), order_id INT REFERENCES orders(id) ON DELETE CASCADE);
CREATE TABLE
```
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db  
```SQL
GRANT CONNECT ON DATABASE test_db to "test-admin-user";
GRANT
GRANT ALL ON ALL TABLES IN SCHEMA public to "test-admin-user";
GRANT
```
- создайте пользователя test-simple-user  
```SQL
CREATE USER "test-simple-user" WITH PASSWORD 'test';
CREATE ROLE
```
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db  
```SQL
CREATE USER "test-simple-user" WITH PASSWORD 'test';
CREATE ROLE
GRANT CONNECT ON DATABASE test_db TO "test-simple-user";
GRANT
GRANT USAGE ON SCHEMA public TO "test-simple-user";
GRANT
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public to "test-simple-user";
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
SELECT * FROM information_schema.table_privileges WHERE table_catalog = 'test_db' AND grantee LIKE 'test-%';
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
## Задача 3
Используя SQL синтаксис - наполните таблицы следующими тестовыми данными, вычислите количество записей для каждой таблицы
```SQL
INSERT INTO orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
INSERT 0 5
SELECT * FROM orders;
 id |  title  | cost
----+---------+------
  1 | Шоколад |   10
  2 | Принтер | 3000
  3 | Книга   |  500
  4 | Монитор | 7000
  5 | Гитара  | 4000
(5 rows)
SELECT count(1) FROM orders;
 count
-------
     5
(1 row)
INSERT INTO clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
test_db=# SELECT  * FROM clients;
 id |      last_name       | country | order_id
----+----------------------+---------+----------
  1 | Иванов Иван Иванович | USA     |
  2 | Петров Петр Петрович | Canada  |
  3 | Иоганн Себастьян Бах | Japan   |
  4 | Ронни Джеймс Дио     | Russia  |
  5 | Ritchie Blackmore    | Russia  |
(5 rows)
SELECT count(1) FROM clients;
 count
-------
     5
(1 row)
```
## Задача 4
Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:
```SQL
UPDATE clients SET order_id = 3 WHERE last_name = 'Иванов Иван Иванович';
UPDATE 1
UPDATE clients SET order_id = 4 WHERE last_name = 'Петров Петр Петрович';
UPDATE 1
UPDATE clients SET order_id = 5 WHERE last_name = 'Иоганн Себастьян Бах';
UPDATE 1
SELECT c.id, c.last_name, c.country, o.title FROM clients AS c INNER JOIN orders AS o ON o.id = c.order_id;
 id |      last_name       | country |  title
----+----------------------+---------+---------
  1 | Иванов Иван Иванович | USA     | Книга
  2 | Петров Петр Петрович | Canada  | Монитор
  3 | Иоганн Себастьян Бах | Japan   | Гитара
(3 rows)
```
## Задача 5
Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).  
```SQL
EXPLAIN SELECT c.id, c.last_name, c.country, o.title FROM clients AS c INNER JOIN orders AS o ON o.id = c.order_id;
                               QUERY PLAN
-------------------------------------------------------------------------
 Hash Join  (cost=13.15..26.96 rows=300 width=756)
   Hash Cond: (c.order_id = o.id)
   ->  Seq Scan on clients c  (cost=0.00..13.00 rows=300 width=244)
   ->  Hash  (cost=11.40..11.40 rows=140 width=520)
         ->  Seq Scan on orders o  (cost=0.00..11.40 rows=140 width=520)
(5 rows)
```
EXPLAIN - выводит план построения запроса. PostgreSQL разделил запрос на 5 итераций:
 - время котрое пройдет прежде чем начнется вывод данных, стоимость запуска;
 - приблизительное время которое понадобится на вывод всех данных;
 - Ожидаемое число строк;
 - Ожидамый средний размер строк в байтах.
 ## Задача 6
 Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).
 ```bash
export PGPASSWORD=test && pg_dumpall -h localhost -U test-admin-user > /media/postgresql/backup/test_db.sql
ls /media/postgresql/backup/
test_db.sql
vagrant@server1:~/SQL$ docker-compose stop
Stopping psql ... done
vagrant@server1:~/SQL$ docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED             STATUS                      PORTS     NAMES
2e7747507816   postgres:12   "docker-entrypoint.s…"   About an hour ago   Exited (0) 13 seconds ago             psql
docker run --rm -d -e POSTGRES_USER=test-admin-user -e POSTGRES_PASSWORD=test -e POSTGRES_DB=test_db -v /var/lib/docker/volumes/sql_backup/_d
ata/:/media/postgresql/backup --name psqnew postgres:12
14ce5d2623223b1f4187da85ca7f1a06a6a6e97c6eccab970d6e44ff48d5217f
docker ps -a
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS          PORTS                    NAMES
981f10875bd1   postgres:12   "docker-entrypoint.s…"   6 seconds ago   Up 5 seconds    5432/tcp                 psqnew
2e7747507816   postgres:12   "docker-entrypoint.s…"   8 hours ago     Up 17 minutes   0.0.0.0:5432->5432/tcp   psql
docker exec -it psqnew  bash
ls /media/postgresql/backup/
test_db.sql
export PGPASSWORD=test && psql -h localhost -U test-admin-user -f /media/postgresql/backup/test_db.sql test_db
psql -h localhost -U test-admin-user test_db
psql (12.10 (Debian 12.10-1.pgdg110+1))
Type "help" for help.

test_db=#
