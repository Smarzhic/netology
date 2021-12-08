1. Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей
>![1](https://github.com/Smarzhic/netology/blob/main/03-sysadmin-09-security/1.JPG)
2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.
>![1](https://github.com/Smarzhic/netology/blob/main/03-sysadmin-09-security/2.JPG)
3.Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.
```
smarzhic@netology:~$ sudo apt install apache2sudo 
.....
smarzhic@netology:~$ sudo a2enmod ssl
.....
smarzhic@netology:~$ sudo systemctl restart apache2
smarzhic@netology:~$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
> -keyout /etc/ssl/private/apache-selfsigned.key \
> -out /etc/ssl/certs/apache-selfsigned.crt \
> -subj "/C=RU/ST=Moscow/L=Moscow/O=Company Name/OU=Org/CN=www.homepc.com"
[sudo] password for smarzhic:
Generating a RSA private key
..........................................+++++
.......................................................................................+++++
writing new private key to '/etc/ssl/private/apache-selfsigned.key'
```
>![1](https://github.com/Smarzhic/netology/blob/main/03-sysadmin-09-security/3.JPG)

