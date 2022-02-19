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
    
