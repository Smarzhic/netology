# Курсовая работа по итогам модуля "DevOps и системное администрирование"

## 1. Первоначальная настройка сервера  
Для начала обновим свежеустановленный сервер.
```
smarzhic@websrv:~$ sudo apt update
smarzhic@websrv:~$ sudo apt upgrade
```
Дополним систему минимумом необходимых утилит
```
apt-get install wget chrony curl apt-transport-https
```
Для корректного получения токенов необходимо, чтобы время на сервере было правильное. Настраиваем время
```
timedatectl set-timezone Europe/Moscow
```
Добавляем автоматический запуск для сервиса синхронизации времени и перезапускаем его.
```
systemctl enable chrony
systemctl restart chrony
```
## 2.Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.

Проверим версию ufw
```
sudo ufw status
```

Если ufw не запущен, запускаем
```
sudo ufw enable
```

Создаем политику по умолчанию, указываюшие что делать с пакетами не подходящими под правила. Выберем  отклониять все входяшие и разрешить все исходящие
```
sudo ufw default deny incoming
sudo ufw default allow outgoing
```
Разрешим входящие и исходящие подключения к порту 22 и 443 для любого протокола
```
sudo ufw allow OpenSSH
sudo ufw allow 443
```
Проверим созданые правила
```
smarzhic@websrv:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp (OpenSSH)           ALLOW IN    Anywhere
443                        ALLOW IN    Anywhere
22/tcp (OpenSSH (v6))      ALLOW IN    Anywhere (v6)
443 (v6)                   ALLOW IN    Anywhere (v6)
```
## 3.Установите hashicorp vault
Рассмотрим установку из репозитория.
```
sudo curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt update && sudo apt install vault
```
```
smarzhic@websrv:~$ vault
Usage: vault <command> [args]

Common commands:
    read        Read data and retrieves secrets
    write       Write data, configuration, and secrets
    delete      Delete secrets and configuration
    list        List data or secrets
    login       Authenticate locally
    agent       Start a Vault agent
    server      Start a Vault server
    status      Print seal and HA status
    unwrap      Unwrap a wrapped secret

Other commands:
    audit          Interact with audit devices
    auth           Interact with auth methods
    debug          Runs the debug command
    kv             Interact with Vault's Key-Value storage
    lease          Interact with leases
    monitor        Stream log messages from a Vault server
    namespace      Interact with namespaces
    operator       Perform operator-specific tasks
    path-help      Retrieve API help for paths
    plugin         Interact with Vault plugins and catalog
    policy         Interact with policies
    print          Prints runtime configurations
    secrets        Interact with secrets engines
    ssh            Initiate an SSH session
    token          Interact with tokens
```
## 4.Создайте центр сертификации по инструкциии выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).
Устанавливаем дополнительный пакет jq, запускаем сервис, утанавливаем переменные.
```
sudo apt install jq
sudo systemctl start vault.service
smarzhic@websrv:~$ sudo systemctl status vault.service
● vault.service - "HashiCorp Vault - A tool for managing secrets"
     Loaded: loaded (/lib/systemd/system/vault.service; disabled; vendor preset: enabled)
     Active: active (running) since Sun 2021-12-26 02:01:18 MSK; 16s ago
       Docs: https://www.vaultproject.io/docs/
   Main PID: 29817 (vault)
      Tasks: 7 (limit: 1071)
     Memory: 53.8M
     CGroup: /system.slice/vault.service
             └─29817 /usr/bin/vault server -config=/etc/vault.d/vault.hcl

Dec 26 02:01:19 websrv vault[29817]:                Log Level: info
Dec 26 02:01:19 websrv vault[29817]:                    Mlock: supported: true, enabled: true
Dec 26 02:01:19 websrv vault[29817]:            Recovery Mode: false
Dec 26 02:01:19 websrv vault[29817]:                  Storage: file
Dec 26 02:01:19 websrv vault[29817]:                  Version: Vault v1.9.2
Dec 26 02:01:19 websrv vault[29817]:              Version Sha: f4c6d873e2767c0d6853b5d9ffc77b0d297bfbdf[m
Dec 26 02:01:19 websrv vault[29817]: ==> Vault server started! Log data will stream in below:
```
