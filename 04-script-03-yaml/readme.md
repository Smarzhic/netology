## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис
```json
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"
            }
        ]
    }
```
## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket
import json
import yaml

i = 1
wait = 2
serv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init=0
fpath = "C:/Users/Smarzhic/Desktop/DevOps/" # путь к файлам json и yaml

print("Проверка IP")

while 1:
  for host in serv:
    ip = socket.gethostbyname(host)
    if ip != serv[host]:
      if i==True and init !=1:
        is_error = True
        with open(fpath+host+".json",'w') as jsf:
          json_data= json.dumps({host:ip})
          jsf.write(json_data)
        with open(fpath + host + ".yaml", 'w') as ymf:
          yaml_data = yaml.dump([{host: ip}])
          ymf.write(yaml_data)
      serv[host]=ip
```

### Вывод скрипта при запуске при тестировании:
```
Проверка IP
```

### json-файл(ы), который(е) записал ваш скрипт:  
Создано 3 файла
```json
{"drive.google.com": "64.233.165.194"}
{"google.com": "74.125.205.139"}
{"mail.google.com": "64.233.165.18"}
```

### yml-файл(ы), который(е) записал ваш скрипт:  
Создано 3 файла
```yaml
- drive.google.com: 64.233.165.194
- google.com: 74.125.205.139
- mail.google.com: 64.233.165.18
```

