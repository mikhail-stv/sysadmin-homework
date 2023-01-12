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

4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
```vagrant@vagrant:~$ dmesg | grep -i virtual```
-
-
-

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
-
-
-

7. Найти информацию о том, что такое ```:(){ :|:& };:```  
Данная команда является логической бомбой. Она оперирует определением функции с именем ```‘:‘```, которая вызывает сама себя дважды: один раз на переднем плане и один раз в фоне. Она продолжает своё выполнение снова и снова, пока система не зависнет
