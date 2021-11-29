# Домашнее задание "3.4. Операционные системы, лекция 2"
1. На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:
Установлен и запущен Node_exporter
>![PID 1](https://github.com/Smarzhic/netology/blob/main/03-sysadmin-04-os/1.JPG)
```
vagrant@vagrant:~$ ps -e |grep node_exporter
1424 ?        00:00:00 node_exporter
vagrant@vagrant:~$ systemctl stop node_exporter
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to stop 'node_exporter.service'.
Authenticating as: vagrant,,, (vagrant)
Password:
==== AUTHENTICATION COMPLETE ===
vagrant@vagrant:~$ ps -e |grep node_exporter
vagrant@vagrant:~$ systemctl start node_exporter
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to start 'node_exporter.service'.
Authenticating as: vagrant,,, (vagrant)
Password:
==== AUTHENTICATION COMPLETE ===
vagrant@vagrant:~$ ps -e |grep node_exporter
1488 ?        00:00:00 node_exporter
vagrant@vagrant:~$
```
 unit-файл
```
[Unit]
Description=Prometheus Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter  $ONE $TWO $THREE
EnvironmentFile=-/etc/default/cron
[Install]
WantedBy=multi-user.target  


В ExecStart передыны переменные $ONE $TWO $THREE. 
```
2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
> CPU  
> node_cpu_seconds_total{cpu="0",mode="idle"} 4611.59  
> node_cpu_seconds_total{cpu="0",mode="system"} 3.42  
> node_cpu_seconds_total{cpu="0",mode="user"} 3.6  
> Memory:  
> node_memory_MemAvailable_bytes  
> node_memory_MemFree_bytes  
> Disk - для каждого диска в системе  
> node_disk_io_time_seconds_total{device="sda"}  
> node_disk_read_bytes_total{device="sda"}  
> node_disk_read_time_seconds_total{device="sda"}  
> node_disk_write_time_seconds_total{device="sda"}  
> Network - для каждого адаптера  
> node_network_receive_errs_total{device="eth0"}  
> node_network_receive_bytes_total{device="eth0"}  
> node_network_transmit_bytes_total{device="eth0"}  
> node_network_transmit_errs_total{device="eth0"}  
3. Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata). После успешной установки:
>![PID 1](https://github.com/Smarzhic/netology/blob/main/03-sysadmin-04-os/2.JPG)
4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
> Да, можно  
> vagrant@vagrant:~$ dmesg |grep virtualiz  
> [    0.029719] CPU MTRRs all blank - virtualized system.  
> [    0.138646] Booting paravirtualized kernel on KVM  
> [    0.323679] Performance Events: PMU not available due to virtualization, using software events only.  
> [    5.033347] systemd[1]: Detected virtualization oracle.  
5. Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?
> Это максимальное число открытых дескрипторов для ядра.  
```
vagrant@vagrant:~$  /sbin/sysctl -n fs.nr_open
1048576
```
> Максимальное число открытых дескрипторов для ОС
```
vagrant@vagrant:~$  cat /proc/sys/fs/file-max
9223372036854775807
```
6. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.
```
root@vagrant:~# ps -e |grep sleep
   2057 pts/2    00:00:00 sleep
root@vagrant:~# nsenter --target 2057 --pid --mount
root@vagrant:/# ps
    PID TTY          TIME CMD
      2 pts/0    00:00:00 bash
     11 pts/0    00:00:00 p
`````
7. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
> Данный код называется "Вилочной бомбой"  
> :() означает, что вы определяете функцию под названием :  
> {:|: &}означает запустить функцию :и :снова отправить ее вывод в функцию и запустить ее в фоновом режиме.  
> Это ;разделитель команд.  
>  : запускает функцию в первый раз.  
> По сути, вы создаете функцию, которая вызывает себя дважды при каждом вызове и не имеет возможности завершить себя. Он будет удваиваться, пока у вас не закончатся системные ресурсы.

> Системы типа Unix обычно имеют ограничение процесса, управляемое командой оболочки ulimit или ее преемником setrlimit   
> Ядра Linux устанавливают и применяют ограничение  >RLIMIT_NPROC («ограничение ресурсов») процесса   
> Если процесс пытается выполнить ответвление, а пользователь, которому принадлежит этот процесс, уже владеет >RLIMIT_NPROCпроцессами, то ответвление завершается неудачно  
> Кроме того, в Linux или * BSD можно отредактировать pam_limitsфайл конфигурации /etc/security/limits.confс тем же ?>эффектом.  
> Однако не во всех дистрибутивах Linux pam_limitsмодуль установлен по умолчанию.
