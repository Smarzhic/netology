# Домашнее задание к занятию "Компьютерные сети, лекция 3"
1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP
```
route-views>show ip route 213.24.127.45 255.255.255.240
% Network not in table
route-views>show ip route 213.24.127.45
Routing entry for 213.24.126.0/23, supernet
  Known via "bgp 6447", distance 20, metric 0
  Tag 6939, type external
  Last update from 64.71.137.241 6d11h ago
  Routing Descriptor Blocks:
  * 64.71.137.241, from 64.71.137.241, 6d11h ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 6939
      MPLS label: none

route-views>show bgp 213.24.127.45/32
% Network not in table
route-views>show bgp 213.24.127.45 255.255.255.240
% Network not in table
route-views>show bgp 213.24.127.45
BGP routing table entry for 213.24.126.0/23, version 1388583815
Paths: (24 available, best #23, table default)
  Not advertised to any peer
  Refresh Epoch 1
  4901 6079 3257 12389
    162.250.137.254 from 162.250.137.254 (162.250.137.254)
      Origin IGP, localpref 100, valid, external
      Community: 65000:10100 65000:10300 65000:10400
      path 7FE15D2A5878 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 3
  3303 12389
    217.192.89.50 from 217.192.89.50 (138.187.128.158)
      Origin IGP, localpref 100, valid, external
      Community: 3303:1004 3303:1006 3303:1030 3303:3056
      path 7FE1189EC668 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  7660 2516 12389
    203.181.248.168 from 203.181.248.168 (203.181.248.168)
      Origin IGP, localpref 100, valid, external
      Community: 2516:1050 7660:9001
      path 7FE12A25B8E0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3267 1299 12389
    194.85.40.15 from 194.85.40.15 (185.141.126.1)
      Origin IGP, metric 0, localpref 100, valid, external
      path 7FE0C7AE6148 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  57866 3356 12389
 --More--
 ```
 2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.
 ```
 source-directory /etc/network/interfaces.d
auto dummy0
iface dummy0 inet static
    address 10.2.2.2/32
    pre-up ip link add dummy0 type dummy
    post-down ip link del dummy0
```
```
vagrant@vagrant:~$ sudo -i
root@vagrant:~# ip route add 172.16.10.0/24 dev eth0
root@vagrant:~# ip route add 172.16.10.0/24 dev eth0 metric 100
root@vagrant:~# ip -br route
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100
172.16.10.0/24 dev eth0 scope link
172.16.10.0/24 dev eth0 scope link metric 100
```
3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров
```
vagrant@vagrant:~$ ss -tl
State        Recv-Q       Send-Q             Local Address:Port               Peer Address:Port      Process
LISTEN       0            4096                     0.0.0.0:sunrpc                  0.0.0.0:*
LISTEN       0            4096               127.0.0.53%lo:domain                  0.0.0.0:*
LISTEN       0            128                      0.0.0.0:ssh                     0.0.0.0:*
LISTEN       0            4096                   127.0.0.1:8125                    0.0.0.0:*
LISTEN       0            4096                     0.0.0.0:19999                   0.0.0.0:*
LISTEN       0            4096                           *:9100                          *:*
LISTEN       0            4096                        [::]:sunrpc                     [::]:*
LISTEN       0            128                         [::]:ssh                        [::]:*
LISTEN       0            4096                       [::1]:8125                       [::]:*
```
4.Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?
```
vagrant@vagrant:~$ ss -lu
State        Recv-Q       Send-Q              Local Address:Port               Peer Address:Port      Process
UNCONN       0            0                       127.0.0.1:8125                    0.0.0.0:*
UNCONN       0            0                   127.0.0.53%lo:domain                  0.0.0.0:*
UNCONN       0            0                  10.0.2.15%eth0:bootpc                  0.0.0.0:*
UNCONN       0            0                         0.0.0.0:sunrpc                  0.0.0.0:*
UNCONN       0            0                           [::1]:8125                       [::]:*
UNCONN       0            0                            [::]:sunrpc                     [::]:*
vagrant@vagrant:~$
```
5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.


