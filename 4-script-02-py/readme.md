# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"

## Обязательная задача 1

Есть скрипт:
```python
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

### Вопросы:
| Вопрос  | Ответ |
| ------------- | ------------- |
| Какое значение будет присвоено переменной `c`?  |TypeError: unsupported operand type(s) for +: 'int' and 'str' Нельзя складывать строки и числа  |
| Как получить для переменной `c` значение 12?  |a = str("1") задать для a строковый тип |
| Как получить для переменной `c` значение 3?  | b = 2 задать для b числовой тип |

## Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os

bash_command = ["cd ~/devops3-netology", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
# is_change = False - лишняя переменная
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified: ', '').strip() #отрезает пробелы по краям строки
        print(prepare_result)
#        break - следует убрать что бы скрипт не прерывался при первом вхождении

```

### Вывод скрипта при запуске при тестировании:
```
/home/vagrant/devops/README.md
/home/vagrant/devops/1.txt
```

## Обязательная задача 3
1. Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import sys

a = os.getcwd()

if len(sys.argv)>=2:
    a = sys.argv[1]
bash_command = ["cd "+a, "git status 2>&1"]

print('\033[31m')
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('fatal') != -1:
        print('\033[31m Каталог \033[1m '+cmd+'\033[0m\033[31m не является GIT репозиторием\033[0m')    
    if result.find('изменено') != -1:
        prepare_result = result.replace('\tmodified: ', '').strip()
        print(cmd+prepare_result)
#        break
print('\033[0m')
```

### Вывод скрипта при запуске при тестировании:
```
???
```
