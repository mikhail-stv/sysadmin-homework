1. Установить node_exporter. Создать простой unit-файл для node_exporter:
 - поместить его в автозагрузку
 - предусмотреть возможность добавления опций к запускаемому процессу через внешний файл (посмотреть, например, на systemctl cat cron)
 - удостовериться, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается

- Создаем Systemd Unit   

```$ sudo nano /etc/systemd/system/node_exporter.service```  
```
[Unit]  
Description=Prometheus Node Exporter  
Wants=network-online.target  
After=network-online.target  
  
[Service]  
User=node_exporter  
Group=node_exporter  
Type=simple  
ExecStart=/usr/local/bin/node_exporter  
  
[Install]  
WantedBy=multi-user.target  
```
- Добавление переменную окружения   
```
$ nano /etc/default/node_exporter
OPTIONS = "--collector.textfile.directory /var/lib/node_exporter/textfile_collector"
```

- Добавляение сервиса в автозагрузку    

```sudo systemctl enable --now node_exporter```
```
root@vagrant:/tmp/node_exporter-1.5.0.linux-amd64# sudo systemctl enable --now node_exporter
Created symlink /etc/systemd/system/multi-user.target.wants/node_exporter.service → /etc/systemd/system/node_exporter.service.
```
  ```
  root@vagrant:/tmp/node_exporter-1.5.0.linux-amd64# sudo systemctl status node_exporter
● node_exporter.service - Prometheus Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2023-01-15 14:45:58 UTC; 11s ago
   Main PID: 13977 (node_exporter)
      Tasks: 4 (limit: 1065)
     Memory: 2.8M
     CGroup: /system.slice/node_exporter.service
             └─13977 /usr/local/bin/node_exporter --collector.textfile.directory /var/lib/node_exporter/>

Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.590Z caller=node_exporter.go:117 le>
Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.590Z caller=node_exporter.go:117 le>
Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.590Z caller=node_exporter.go:117 le>
Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.590Z caller=node_exporter.go:117 le>
Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.590Z caller=node_exporter.go:117 le>
Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.590Z caller=node_exporter.go:117 le>
Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.590Z caller=node_exporter.go:117 le>
Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.590Z caller=node_exporter.go:117 le>
Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.591Z caller=tls_config.go:232 level>
Jan 15 14:45:58 vagrant node_exporter[13977]: ts=2023-01-15T14:45:58.591Z caller=tls_config.go:235 level>
root@vagrant:/tmp/node_exporter-1.5.0.linux-amd64# sudo ss -pnltu | grep 9100
tcp     LISTEN   0        4096                    *:9100                *:*      users:(("node_exporter",pid=13977,fd=3))
```
- После перезагрузки сервис успешно запустился  

2. Ознакомьтесь с опциями node_exporter и выводом ```/metrics``` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
- CPU
```
node_cpu_seconds_total{cpu="0",mode="idle"}
node_cpu_seconds_total{cpu="0",mode="system"}
node_cpu_seconds_total{cpu="0",mode="user"}
process_cpu_seconds_total
```
- Memory
```
node_memory_MemAvailable_bytes
node_memory_MemFree_bytes
node_memory_Buffers_bytes
node_memory_Cached_bytes
```
- Disks
```
node_disk_io_time_seconds_total{device="sda"}
node_disk_read_time_seconds_total{device="sda"}
node_disk_write_time_seconds_total{device="sda"}
node_filesystem_avail_bytes
```
- Network
```
node_network_info
node_network_receive_bytes_total
node_network_receive_errs_total
node_network_transmit_bytes_total
node_network_transmit_errs_total
```

3. Установите в свою виртуальную машину Netdata
```
root@vagrant:/home/vagrant# sudo ss -tulpn | grep :19999
tcp   LISTEN 0      4096      0.0.0.0:19999      0.0.0.0:*    users:(("netdata",pid=2972,fd=5))
root@vagrant::/home/vagrant# sudo lsof -i :19999
COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
netdata 2972 netdata    5u  IPv4  28925      0t0  TCP vagrant:19999 (LISTEN)
netdata 2972 netdata   67u  IPv4  31632      0t0  TCP vagrant:19999->0.0.0.0:53625 (ESTABLISHED)
```
После проброса порта на своем ПК зашел на ```localhost:19999```  

4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?  
Да возможно, данный выод показувает, что ОС запущена на системе виртуализации  
```
vagrant@vagrant:/home/vagrant# dmesg | grep -i virtual
[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006
[    0.001615] CPU MTRRs all blank - virtualized system.
[    0.041192] Booting paravirtualized kernel on KVM
[    0.205238] Performance Events: PMU not available due to virtualization, using software events only.
[    3.183424] systemd[1]: Detected virtualization oracle.
```

5. Настроки ```sysctl fs.nr_open``` на системе по-умолчанию, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (```ulimit --
help```)?
```
vagrant@vagrant:$ sysctl -n fs.nr_open  
1048576  
```
Это максимальное количество файловых дескрипторов, которые могут быть выделены одним процессом. Число задается кратное 1024. Для изменения данного значения нужно увеличить значение nr_open ```sysctl -w fs.nr_open = 10000000``` или записать его непосредственно в файл sysctl.conf.  
максимальное количество открытых файловых дескрипторов     
```
vagrant@vagrant:~$ ulimit -n
1024
```
Мягкий предел
```
root@zabbix:/home/zabbix# ulimit -Sn
1024
```
Жесткий предел
```
root@vagrant:/home/vagrant# ulimit -Hn
1048576
```
- количество файловых дескрипторов, открытых всеми процессами, не может превышать ```/proc/sys/fs/file-max``` 
- количество файловых дескрипторов, открытых одним процессом, не может превышать мягкое ограничение ```nofile``` в пользовательском ограничении  
- мягкий предел ```nofile``` не может превышать его жесткий предел  
- жесткий предел ```nofile``` не может превышать ```/proc/sys/fs/nr_open```  

6. Запустить любой долгоживущий процесс (не ```ls```, который отработает мгновенно, а, например, ```sleep 1h```) в отдельном неймспейсе процессов; показать, что данный процесс работает под PID 1 через ```nsenter```. Под root запускать - ```sudo -i```. Под обычным пользователем требуются дополнительные опции - ```--map-root-user```.  
```
vagrant@vagrant-fs:~$ sudo -i
root@vagrant-fs:~# unshare -f --pid --mount-proc sleep 1h
bg
^Z
[1]+  Stopped                 unshare -f --pid --mount-proc sleep 1h
root@vagrant:~# ps
    PID TTY          TIME CMD
   1708 pts/0    00:00:00 sudo
   1710 pts/0    00:00:00 bash
   1736 pts/0    00:00:00 unshare
   1737 pts/0    00:00:00 sleep
   1739 pts/0    00:00:00 ps
root@vagrant:~# nsenter --target 1737 --pid --mount
root@vagrant:/# ps
    PID TTY          TIME CMD
      1 pts/0    00:00:00 sleep
      2 pts/0    00:00:00 bash
     13 pts/0    00:00:00 ps
root@vagrant:/# 
```

7. Найти информацию о том, что такое ```:(){ :|:& };:```  
Данная команда является логической бомбой. Она оперирует определением функции с именем ```‘:‘```, которая вызывает сама себя дважды: один раз на переднем плане и один раз в фоне. Она продолжает своё выполнение снова и снова, пока система не зависнет.  
Выход из зацикливания проиcходит при превышении параметров по умолчанию, которые моно посмотреть с помощью ```ulimit -a```:  
```
vagrant@vagrant:~$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 7579
max locked memory       (kbytes, -l) 65536
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 3703
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```
Можно изменить данный лимит с помощью ulimit -a, для 50 процессов надо выставить ulimit -u 50:
```
vagrant@vagrant:~$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 7579
max locked memory       (kbytes, -l) 65536
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 50
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```
Либо наложить ограничение на количество процессов, которые может порождать пользователь или группа пользователей, можно отредактировав файл /etc/security/limits.conf
