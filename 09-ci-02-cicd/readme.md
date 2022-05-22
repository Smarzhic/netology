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
2. [maven-metadata](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/maven-metadata.xml)
3. [pom](https://github.com/Smarzhic/netology/blob/main/09-ci-02-cicd/pom.xml)
