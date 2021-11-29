# Домашнее задание "3.6. Компьютерные сети, лекция 1"
1.Работа c HTTP через телнет.
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
