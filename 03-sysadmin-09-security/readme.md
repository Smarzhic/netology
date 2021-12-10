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
4. Проверьте на TLS уязвимости произвольный сайт в интернете
```
 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK)
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           supported (OK)
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    potentially NOT ok, "gzip" HTTP compression detected. - only supplied "/" tested
                                           Can be ignored for static pages or if no secrets in the page
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              No fallback possible (OK), no protocol below TLS 1.2 offered
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    not vulnerable (OK)
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services
                                           https://censys.io/ipv4?q=BF97DC1B282F042C2EFFF358BF5B28AEACE104A0C2BD84D582E246C1A5C8D40C could help you to find out
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no DH key detected with <= TLS 1.2
 BEAST (CVE-2011-3389)                     not vulnerable (OK), no SSL3 or TLS1
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK)
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2021-12-08 18:18:21 [  46s] -->> 31.31.196.60:443 (www.globus-stal.ru) <<--
 ```
5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу
```
smarzhic@netology:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/smarzhic/.ssh/id_rsa):
Created directory '/home/smarzhic/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/smarzhic/.ssh/id_rsa
Your public key has been saved in /home/smarzhic/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:Cc3AYRAXnY9273OK7ySK+bN08pyu7icV6LCXhmbk2xU smarzhic@netology
The key's randomart image is:
+---[RSA 3072]----+
|    o+*+ .       |
|     o.+o        |
|      . o+       |
|      o.+.E      |
|     o *So +     |
|      * = o .    |
|     o =ooo..    |
|      .++*.=o .  |
|      o+BB*o++   |
+----[SHA256]-----+
smarzhic@netology:~$ ssh-copy-id smarzhic@192.168.1.60
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/smarzhic/.ssh/id_rsa.pub"
The authenticity of host '192.168.1.60 (192.168.1.60)' can't be established.
ECDSA key fingerprint is SHA256:4algMGB1pzwdUdHCNHZgrqhtNdwoGo8PmSDpZY5sCdk.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
smarzhic@192.168.1.60's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'smarzhic@192.168.1.60'"
and check to make sure that only the key(s) you wanted were added.

smarzhic@netology:~$ ssh 192.168.1.60
Enter passphrase for key '/home/smarzhic/.ssh/id_rsa':
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.11.0-40-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

39 updates can be applied immediately.
22 of these updates are standard security updates.
Чтобы просмотреть дополнительные обновления выполните: apt list --upgradable

Your Hardware Enablement Stack (HWE) is supported until April 2025.
smarzhic@matrosov:~$
```
