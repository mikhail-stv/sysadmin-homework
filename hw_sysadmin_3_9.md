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
```
vagrant@vagrant:~$ git clone --depth 1 https://github.com/drwetter/testssl.sh.git
Cloning into 'testssl.sh'...
remote: Enumerating objects: 104, done.
remote: Counting objects: 100% (104/104), done.
remote: Compressing objects: 100% (100/100), done.
remote: Total 104 (delta 14), reused 31 (delta 4), pack-reused 0
Receiving objects: 100% (104/104), 8.70 MiB | 6.48 MiB/s, done.
Resolving deltas: 100% (14/14), done.
vagrant@vagrant:~$ cd testssl.sh
vagrant@vagrant:~/testssl.sh$ ./testssl.sh -U --sneaky https://www.losst.pro/

###########################################################
    testssl.sh       3.2rc2 from https://testssl.sh/dev/
    (8260ca1 2023-02-07 12:23:04)

      This program is free software. Distribution and
             modification under GPLv2 permitted.
      USAGE w/o ANY WARRANTY. USE IT AT YOUR OWN RISK!

       Please file bugs @ https://testssl.sh/bugs/

###########################################################

 Using "OpenSSL 1.0.2-bad (1.0.2k-dev)" [~179 ciphers]
 on vagrant:./bin/openssl.Linux.x86_64
 (built: "Sep  1 14:03:44 2022", platform: "linux-x86_64")


 Start 2023-02-08 13:11:09        -->> 5.45.95.135:443 (www.losst.pro) <<--

 rDNS (5.45.95.135):     --
 Service detected:       HTTP


 Testing vulnerabilities 

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK), no session ticket extension
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           supported (OK)
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    no gzip/deflate/compress/br HTTP compression (OK)  - only supplied "/" tested
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              No fallback possible (OK), no protocol below TLS 1.2 offered
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    not vulnerable (OK)
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services, see
                                           https://search.censys.io/search?resource=hosts&virtual_hosts=INCLUDE&q=4E23A632F88F89C4C45BB60588B02B397D92F121167A48F7DB97C0E039D55ED9
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no common prime detected
 BEAST (CVE-2011-3389)                     not vulnerable (OK), no SSL3 or TLS1
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK)
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2023-02-08 13:11:43 [  38s] -->> 5.45.95.135:443 (www.losst.pro) <<--
```
  
5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.  
  
```
vagrant@vagrant:~$ systemctl status sshd.service
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-02-08 07:42:25 UTC; 5h 32min ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 13944 (sshd)
      Tasks: 1 (limit: 2236)
     Memory: 1.7M
        CPU: 24ms
     CGroup: /system.slice/ssh.service
             └─13944 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

Feb 08 07:42:25 vagrant systemd[1]: Starting OpenBSD Secure Shell server...
Feb 08 07:42:25 vagrant sshd[13944]: Server listening on 0.0.0.0 port 22.
Feb 08 07:42:25 vagrant sshd[13944]: Server listening on :: port 22.
Feb 08 07:42:25 vagrant systemd[1]: Started OpenBSD Secure Shell server.
vagrant@vagrant:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/vagrant/.ssh/id_rsa
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:h8zSXePibvAk/p46dfAHS5TdHHcOk5M1dwp+TE/MS8A vagrant@vagrant
The key's randomart image is:
+---[RSA 3072]----+
|            o+=@O|
|           .oE=O@|
|           .+ =.+|
|       + o.ooo . |
|      . S ++.o   |
|       .ooo.+ .  |
|       . *.. .   |
|        o.o.     |
|        .*=      |
+----[SHA256]-----+
vagrant@vagrant:~$ ssh-copy-id vagrant@PPS
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed

/usr/bin/ssh-copy-id: ERROR: ssh: Could not resolve hostname pps: Temporary failure in name resolution

vagrant@vagrant:~$ ssh-copy-id vagrant@172.16.10.57
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
The authenticity of host '172.16.10.57 (172.16.10.57)' can't be established.
ED25519 key fingerprint is SHA256:YIMwX/c+MUDlSf4AmSNx7dTm8QyZ5BiftQP7M/7cj2U.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? Y
Please type 'yes', 'no' or the fingerprint: Y
Please type 'yes', 'no' or the fingerprint: YES
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@172.16.10.57's password: 
Permission denied, please try again.
vagrant@172.16.10.57's password: 
Permission denied, please try again.
vagrant@172.16.10.57's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@172.16.10.57'"
and check to make sure that only the key(s) you wanted were added.

vagrant@vagrant:~$ ssh vagrant@172.16.10.57
Welcome to Ubuntu 22.04.1 LTS (GNU/Linux 5.15.0-56-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Feb  8 01:27:55 PM UTC 2023

  System load:  0.080078125        Processes:             111
  Usage of /:   13.6% of 30.34GB   Users logged in:       1
  Memory usage: 13%                IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                 IPv4 address for eth1: 172.16.10.57


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Wed Feb  8 07:25:42 2023 from 10.0.2.2
```   

6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.

7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.
