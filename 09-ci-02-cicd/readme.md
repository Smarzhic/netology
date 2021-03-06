# Домашнее задание к занятию "09.02 CI\CD"
## Готовим среду
>![PID 1](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/1.png)


`hosts.yml`
```yml
---
all:
  hosts:
    sonar-01:
      ansible_host: 51.250.22.212
    nexus-01:
      ansible_host: 62.84.121.111
  children:
    sonarqube:
      hosts:
        sonar-01:
    nexus:
      hosts:
        nexus-01:
    postgres:
      hosts:
        sonar-01:
  vars:
    ansible_connection_type: paramiko
    ansible_user: netology
 ```
Запускаем плейбук `ansible-playbook -i inventory/cicd/hosts.yml site.yml`

Проверяем доступность 
`SonarQube`
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/3.png)

`Nexus`
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/4.png)

`Maven`
```bash
netology@netology:~/Projects/all_exercises$ export PATH=$PATH:/home/netology/progs/apache-maven-3.8.4-bin/apache-maven-3.8.4/bin
netology@netology:~/Projects/all_exercises$ echo $PATH
/home/netology/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/netology/.local/bin:/home/netology/progs/sonar-scanner-4.6.2.2472-linux/bin:/home/netology/progs/apache-maven-3.8.4-bin/apache-maven-3.8.4/bin
```
Разрешаем доступ по http
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/5.png)

```bash
netology@netology:~/Projects/ci_cd/current_exercises/9.3ex/example$ mvn --version
Apache Maven 3.8.4 (9b656c72d54e5bacbed989b64718c159fe39b537)
Maven home: /home/netology/progs/apache-maven-3.8.4-bin/apache-maven-3.8.4
Java version: 11.0.13, vendor: Ubuntu, runtime: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: ru_RU, platform encoding: UTF-8
OS name: "linux", version: "5.13.0-25-generic", arch: "amd64", family: "unix"
```
## Знакомоство с SonarQube
Создаём новый проект, название произвольное
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/6.png)

Скачиваем пакет sonar-scanner, который нам предлагает скачать сам sonarqube  
Делаем так, чтобы binary был доступен через вызов в shell (или меняем переменную PATH или любой другой удобный вам способ)
```bash
netology@netology:~/Projects$ export PATH=$PATH:/home/netology/progs/sonar-scanner-4.6.2.2472-linux/bin
netology@netology:~/Projects$ echo $PATH
/home/netology/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/netology/.local/bin:/home/netology/progs/sonar-scanner-4.6.2.2472-linux/bin
```
Проверяем sonar-scanner --version
```bash
netology@netology:~/Projects$ sonar-scanner --version
INFO: Scanner configuration file: /home/netology/progs/sonar-scanner-4.6.2.2472-linux/conf/sonar-scanner.properties
INFO: Project root configuration file: NONE
INFO: SonarScanner 4.6.2.2472
INFO: Java 11.0.11 AdoptOpenJDK (64-bit)
INFO: Linux 5.13.0-25-generic amd64
```
Запускаем `sonar-scanner`
```bash
sonar-scanner \
>   -Dsonar.projectKey=NC-power \
>   -Dsonar.sources=. \
>   -Dsonar.host.url=http://51.250.22.212:9000 \
>   -Dsonar.login=5562f9278f9a797a74ed5b5577314a05a4332cc7
```
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/7.png)
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/8.png)

Запускаем анализатор против кода из директории example с дополнительным ключом -Dsonar.coverage.exclusions=fail.py
```bash
sonar-scanner \
>   -Dsonar.projectKey=NC-power \
>   -Dsonar.sources=. \
>   -Dsonar.host.url=http://51.250.22.212:9000 \
>   -Dsonar.login=5562f9278f9a797a74ed5b5577314a05a4332cc7 \
>   -Dsonar.coverage.exclusions=fail.py
```
Проверяем резудтат после исклоючения файла
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/9.png)

Баги остались
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/10.png)

Исправляем ошибки, которые он выявил(включая warnings)
```bash
def increment(index):
    local_index = 0
    local_index += 1
    local_index += index
    return local_index
def get_square(numb):
    return numb*numb
def print_numb(numb):
    print("Number is {}".format(numb))

index = 0
while (index < 10):
    index = increment(index)
    print(get_square(index))
```
Запуск осуществляла с `-Dsonar.python.version=2` чтобы ушло предупреждение об отсутствии версии `python`
```bash
netology@netology:~/Projects/ci_cd/current_exercises/9.3ex/example$ 
sonar-scanner \
   -Dsonar.projectKey=NC-power \
   -Dsonar.sources=. \
   -Dsonar.host.url=http://51.250.22.212:9000 \
   -Dsonar.login=5562f9278f9a797a74ed5b5577314a05a4332cc7 \
   -Dsonar.coverage.exclusions=fail.py \
   -Dsonar.python.version=2
```
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/11.png)
Запускаем анализатор повторно
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/12.png)

##Знакомоство с Nexus
В репозиторий maven-public загружаем артефакт с GAV параметрами:

```bash
    * groupId: netology
    * artifactId: java
    * version: 8_202
    * classifier: distrib
    * type: tar.gz
```
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/13.png)
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/14.png)
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/15.png)
 
В него же загружаем такой же артефакт, но с version: 8_102
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/16.png)
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/17.png)

Проверяем, что все файлы загрузились успешно
>![PID 2](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/img/18.png)

metadata
```xml
<metadata modelVersion="1.1.0">
    <groupId>netology</groupId>
    <artifactId>java</artifactId>
    <versioning>
        <latest>8_202</latest>
        <release>8_202</release>
        <versions>
            <version>8_102</version>
            <version>8_202</version>
        </versions>
        <lastUpdated>20220119203554</lastUpdated>
    </versioning>
</metadata>
```
## Знакомоство с Maven
Меняем в pom.xml блок с зависимостями под наш артефакт из первого пункта задания для Nexus (java с версией 8_202)

pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.netology.app</groupId>
  <artifactId>simple-app</artifactId>
  <version>1.0-SNAPSHOT</version>
   <repositories>
    <repository>
      <id>my-repo</id>
      <name>maven-public</name>
      <url>http://62.84.121.111:8081/repository/maven-public/</url>
    </repository>
  </repositories>
  <dependencies>
    <dependency>
      <groupId>netology</groupId>
      <artifactId>java</artifactId>
      <version>8_202</version>
      <classifier>distrib</classifier>
      <type>tar.gz</type>
    </dependency>
  </dependencies>
</project>
```
Запускаем команду mvn package в директории с pom.xml, ожидаем успешного окончания

Проверяем директорию ~/.m2/repository/, находим наш артефакт

```bash
netology@netology:~/Projects/ci_cd/current_exercises/9.3ex/mvn$ ls -l ~/.m2/repository/
итого 40
drwxrwxr-x 3 netology netology 4096 янв 19 23:59 backport-util-concurrent
drwxrwxr-x 3 netology netology 4096 янв 19 23:59 classworlds
drwxrwxr-x 3 netology netology 4096 янв 19 23:59 com
drwxrwxr-x 3 netology netology 4096 янв 19 23:59 commons-cli
drwxrwxr-x 3 netology netology 4096 янв 19 23:59 commons-lang
drwxrwxr-x 3 netology netology 4096 янв 19 23:59 commons-logging
drwxrwxr-x 3 netology netology 4096 янв 19 23:59 junit
drwxrwxr-x 3 netology netology 4096 янв 19 23:59 log4j
drwxrwxr-x 3 netology netology 4096 янв 19 23:59 netology
drwxrwxr-x 6 netology netology 4096 янв 19 23:59 org

netology@netology:~/Projects/ci_cd/current_exercises/9.3ex/mvn$ cd ~/.m2/repository/netology/
netology@netology:~/.m2/repository/netology$ tree
.
└── java
    └── 8_202
        ├── java-8_202-distrib.tar.gz
        ├── java-8_202-distrib.tar.gz.sha1
        ├── java-8_202.pom.lastUpdated
        └── _remote.repositories

2 directories, 4 files
```
