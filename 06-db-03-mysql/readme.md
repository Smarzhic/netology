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
