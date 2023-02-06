1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP.  
```
mstepanov@Admin:~$ telnet route-views.routeviews.org
Trying 128.223.51.103...
Connected to route-views.routeviews.org.
Escape character is '^]'.
C
**********************************************************************

                    RouteViews BGP Route Viewer
                    route-views.routeviews.org

 route views data is archived on http://archive.routeviews.org

 This hardware is part of a grant by the NSF.
 Please contact help@routeviews.org if you have questions, or
 if you wish to contribute your view.

 This router has views of full routing tables from several ASes.
 The list of peers is located at http://www.routeviews.org/peers
 in route-views.oregon-ix.net.txt

 NOTE: The hardware was upgraded in August 2014.  If you are seeing
 the error message, "no default Kerberos realm", you may want to
 in Mac OS X add "default unset autologin" to your ~/.telnetrc

 To login, use the username "rviews".

 **********************************************************************

User Access Verification

Username: rviews
route-views>show ip route 176.53.196.51
Routing entry for 176.53.196.0/24
  Known via "bgp 6447", distance 20, metric 0
  Tag 3267, type external
  Last update from 194.85.40.15 2w0d ago
  Routing Descriptor Blocks:
  * 194.85.40.15, from 194.85.40.15, 2w0d ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 3267
      MPLS label: none
```
```
route-views>show bgp 176.53.196.51     
BGP routing table entry for 176.53.196.0/24, version 2658231431
Paths: (19 available, best #9, table default)
  Not advertised to any peer
  Refresh Epoch 1
  20912 3257 9002 56534
    212.66.96.126 from 212.66.96.126 (212.66.96.126)
      Origin IGP, localpref 100, valid, external
      Community: 3257:8052 3257:50001 3257:54900 3257:54901 20912:65004 65535:65284
      path 7FE13BAB4048 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  6939 56534
    64.71.137.241 from 64.71.137.241 (216.218.253.53)
      Origin IGP, localpref 100, valid, external
      path 7FE09E3B7D40 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  8283 20764 56534
    94.142.247.3 from 94.142.247.3 (94.142.247.3)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 8283:1 8283:101 8283:102 20764:3002 20764:3010 20764:3021 56534:6669 56534:9000
      unknown transitive attribute: flag 0xE0 type 0x20 length 0x30
        value 0000 205B 0000 0000 0000 0001 0000 205B
              0000 0005 0000 0001 0000 205B 0000 0005
              0000 0002 0000 205B 0000 0008 0000 001A
              
      path 7FE1019D2118 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  7018 3356 31500 56534
    12.0.1.63 from 12.0.1.63 (12.0.1.63)
      Origin IGP, localpref 100, valid, external
      Community: 7018:5000 7018:37232
      path 7FE0C2369668 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3257 9002 56534
    89.149.178.10 from 89.149.178.10 (213.200.83.26)
      Origin IGP, metric 10, localpref 100, valid, external
      Community: 3257:8052 3257:50001 3257:54900 3257:54901 65535:65284
      path 7FE11BD56DF0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  49788 12552 9002 56534
    91.218.184.60 from 91.218.184.60 (91.218.184.60)
      Origin IGP, localpref 100, valid, external
      Community: 12552:10000 12552:14000 12552:14100 12552:14101 12552:24000
      Extended Community: 0x43:100:0
      path 7FE038E60500 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3561 3910 3356 31500 56534
    206.24.210.80 from 206.24.210.80 (206.24.210.80)
      Origin IGP, localpref 100, valid, external
      path 7FE17D1E2D68 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3549 3356 31500 56534
    208.51.134.254 from 208.51.134.254 (67.16.168.191)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 3356:2 3356:22 3356:100 3356:123 3356:503 3356:901 3356:2067 3549:2581 3549:30840
      path 7FE0DBBCC120 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3267 56534
    194.85.40.15 from 194.85.40.15 (185.141.126.1)
      Origin IGP, metric 0, localpref 100, valid, external, best
      path 7FE0E5AFA530 RPKI State valid
      rx pathid: 0, tx pathid: 0x0
  Refresh Epoch 1
  3333 20764 56534
    193.0.0.56 from 193.0.0.56 (193.0.0.56)
      Origin IGP, localpref 100, valid, external
      Community: 20764:3002 20764:3010 20764:3021 56534:6669 56534:9000
      path 7FE12C6B71D8 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  4901 6079 9002 56534
    162.250.137.254 from 162.250.137.254 (162.250.137.254)
      Origin IGP, localpref 100, valid, external
      Community: 65000:10100 65000:10300 65000:10400
      path 7FE082BF25B0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  101 3491 20485 20485 56534
    209.124.176.223 from 209.124.176.223 (209.124.176.223)
      Origin IGP, localpref 100, valid, external
      Community: 101:20300 101:22100 3491:300 3491:311 3491:9001 3491:9080 3491:9081 3491:9087 3491:62210 3491:62220 20485:10078
      path 7FE129BEBE58 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3356 20764 56534
    4.68.4.46 from 4.68.4.46 (4.69.184.201)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 3356:2 3356:22 3356:100 3356:123 3356:501 3356:901 3356:2065 20764:3002 20764:3010 20764:3021 56534:6669 56534:9000 65000:714 65000:6185 65000:16509 65000:16625 65000:20940
      path 7FE1268A4278 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  57866 9002 56534
    37.139.139.17 from 37.139.139.17 (37.139.139.17)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 9002:0 9002:64667 57866:100 65100:9002 65103:1 65104:31
      unknown transitive attribute: flag 0xE0 type 0x20 length 0x30
        value 0000 E20A 0000 0064 0000 232A 0000 E20A
              0000 0065 0000 0064 0000 E20A 0000 0067
              0000 0001 0000 E20A 0000 0068 0000 001F
              
      path 7FE13070CAB0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 2
  2497 20485 56534
    202.232.0.2 from 202.232.0.2 (58.138.96.254)
      Origin IGP, localpref 100, valid, external
      path 7FE0D87ED3F0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  20130 6939 56534
    140.192.8.16 from 140.192.8.16 (140.192.8.16)
      Origin IGP, localpref 100, valid, external
      path 7FE10D33DBA8 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  852 3491 20485 20485 56534
    154.11.12.212 from 154.11.12.212 (96.1.209.43)
      Origin IGP, metric 0, localpref 100, valid, external
      path 7FE0E5E27C28 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  1351 20764 20764 20764 20764 20764 20764 20764 20764 20764 56534
    132.198.255.253 from 132.198.255.253 (132.198.255.253)
      Origin IGP, localpref 100, valid, external
      path 7FE17C5E3C28 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3303 20485 56534
    217.192.89.50 from 217.192.89.50 (138.187.128.158)
      Origin IGP, localpref 100, valid, external
      Community: 3303:1004 3303:1006 3303:1030 3303:1031 3303:3056 20485:10078 65101:1082 65102:1000 65103:276 65104:150
      path 7FE16E7F7928 RPKI State valid
      rx pathid: 0, tx pathid: 0
```
      
2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.
```
mstepanov@Admin:~$ sudo modprobe -v dummy numdummies=1
[sudo] password for mstepanov: 
insmod /lib/modules/5.19.0-29-generic/kernel/drivers/net/dummy.ko numdummies=0 numdummies=1
mstepanov@Admin:~$ lsmod | grep dummy
dummy                  16384  0
snd_seq_dummy          16384  0
snd_seq                77824  9 snd_seq_midi,snd_seq_midi_event,snd_seq_dummy
mstepanov@Admin:~$ ifconfig -a | grep dummy
dummy0: flags=130<BROADCAST,NOARP>  mtu 1500
root@Admin:/home/mstepanov# ip link set dev dummy0 up
root@Admin:/home/mstepanov# ip addr add 192.168.70.10/24 dev dummy0
root@Admin:/home/mstepanov# ip route add 172.16.20.205/32 dev dummy0
root@Admin:/home/mstepanov# ip -c a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp10s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether ac:22:0b:50:7c:5b brd ff:ff:ff:ff:ff:ff
    inet 172.16.10.1/20 brd 172.16.15.255 scope global noprefixroute enp10s0
       valid_lft forever preferred_lft forever
    inet6 fe80::63fe:8425:9063:88ae/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
7: dummy0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether 6a:20:df:11:3b:4d brd ff:ff:ff:ff:ff:ff
    inet 192.168.70.10/24 scope global dummy0
       valid_lft forever preferred_lft forever
    inet6 fe80::6820:dfff:fe11:3b4d/64 scope link 
       valid_lft forever preferred_lft forever
root@Admin:/home/mstepanov# ip r
default via 172.16.0.1 dev enp10s0 proto static metric 100 
169.254.0.0/16 dev enp10s0 scope link metric 1000 
172.16.0.0/20 dev enp10s0 proto kernel scope link src 172.16.10.1 metric 100 
172.16.20.205 dev dummy0 scope link 
192.168.70.0/24 dev dummy0 proto kernel scope link src 192.168.70.10
```
  
3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.
```
root@Admin:/home/mstepanov# ss -ltpn
State       Recv-Q      Send-Q            Local Address:Port             Peer Address:Port      Process                                                                                         
LISTEN      0           4096                 127.0.0.54:53                    0.0.0.0:*          users:(("systemd-resolve",pid=677,fd=16))                                                      
LISTEN      0           4096              127.0.0.53%lo:53                    0.0.0.0:*          users:(("systemd-resolve",pid=677,fd=14))                                                      
LISTEN      0           128                   127.0.0.1:631                   0.0.0.0:*          users:(("cupsd",pid=200210,fd=7))                                                              
LISTEN      0           10                      0.0.0.0:7070                  0.0.0.0:*          users:(("anydesk",pid=926,fd=32))                                                              
LISTEN      0           4096                          *:22                          *:*          users:(("sshd",pid=43215,fd=3),("systemd",pid=1,fd=156))                                       
LISTEN      0           2                             *:3389                        *:*          users:(("xrdp",pid=14402,fd=11))                                                               
LISTEN      0           2                         [::1]:3350                     [::]:*          users:(("xrdp-sesman",pid=14392,fd=11))                                                        
LISTEN      0           128                       [::1]:631                      [::]:*          users:(("cupsd",pid=200210,fd=6))
```
22й порт SSH
53й порт DNS
3389й порт RDP
  
4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?
```
root@Admin:/home/mstepanov# ss -lupn
State       Recv-Q      Send-Q           Local Address:Port              Peer Address:Port      Process                                                                                         
UNCONN      0           0                  224.0.0.251:5353                   0.0.0.0:*          users:(("chrome",pid=2248,fd=261))                                                             
UNCONN      0           0                  224.0.0.251:5353                   0.0.0.0:*          users:(("chrome",pid=2248,fd=188))                                                             
UNCONN      0           0                  224.0.0.251:5353                   0.0.0.0:*          users:(("chrome",pid=2297,fd=104))                                                             
UNCONN      0           0                      0.0.0.0:5353                   0.0.0.0:*          users:(("avahi-daemon",pid=763,fd=12))                                                         
UNCONN      0           0                      0.0.0.0:47086                  0.0.0.0:*          users:(("1cv8c",pid=210899,fd=11))                                                             
UNCONN      0           0                   127.0.0.54:53                     0.0.0.0:*          users:(("systemd-resolve",pid=677,fd=15))                                                      
UNCONN      0           0                127.0.0.53%lo:53                     0.0.0.0:*          users:(("systemd-resolve",pid=677,fd=13))                                                      
UNCONN      0           0                      0.0.0.0:631                    0.0.0.0:*          users:(("cups-browsed",pid=200211,fd=7))                                                       
UNCONN      0           0                      0.0.0.0:50001                  0.0.0.0:*          users:(("anydesk",pid=926,fd=46))                                                              
UNCONN      0           0                      0.0.0.0:58808                  0.0.0.0:*          users:(("winbox64.exe",pid=211632,fd=15),("wineserver64",pid=211576,fd=259))                   
UNCONN      0           0                      0.0.0.0:1701                   0.0.0.0:*          users:(("xl2tpd",pid=1200,fd=3))                                                               
UNCONN      0           0                      0.0.0.0:51044                  0.0.0.0:*          users:(("avahi-daemon",pid=763,fd=14))                                                         
UNCONN      0           0                         [::]:5353                      [::]:*          users:(("avahi-daemon",pid=763,fd=13))                                                         
UNCONN      0           0                         [::]:41073                     [::]:*          users:(("avahi-daemon",pid=763,fd=15))  
```
  
5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали

https://drive.google.com/file/d/1CkuqwQHupMIIgcXreNg7HHr3GFcLVqqY/view?usp=share_link

-----------------------------------------------------------------------------------------------------------------

6. Установите Nginx, настройте в режиме балансировщика TCP или UDP.

7. Установите bird2, настройте динамический протокол маршрутизации RIP.

8. Установите Netbox, создайте несколько IP префиксов, используя curl проверьте работу API.
