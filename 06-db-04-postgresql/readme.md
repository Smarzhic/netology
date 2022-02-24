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
