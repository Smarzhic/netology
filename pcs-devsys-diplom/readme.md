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
sudo ufw allow in on lo to any
sudo ufw allow out on lo to any
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
Anywhere on lo             ALLOW IN    Anywhere
22/tcp (OpenSSH (v6))      ALLOW IN    Anywhere (v6)
443 (v6)                   ALLOW IN    Anywhere (v6)
Anywhere (v6) on lo        ALLOW IN    Anywhere (v6)

Anywhere                   ALLOW OUT   Anywhere on lo
Anywhere (v6)              ALLOW OUT   Anywhere (v6) on lo
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
systemctl enable vault --now
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
Настройка рабочего окружения. Раскоментируем и отредактируем следующие строки
```
vi /etc/vault.d/vault.hcl
listener "tcp" {
  address = "127.0.0.1:8201"
  tls_disable = 1
}
```
```
export VAULT_ADDR=http://127.0.0.1:8201
```
```
smarzhic@websrv:~$ vault status
Key                Value
---                -----
Seal Type          shamir
Initialized        false
Sealed             true
Total Shares       0
Threshold          0
Unseal Progress    0/0
Unseal Nonce       n/a
Version            1.9.2
Storage Type       file
HA Enabled         false
````
### Инициализируем новый Vault
```
smarzhic@websrv:~$ vault operator init -key-shares=3 -key-threshold=2
Unseal Key 1: Zx5yPxzJXT+pI4aYsbnCdlf9i+Xe/Yirz2KQpQAVHzEJ
Unseal Key 2: gEW+xZ+Hfe2eYQQ465kIHwgo2RR4qSwoLzyv8s+q5iDp
Unseal Key 3: BWHlNymujpbzD3YqC2r9rxE5Q8vEnQOewOG4kcNcQxAG

Initial Root Token: s.l7pwcXDAyouEnkdcx0tg8DWe

Vault initialized with 3 key shares and a key threshold of 2. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 2 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated master key. Without at least 2 keys to
reconstruct the master key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.
```
### Подключаемся к новому Vault.
```
smarzhic@websrv:~$ vault operator unseal Zx5yPxzJXT+pI4aYsbnCdlf9i+Xe/Yirz2KQpQAVHzEJ
smarzhic@websrv:~$ vault operator unseal BWHlNymujpbzD3YqC2r9rxE5Q8vEnQOewOG4kcNcQxAG
smarzhic@websrv:~$ vault login s.l7pwcXDAyouEnkdcx0tg8DWe
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                s.l7pwcXDAyouEnkdcx0tg8DWe
token_accessor       9zJqcGRuAIelH1eR0PeI8eIa
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]
```
### Содаем ключ и сертификат для центра сертификации.
```
vault secrets enable pki
vault secrets tune -max-lease-ttl=87600h pki
vault write -field=certificate pki/root/generate/internal common_name=kurs.dev ttl=87600 > CA_cert.crt
vault write pki/config/urls issuing_certificates="$VAULT_ADDR/v1/pki/ca" crl_distribution_points="$VAULT_ADDR/v1/pki/crl"
```
### Создаем промежуточный сертификат.
```
vault secrets enable -path=pki_int pki
vault secrets tune -max-lease-ttl=43800h pki_int
vault write -format=json pki_int/intermediate/generate/internal common_name="kurs.dev Intermediate Authority" | jq -r '.data.csr' > pki_intermediate.csr
vault write -format=json pki/root/sign-intermediate csr=@pki_intermediate.csr format=pem_bundle ttl="43800h"| jq -r '.data.certificate' > intermediate.cert.pem
vault write pki_int/intermediate/set-signed certificate=@intermediate.cert.pem
```
### Создаем роль серверу
```
vault write pki_int/roles/project-dot-devel allowed_domains="kurs.dev" allow_bare_domains=true allow_subdomains=true max_ttl="720h"
```
### Генерируем сертификат для домена на месяц.
```
vault write -format=json pki_int/issue/project-dot-devel common_name="kurs.dev" ttl="720h" > project.devel.raw.json
```
## Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.
```
smarzhic@websrv:~$ sudo mv CA_cert.crt /usr/local/share/ca-certificates/CA_cert.crt
[sudo] password for smarzhic:
smarzhic@websrv:~$ sudo update-ca-certificates
Updating certificates in /etc/ssl/certs...
1 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
```
## Установите nginx.
```
sudo apt install nginx
smarzhic@websrv:~$ systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2021-12-27 13:08:05 MSK; 31s ago
       Docs: man:nginx(8)
   Main PID: 27296 (nginx)
      Tasks: 2 (limit: 1071)
     Memory: 4.9M
     CGroup: /system.slice/nginx.service
             ├─27296 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
             └─27297 nginx: worker process

Dec 27 13:08:05 websrv systemd[1]: Starting A high performance web server and a reverse proxy server...
Dec 27 13:08:05 websrv systemd[1]: Started A high performance web server and a reverse proxy server.
```
## Настройте nginx на https, используя ранее подготовленный сертификат:
Настраиваем nginx на использование SSL
```
server {
        listen 443 ssl;

        root /var/www/html;
        index index.html;

        server_name kurs.dev;

        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers         HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;

        ssl_certificate     /etc/ssl/certs/kurs.dev.crt;
        ssl_certificate_key /etc/ssl/private/kurs.dev.key;

        location / {
                try_files $uri $uri/ =404;
        }
}
```
Создаем цепочку сертификатов
```
sudo cat project.devel.raw.json | jq -r '.data.certificate' > /etc/ssl/certs/kurs.dev.crt
sudo cat project.devel.raw.json | jq -r '.data.ca_chain[]' >> /etc/ssl/certs/kurs.dev.crt
sudo cat project.devel.raw.json | jq -r '.data.private_key' > /etc/ssl/private/kurs.dev.key
```
Перезагружаем сервис nginx что бы применились изменения
```
systemctl reload nginx
```
### Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx.
![PID 1](https://github.com/Smarzhic/netology/blob/main/pcs-devsys-diplom/cert.JPG)  

### Создайте скрипт, который будет генерировать новый сертификат в vault:
```bash

#!/usr/bin/env bash

#Start HashiCorp vault.service
systemctl start vault.service
sleep 10

#Open vault
export VAULT_ADDR='http://127.0.0.1:8201'
vault operator unseal Zx5yPxzJXT+pI4aYsbnCdlf9i+Xe/Yirz2KQpQAVHzEJ
vault operator unseal BWHlNymujpbzD3YqC2r9rxE5Q8vEnQOewOG4kcNcQxAG
vault login s.l7pwcXDAyouEnkdcx0tg8DWe

#Generate new certificate
TEMP_DATA=$(vault write -format=json pki_int/issue/project-dot-devel common_name="kurs.dev" ttl="720h")
jq -r '.data.certificate' <<< "$TEMP_DATA" > /etc/ssl/certs/kurs.dev.crt
jq -r '.data.ca_chain[]' <<< "$TEMP_DATA" >> /etc/ssl/certs/kurs.dev.crt
jq -r '.data.private_key' <<< "$TEMP_DATA" > /etc/ssl/private/kurs.dev.key

#Stop HashiCorp vault.service
systemctl stop vault.service

# restart nginx
systemctl reload nginx
```
### Зададим права доступа
chmod 755 update_crt.sh

### Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время.
```
49 21 * * * /root/script/update_crt.sh 
```
Настраиваем обновление сертификата на 1 чаc, 10 минут, 1 числа, каждого месяца.
```
10 1 1 * * /root/script/update_crt.sh
```
