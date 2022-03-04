# Задание 1
```
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
