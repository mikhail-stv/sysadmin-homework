1. Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.
  
![](https://github.com/mikhail-stv/sysadmin-homework/blob/main/bitwarden.jpg)
  
2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.
  
Установил, настроил
  
![](https://github.com/mikhail-stv/sysadmin-homework/blob/main/google%20auth.png)
  
3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.
- установка apache2, проверка
```
vagrant@vagrant:~$ sudo apt install apache2
vagrant@vagrant:~$ sudo systemctl status apache2
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-02-08 08:34:36 UTC; 40s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 25084 (apache2)
      Tasks: 55 (limit: 2236)
     Memory: 5.1M
        CPU: 30ms
     CGroup: /system.slice/apache2.service
             ├─25084 /usr/sbin/apache2 -k start
             ├─25086 /usr/sbin/apache2 -k start
             └─25087 /usr/sbin/apache2 -k start

Feb 08 08:34:36 vagrant systemd[1]: Starting The Apache HTTP Server...
Feb 08 08:34:36 vagrant apachectl[25083]: AH00558: apache2: Could not reliably determine the serve>
Feb 08 08:34:36 vagrant systemd[1]: Started The Apache HTTP Server.
```
- генерация самоподписного сертификата
```
vagrant@vagrant:~$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
...
-----
Country Name (2 letter code) [AU]:RU
State or Province Name (full name) [Some-State]:Moscow
Locality Name (eg, city) []:Moscow
Organization Name (eg, company) [Internet Widgits Pty Ltd]:PPS
Organizational Unit Name (eg, section) []:IT department
Common Name (e.g. server FQDN or YOUR name) []:PPS   
Email Address []:it.mip-sz@yandex.ru
vagrant@vagrant:~$ sudo a2enmod ssl
vagrant@vagrant:~$ sudo a2enmod headers
vagrant@vagrant:~$ systemctl restart apache2
```
```
vagrant@vagrant:~$ cd /etc/apache2/sites-available/
vagrant@vagrant:/etc/apache2/sites-available$ sudo nano /etc/apache2/sites-available/172.16.10.57.conf
vagrant@vagrant:/etc/apache2/sites-available$ sudo mkdir /var/www/172.16.10.57
vagrant@vagrant:/etc/apache2/sites-available$ sudo nano /var/www/PPS/index.html
vagrant@vagrant:/etc/apache2/sites-available$ sudo a2ensite 172.16.10.57.conf
Enabling site 172.16.10.57.
To activate the new configuration, you need to run:
  systemctl reload apache2
vagrant@vagrant:/etc/apache2/sites-available$ systemctl reload apache2
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to reload 'apache2.service'.
Authenticating as: vagrant
Password: 
==== AUTHENTICATION COMPLETE ===
```
- запуск в браузере, проверка сертификата
![](https://github.com/mikhail-stv/sysadmin-homework/blob/main/Snapshot_2023-02-08_12-34-25.png)  
    
4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).

5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.

6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.

7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.
