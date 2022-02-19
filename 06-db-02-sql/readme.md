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
    
