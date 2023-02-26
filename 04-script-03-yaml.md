## Задание 1

Мы выгрузили JSON, который получили через API-запрос к нашему сервису:

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
  Нужно найти и исправить все ошибки, которые допускает наш сервис.

### Ваш скрипт:

```json
{
   "info":"Sample JSON output from our service\t",
   "elements":[
      {
         "name": "first",
         "type": "server",
         "ip": 7175
      },
      {
         "name": "second",
         "type": "proxy",
         "ip": "71.78.22.43"
      }
   ]
}
```

---

## Задание 2

В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML-файлов, описывающих наши сервисы. 

Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. 

Формат записи YAML по одному сервису: `- имя сервиса: его IP`. 

Если в момент исполнения скрипта меняется IP у сервиса — он должен так же поменяться в YAML и JSON-файле.

### Ваш скрипт:

```python
import socket
import time
import json
import yaml

# задаем словарь
service_addr = {
    'drive.google.com': '0',
    'mail.google.com': '0',
    'google.com': '0'
}

for item in service_addr:
    initial_addr = socket.gethostbyname(item)
    service_addr[item] = initial_addr
    with open(item + '.json', 'w') as output_json:
        data_json = json.dumps({item: service_addr[item]})
        output_json.write(data_json)

    with open(item + '.yaml', 'w') as output_yaml:
        data_yaml = yaml.dump([{item: service_addr[item]}])
        output_yaml.write(data_yaml)

while True:
    for item in service_addr:
        old_addr = service_addr[item]
        new_addr = socket.gethostbyname(item)
        if new_addr != old_addr:
            service_addr[item] = new_addr
            with open(item + '.json', 'w') as output_json:
                data_json = json.dumps({item: service_addr[item]})
                output_json.write(data_json)

            with open(item + '.yaml', 'w') as output_yaml:
                data_yaml = yaml.dump([{item: service_addr[item]}])
                output_yaml.write(data_yaml)
            print("[ERROR] " + item + " IP mismatch: old IP " + old_addr + ", new IP " + new_addr)
        print(item + " - " + service_addr[item])
    print("######################################")
    time.sleep(10)
```

### Вывод скрипта при запуске во время тестирования:

```
/usr/bin/python3.10 /home/mike/Documents/ip_test.py 
drive.google.com - 64.233.162.194
[ERROR] mail.google.com IP mismatch: old IP 64.233.165.17, new IP 64.233.165.18
mail.google.com - 64.233.165.18
[ERROR] google.com IP mismatch: old IP 74.125.205.102, new IP 74.125.205.139
google.com - 74.125.205.139
######################################
drive.google.com - 64.233.162.194
[ERROR] mail.google.com IP mismatch: old IP 64.233.165.18, new IP 64.233.165.17
mail.google.com - 64.233.165.17
[ERROR] google.com IP mismatch: old IP 74.125.205.139, new IP 74.125.205.102
google.com - 74.125.205.102
```

### JSON-файл(ы), который(е) записал ваш скрипт:

```json
{"drive.google.com": "64.233.162.194"}
```
```json
{"google.com": "74.125.205.138"}
```
```json
{"mail.google.com": "64.233.165.17"}
```

### YAML-файл(ы), который(е) записал ваш скрипт:

```yaml
- drive.google.com: 64.233.162.194
```
```yaml
- google.com: 74.125.205.138
```
```yaml
- mail.google.com: 64.233.165.17
```
