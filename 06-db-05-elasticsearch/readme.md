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
