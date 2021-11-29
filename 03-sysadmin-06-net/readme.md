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
> В системе Ubuntu 20.04  не смог найти аналогов ключей -An
```
smarzhic@matrosov:~$ traceroute --help
Usage: traceroute [OPTION...] HOST
Print the route packets trace to network host.

  -f, --first-hop=NUM        set initial hop distance, i.e., time-to-live
  -g, --gateways=GATES       list of gateways for loose source routing
  -I, --icmp                 use ICMP ECHO as probe
  -m, --max-hop=NUM          set maximal hop count (default: 64)
  -M, --type=METHOD          use METHOD (`icmp' or `udp') for traceroute
                             operations, defaulting to `udp'
  -p, --port=PORT            use destination PORT port (default: 33434)
  -q, --tries=NUM            send NUM probe packets per hop (default: 3)
      --resolve-hostnames    resolve hostnames
  -t, --tos=NUM              set type of service (TOS) to NUM
  -w, --wait=NUM             wait NUM seconds for response (default: 3)
  -?, --help                 give this help list
      --usage                give a short usage message
  -V, --version              print program version

Mandatory or optional arguments to long options are also mandatory or optional
for any corresponding short options.

Report bugs to <bug-inetutils@gnu.org>.
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
