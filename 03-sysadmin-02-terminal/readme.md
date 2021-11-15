# Домашнее "3.2. Работа в терминале, лекция 2"
1. Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.
> Это встроенная команда Bash меняющая текущую папку только для той оболочки, в которой выполняется. Если использовать внешний вызов, то он будет работать со своим окружением, и менять  текущий каталог внутри своего окружения, а на вызвавший shell влиять не будет
2. Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l?
> vagrant@vagrant:~$cat >test_bash  
> asdfdasfg  
>adsfasdf  
> 123  
> vagrant@vagrant:~$grep 123 test_bash -c  
> 1  
> vagrant@vagrant:~$grep 123 test_bash |wc -l  
> 1  
3. Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?
> Корневым является процесс systemd  
>![PID 1](https://github.com/Smarzhic/netology/blob/main/03-sysadmin-02-terminal/1.JPG)
4. Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?
> Вызов из pts/0:  
> vagrant@vagrant:~$ ls -l \root 2>/dev/pts/1  
> vagrant@vagrant:~$
> 
>   Вывод в другой сессии pts/1:
>   
> vagrant@vagrant:~$ who  
> vagrant  pts/0        2021-11-15 17:47 (10.0.2.2)  
> vagrant  pts/1        2021-11-15 17:47 (10.0.2.2)  
> vagrant@vagrant:~$ ls: cannot access 'root': No such file or directory  
5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.
> vagrant@vagrant:~$ cat > test_bash  
> asdfasdf  
> asdgffdg  
> 123456  
> vagrant@vagrant:~$ cat test_bash_out  
> cat: test_bash_out: No such file or directory  
> vagrant@vagrant:~$ cat <test_bash >test_bash_out   
> vagrant@vagrant:~$ cat test_bash_out  
> asdfasdf  
> asdgffdg  
> 123456  
6. Получится ли находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?
> Да, путем перенаправление вывода из tty3 при работе на гостевой ОС:  
> vagrant@vagrant:~$ echo test > /dev/pts/0  
> test  
> vagrant@vagrant:~$  
7. Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?
> bash 5>&1 - Создаст дескриптор с 5 и перенатправит его в stdout  
> echo netology > /proc/$$/fd/5 - выведет в дескриптор "5", который был пернеаправлен в stdout  
> vagrant@vagrant:~$ bash 5>&1  
> vagrant@vagrant:~$ echo netology > /proc/$$/fd/5  
> netology
8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty?  
> vagrant@vagrant:~$ ls -l /root 9>&2 2>&1 1>&9 |grep denied -c  
> 1  
> vagrant@vagrant:~$  
> 9>&2 - новый дескриптор перенаправили в stderr  
> 2>&1 - stderr перенаправили в stdout  
> 1>&9 - stdout - перенаправили в в новый дескриптор  
9. Что выведет команда cat /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?
> Команда выводит переменные окружения.
> Аналогичный вовод с делением по переменным (только с разделением по переменным по строкам):
> printenv
10. Используя man, опишите что доступно по адресам /proc/<PID>/cmdline, /proc/<PID>/exe
> /proc/<PID>/cmdline - полный путь до исполняемого файла процесса [PID]  (строка 231)  
> /proc/<PID>/exe - содержит ссылку до файла запущенного для процесса [PID]  
11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo
> SSE 4.2  
> grep sse /proc/cpuinfo1
12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. Это можно подтвердить командой tty, которая упоминалась в лекции 3.2
> при подключении ожидается пользователь, а не другой процесс, отсутствует локальный tty в данный момент. Для запуска можно добавить -t - , и команда выполнится c принудительным созданием псевдотерминала
13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись reptyr
> Порядок переноса процесса в другую сессию:  
> Start a long running process, e.g. top  
> Background the process with CTRL-Z  
> Resume the process in the background: bg  
> Display your running background jobs with jobs -l, this should look like this:  
> [1]+ 4711 Stopped (signal) top  
> (The -l in jobs -l makes sure you'll get the PID)  
> Disown the jobs from the current parent with disown top. After that, jobs will not show the job any more, but ps -a will.  
> Start your terminal multiplexer of choice, e.g. tmux  
> Reattach to the backgrounded process: reptyr 4711  
> Detach your terminal multiplexer (e.g. CTRL-A D) and close ssh  
> Reconnect ssh, attach to your multiplexer (e.g. tmux attach), rejoice!  
14. sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без sudo под вашим пользователем. Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file. Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать.
> команда tee делает вывод одновременно и в файл, указаный в качестве параметра, и в stdout, в данном примере команда получает вывод из stdin, перенаправленный через pipe от stdout команды echo и так как команда запущена от sudo , соотвественно имеет права на запись в файл.
