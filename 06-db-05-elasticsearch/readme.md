# Задание 1

Ссылка на образ - https://hub.docker.com/r/smarzhic/elastik

DockerFile для создание образа

```DockerFile
FROM centos:7

RUN yum install wget -y

ENV ES_DISTRIB="/opt/elasticsearch"
ENV ES_HOME="${ES_DISTRIB}/elasticsearch-7.13.4"
ENV ES_DATA_DIR="/var/lib/elasticsearch"
ENV ES_LOG_DIR="/var/log/elasticsearch"
ENV ES_BACKUP_DIR="/opt/backups"
ENV ES_JAVA_OPTS="-Xms512m -Xmx512m"

WORKDIR "${ES_DISTRIB}"

RUN wget --quiet https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.13.4-linux-x86_64.tar.gz && \
    wget --quiet https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.13.4-linux-x86_64.tar.gz.sha512 && \
    sha512sum --check --quiet elasticsearch-7.13.4-linux-x86_64.tar.gz.sha512 && \
    tar -xzf elasticsearch-7.13.4-linux-x86_64.tar.gz

COPY elasticsearch.yml ${ES_HOME}/config

ENV ES_USER="elasticsearch"

RUN useradd ${ES_USER}

RUN mkdir -p "${ES_DATA_DIR}" && \
    mkdir -p "${ES_LOG_DIR}" && \
    mkdir -p "${ES_BACKUP_DIR}" && \
    chown -R ${ES_USER}: "${ES_DISTRIB}" && \
    chown -R ${ES_USER}: "${ES_DATA_DIR}" && \
    chown -R ${ES_USER}: "${ES_LOG_DIR}" && \
    chown -R ${ES_USER}: "${ES_BACKUP_DIR}"

USER ${ES_USER}

WORKDIR "${ES_HOME}"

EXPOSE 9200
EXPOSE 9300

ENTRYPOINT ["./bin/elasticsearch"]
```
elasticsearch.yml
```Yml
---
discovery:
  type: single-node

cluster:
  name: netology

node:
  name: netology_test

network:
  host: 0.0.0.0

path:
  data: /var/lib/elasticsearch
  logs: /var/log/elasticsearch
  repo:
    - /opt/backups
```
```bash
docker run --name elasticsearch_netology -d -p 9200:9200 -p 9300:9300 smarzhic/elastik:latest
smarzhic@docker:~/ELK$ curl localhost:9200/
{
  "name" : "netology_test",
  "cluster_name" : "netology",
  "cluster_uuid" : "pjehlvH5S5K1l1UlDP17JA",
  "version" : {
    "number" : "7.13.4",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "c5f60e894ca0c61cdbae4f5a686d9f08bcefc942",
    "build_date" : "2021-07-14T18:33:36.673943207Z",
    "build_snapshot" : false,
    "lucene_version" : "8.8.2",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```
# Задание 2
Добавляем индексы
```bash
curl -X PUT localhost:9200/ind-1 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
curl -X PUT localhost:9200/ind-2 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 2,  "number_of_replicas": 1 }}'
curl -X PUT localhost:9200/ind-3 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 4,  "number_of_replicas": 2 }}' 
```
Запрос индексов
```bash
smarzhic@docker:~/ELK$ curl -X GET "localhost:9200/_cat/indices"
green  open ind-1 VsLzJh4XSeadYv3O8oFtxg 1 0 0 0 208b 208b
yellow open ind-3 70WybNmCTlGFrL7juP3WAA 4 2 0 0 832b 832b
yellow open ind-2 3LmD2hQMR-SkubOnV_I-XA 2 1 0 0 416b 416b
```
Два индекса находится в состоянии yellow. Это вызвано неверным расчетом кол-во шардов и реплик. Для стабильной работы нам надо было выставлять number_of_shards = 3 и number_of_replicas = 0, а в эти рамки попадает только индекс ind-1.


Состояние кластера
```bash
smarzhic@docker:~/ELK$ curl -XGET localhost:9200/_cluster/health/?pretty=true
{
  "cluster_name" : "netology",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 7,
  "active_shards" : 7,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 41.17647058823529
}
```
