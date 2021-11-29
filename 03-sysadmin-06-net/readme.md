# Домашнее задание "3.6. Компьютерные сети, лекция 1"
1. Работа c HTTP через телнет.
```
HTTP/1.1 301 Moved Permanently
cache-control: no-cache, no-store, must-revalidate
location: https://stackoverflow.com/questions
x-request-guid: 58db06b7-9dbf-44ec-9e51-885855c0447f
feature-policy: microphone 'none'; speaker 'none'
content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com
Accept-Ranges: bytes
Date: Sun, 28 Nov 2021 14:02:47 GMT
Via: 1.1 varnish
Connection: close
X-Served-By: cache-fra19162-FRA
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1638108168.812980,VS0,VE92
Vary: Fastly-SSL
X-DNS-Prefetch-Control: off
Set-Cookie: prov=d6313e90-4eba-092f-e7e4-f0dd1f88a0fc; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly
```
2. Повторите задание 1 в браузере, используя консоль разработчика F12.
```
Request URL: https://stackoverflow.com/
Request Method: GET
Status Code: 200 
Remote Address: 151.101.65.69:443
Referrer Policy: no-referrer-when-downgrade
```
>![PID 1](https://github.com/Smarzhic/netology/blob/main/03-sysadmin-06-net/2.JPG)
3. Какой IP адрес у вас в интернете?
```
217.107.125.105
```
4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой whois  
```
Провайдер - Ростелеком
smarzhic@matrosov:~$ whois -h whois.radb.net 217.107.125.105
route:          217.107.125.0/24
descr:          ROSTELECOM NETS
origin:         AS12389
mnt-by:         ROSTELECOM-MNT
created:        2017-04-18T07:39:30Z
last-modified:  2017-04-18T07:39:30Z
source:         RIPE
remarks:        ****************************
remarks:        * THIS OBJECT IS MODIFIED
remarks:        * Please note that all data that is generally regarded as personal
remarks:        * data has been removed from this object.
remarks:        * To view the original object, please query the RIPE Database at:
remarks:        * http://www.ripe.net/whois
```
5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой traceroute
```
vagrant@vagrant:~$ traceroute -AnI 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  10.0.2.2 [*]  0.150 ms  0.092 ms  0.077 ms
 2  192.168.1.1 [*]  1.940 ms  1.925 ms  1.910 ms
 3  100.64.0.205 [*]  8.424 ms  8.409 ms  9.711 ms
 4  100.64.0.204 [*]  8.009 ms  8.151 ms  8.201 ms
 5  188.254.26.74 [AS12389]  9.631 ms  9.754 ms  9.729 ms
 6  188.254.26.75 [AS12389]  9.749 ms  9.237 ms  9.349 ms
 7  * * *
 8  72.14.205.132 [AS15169]  11.259 ms  11.462 ms  12.245 ms
 9  108.170.250.129 [AS15169]  12.213 ms  12.180 ms  12.169 ms
10  108.170.250.130 [AS15169]  12.144 ms  12.123 ms  12.111 ms
11  209.85.255.136 [AS15169]  28.303 ms  28.380 ms  28.498 ms
12  108.170.235.64 [AS15169]  28.469 ms  27.587 ms  28.075 ms
13  172.253.79.169 [AS15169]  26.881 ms  26.595 ms  26.576 ms
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  8.8.8.8 [AS15169]  26.832 ms  26.966 ms  24.093 ms
```
```
smarzhic@matrosov:~$ traceroute  8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 64 hops max
  1   192.168.1.1  0,693ms  0,316ms  0,283ms 
  2   100.64.0.205  5,005ms  9,449ms  5,348ms 
  3   100.64.0.204  9,591ms  7,529ms  7,359ms 
  4   188.254.26.74  8,535ms  8,505ms  8,558ms 
  5   188.254.26.75  8,711ms  8,759ms  8,873ms 
  6   87.226.183.89  9,984ms  9,938ms  9,966ms 
  7   5.143.253.105  9,848ms  9,634ms  9,752ms 
  8   *  108.170.250.51  9,952ms  10,152ms 
  9   142.250.239.64  24,516ms  22,068ms  24,943ms 
 10   216.239.43.20  25,383ms  25,387ms  25,114ms 
 11   142.250.236.77  22,172ms  21,947ms  22,106ms 
 12   *  *  * 
 13   *  *  * 
 14   *  *  * 
 15   *  *  * 
 16   *  *  * 
 17   *  *  * 
 18   *  *  * 
 19   *  *  * 
 20   *  *  * 
 21   8.8.8.8  27,025ms  26,904ms  27,008ms 
 ```
6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?
> Самая большая задержка на узле 108.170.235.64
>![PID 1](https://github.com/Smarzhic/netology/blob/main/03-sysadmin-06-net/mtr.JPG)
7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? воспользуйтесь утилитой dig
```
dig +trace @8.8.8.8 dns.google
dns.google.		900	IN	A	8.8.8.8
dns.google.		900	IN	A	8.8.4.4
```
8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? воспользуйтесь утилитой dig
```
;; ANSWER SECTION:
8.8.8.8.in-addr.arpa.	5684	IN	PTR	dns.google.
;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.	86400	IN	PTR	dns.google.
```
