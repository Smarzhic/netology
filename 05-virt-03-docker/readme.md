# Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера"
## Задача 1
Сценарий выполения задачи:

создайте свой репозиторий на https://hub.docker.com;  
выберете любой образ, который содержит веб-сервер Nginx;  
создайте свой fork образа;  
реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:  

## Решение 1
Файл образа - https://hub.docker.com/r/smarzhic/nginx_imdevops  
Подключаюсь под своей учеткой к hub.docker
```
vagrant@server1:~$ docker login --username smarzhic
```
Находим контейнер с nginx
```
vagrant@server1:~$ docker search nginx
```
скачиваю образ
```
vagrant@server1:~$ docker pull nginx
```
запускаю контейнер и пробрасываю порт 80 на порт 8080 хостовой машины
```
vagrant@server1:~$ docker run -d --name nginx -p 8080:80 nginx
```
Захожу внутрь контейнера
```
vagrant@server1:~$ docker exec -it nginx bash
```
для внесение изменений копируем себе файл index.html
```
vagrant@server1:~$ sudo docker cp e42f6303e48a:/usr/share/nginx/html/index.html /home/
```
вносим необходимые изенения
```
vagrant@server1:/home$ sudo nano index.html
```
копируем измененный файл в контейнер
```
vagrant@server1:/home$ docker cp index.html e42f6303e48a:/usr/share/nginx/html
```
проверяем что изменения были внесены верно
```
vagrant@server1:/home$ curl localhost:8080
<!DOCTYPE html>
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Коммитим создданый образ
```
vagrant@server1:/home$ docker commit -m "html changed" -a "smarzhic" nginx nginx_imdevops
```
Задаем тэг образу
```
vagrant@server1:/home$ docker tag nginx_imdevops smarzhic/nginx_imdevops:netology
```
Загружаем его на докерхаб
```
vagrant@server1:/home$ docker push smarzhic/nginx_imdevops:netology
```
# Задача 2
Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование Docker контейнеров или лучше  
подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"
| Сценарий | Условия использования|
| ------------- | ------------- |
| Высоконагруженное монолитное java веб-приложение | Правильно будет использовать физ сервер т.к. монолитное и высоконагруженное  |
| Nodejs веб-приложение; |Использование докера считаю правильным, позволит быстро менять версию ноды |
| Мобильное приложение c версиями для Android и iOS | мобильные приложения подразумевают использование GUI что делает не возможным использование контейнеров. Если разговор идет о фронт части |
| Шина данных на базе Apache Kafka| Для сохранения целостности данных лучше использовать физ или вир машину|
| Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana| Классический пример для использования docker|
| Мониторинг-стек на базе Prometheus и Grafana|Использование докера целессобразно так как нет требования к целостности данных + скорость развертывания|
| MongoDB, как основное хранилище данных для java-приложения| докер не подходит из за существенной вероятности потери данных. Праивльно будет использовать вирт машины или физ серевера.|
| Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry| физические сервера или вирт машины|
## Задача 3
Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
```
vagrant@server1:/home$ docker run -dti --name centos -v /data:/data centos
```
Запустите второй контейнер из образа debian в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
```
docker run -dti --name debian -v /data:/data debian
```
Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data;
```
vagrant@server1:/home$ docker exec -ti centos bash
[root@6f40b94bf47a /]# cd data/
[root@6f40b94bf47a data]# touch test.txt
```
Добавьте еще один файл в папку /data на хостовой машине
```
vagrant@server1:/home/data$ cd /data
vagrant@server1:/data$ sudo touch test2.txt
```
Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера
```
vagrant@server1:~$ docker exec -ti debian bash
root@d3e823df61ac:/# ls data/
test.txt  test2.txt
```
