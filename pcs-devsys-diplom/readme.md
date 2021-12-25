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
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
echo "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main" > /etc/apt/sources.list.d/hashicorp.list
sudo apt-get update
snap install vault
```
Далее разрешаем автозапуск службы и если она не запущена, запускаем
```
systemctl enable vault --now
```
