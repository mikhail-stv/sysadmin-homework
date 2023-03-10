## Задание 1

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
| Какое значение будет присвоено переменной `c`?  | TypeError: unsupported operand type(s) for +: 'int' and 'str'  |
| Как получить для переменной `c` значение 12?  | c = str(a) + b |
| Как получить для переменной `c` значение 3?  | c = a + int(b) |

------

## Задание 2

Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. 

Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?

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

bash_command = ["cd ~/Git/devops-netology", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
#is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:      ', '')
        print(os.getcwd() ,'/' , prepare_result, sep='')
        #break
```

### Вывод скрипта при запуске при тестировании:
```
mike@sky:~$ ./test.py
/home/mike/	modified:   has_been_moved
```

------

## Задание 3

Доработать скрипт выше так, чтобы он не только мог проверять локальный репозиторий в текущей директории, но и умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными репозиториями.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import os
import sys

path=os.getcwd()
if len(sys.argv)!=1:
    path=sys.argv[1]
bash_command = [f'cd {path}', 'git status 2>&1']
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('fatal') != -1:
        print('Каталог не является GIT репозиторием')
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:      ', '')
        print(os.getcwd() ,'/' , prepare_result, sep='')
```

### Вывод скрипта при запуске при тестировании:
```
mike@sky:~/Git/devops-netology$ ./test.py
/home/mike/Git/devops-netology/	modified:   has_been_moved
/home/mike/Git/devops-netology/	modified:   terraform/README.md
/home/mike/Git/devops-netology/	modified:   test.py
mike@sky:~/Git/devops-netology$ ./test.py ~/Git/devops-netology/terraform/
/home/mike/Git/devops-netology/	modified:   ../has_been_moved
/home/mike/Git/devops-netology/	modified:   README.md
/home/mike/Git/devops-netology/	modified:   ../test.py
mike@sky:~/Git/devops-netology$ ./test.py ~/Git
Каталог не является GIT репозиторием
```

------

## Задание 4

Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. 

Проблема в том, что отдел, занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. 

Мы хотим написать скрипт, который: 
- опрашивает веб-сервисы, 
- получает их IP, 
- выводит информацию в стандартный вывод в виде: <URL сервиса> - <его IP>. 

Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением: [ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: `drive.google.com`, `mail.google.com`, `google.com`.

### Ваш скрипт:
```python
#!/usr/bin/env python3

import socket
import time

hosts = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
while 1 == 1 :
  for host in hosts :
    ip = socket.gethostbyname(host)
    if ip != hosts[host] :
      print(' [ERROR] ' + str(host) +' IP mistmatch: '+hosts[host]+' '+ip)
      hosts[host]=ip
    else :
        print(str(host) + ' ' + ip)
    time.sleep(2)
```

### Вывод скрипта при запуске при тестировании:
```
[ERROR] drive.google.com IP mistmatch: 0.0.0.0 64.233.165.194
 [ERROR] mail.google.com IP mistmatch: 0.0.0.0 64.233.161.83
 [ERROR] google.com IP mistmatch: 0.0.0.0 209.85.233.138
drive.google.com 64.233.165.194
mail.google.com 64.233.161.83
 [ERROR] google.com IP mistmatch: 209.85.233.138 209.85.233.102
drive.google.com 64.233.165.194
 [ERROR] mail.google.com IP mistmatch: 64.233.161.83 64.233.161.19
 [ERROR] google.com IP mistmatch: 209.85.233.102 209.85.233.101
drive.google.com 64.233.165.194
 [ERROR] mail.google.com IP mistmatch: 64.233.161.19 64.233.161.83
google.com 209.85.233.101
drive.google.com 64.233.165.194
 [ERROR] mail.google.com IP mistmatch: 64.233.161.83 64.233.161.17
 [ERROR] google.com IP mistmatch: 209.85.233.101 209.85.233.113
drive.google.com 64.233.165.194
```
