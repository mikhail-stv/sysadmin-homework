1. Подключиться утилитой телнет к сайту stackoverflow.com telnet stackoverflow.com 80  
```
mike@Sky:~$ telnet stackoverflow.com 80
Trying 151.101.65.69...
Connected to stackoverflow.com.
Escape character is '^]'.
GET /questions HTTP/1.0
HOST: stackoverflow.com

HTTP/1.1 403 Forbidden
Connection: close
Content-Length: 1920
Server: Varnish
Retry-After: 0
Content-Type: text/html
Accept-Ranges: bytes
Date: Sun, 29 Jan 2023 17:31:32 GMT
Via: 1.1 varnish
X-Served-By: cache-hhn-etou8220077-HHN
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1675013492.252468,VS0,VE1
X-DNS-Prefetch-Control: off
```
2. Повторите задание 1 в браузере, используя консоль разработчика F12.
```
Request URL: https://stackoverflow.com/
Request Method: GET
Status Code: 200 
Remote Address: 151.101.193.69:443
Referrer Policy: strict-origin-when-cross-origin
```

![](https://drive.google.com/file/d/1HFG6b85oRgVfvRr05mGOtKAskratDhlH/view?usp=share_link)

3. Какой IP адрес у вас в интернете? 
```
mstepanov@Admin:~$ wget -qO- eth0.me
185.22.207.105
```

4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS?
```
mstepanov@Admin:~$ whois 185.22.207.105
...
...
% Information related to '185.22.204.0/22AS42339'

route:          185.22.204.0/22
descr:          "Chaika Telecom Petersburg" LTD
origin:         AS42339
mnt-by:         CHTP-MNT
created:        2013-04-04T08:49:00Z
last-modified:  2013-04-04T08:50:17Z
source:         RIPE

```
5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS?
```
mstepanov@Admin:~$ traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  10.1.1.1 (10.1.1.1)  10.481 ms  12.439 ms  12.629 ms
 2  254.120.72.77.chtp.net (77.72.120.254)  13.527 ms  13.564 ms  13.722 ms
 3  93.123.72.77.chtp.net (77.72.123.93)  14.462 ms  14.573 ms  14.702 ms
 4  BGP2-KM12.chtp.net (46.28.224.86)  13.943 ms  14.137 ms  14.231 ms
 5  google-spb2.spb.cloud-ix.net (31.28.18.199)  15.042 ms  14.897 ms  15.221 ms
 6  74.125.244.132 (74.125.244.132)  15.737 ms 74.125.244.180 (74.125.244.180)  11.450 ms 74.125.244.132 (74.125.244.132)  16.769 ms
 7  72.14.232.85 (72.14.232.85)  14.010 ms 142.251.61.219 (142.251.61.219)  18.918 ms 72.14.232.85 (72.14.232.85)  14.184 ms
 8  216.239.58.65 (216.239.58.65)  18.203 ms 172.253.64.51 (172.253.64.51)  18.352 ms 142.251.61.221 (142.251.61.221)  18.553 ms
 9  172.253.51.237 (172.253.51.237)  17.681 ms 216.239.63.129 (216.239.63.129)  17.479 ms 72.14.237.201 (72.14.237.201)  17.855 ms
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  dns.google (8.8.8.8)  18.781 ms * *
```
6. Повторите задание 5 в утилите ```mtr```. На каком участке наибольшая задержка - delay?
```
mstepanov@Admin:~$ mtr 8.8.8.8 -znrc 1
Start: 2023-01-26T17:45:00+0300
HOST: Admin                       Loss%   Snt   Last   Avg  Best  Wrst StDev
  1. AS???    10.1.1.1             0.0%     1   10.4  10.4  10.4  10.4   0.0
  2. AS42339  77.72.120.254        0.0%     1   11.0  11.0  11.0  11.0   0.0
  3. AS42339  77.72.123.93         0.0%     1   13.8  13.8  13.8  13.8   0.0
  4. AS42339  46.28.224.86         0.0%     1   11.0  11.0  11.0  11.0   0.0
  5. AS29076  31.28.18.199         0.0%     1   17.0  17.0  17.0  17.0   0.0
  6. AS15169  74.125.244.180       0.0%     1   11.4  11.4  11.4  11.4   0.0
  7. AS15169  142.251.61.219       0.0%     1   15.5  15.5  15.5  15.5   0.0
  8. AS15169  172.253.51.185       0.0%     1   17.8  17.8  17.8  17.8   0.0
  9. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 10. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 11. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 12. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 13. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 14. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 15. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 16. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 17. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 18. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 19. AS???    ???                 100.0     1    0.0   0.0   0.0   0.0   0.0
 20. AS15169  8.8.8.8              0.0%     1   15.9  15.9  15.9  15.9   0.0
```
7. Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи?
```
mstepanov@Admin:~$ dig +short NS dns.google
ns4.zdns.google.
ns2.zdns.google.
ns1.zdns.google.
ns3.zdns.google.
mstepanov@Admin:~$ dig +short А dns.google
8.8.8.8
8.8.4.4
```
8. Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP?
```
mstepanov@Admin:~$ dig -x 8.8.8.8~

; <<>> DiG 9.18.4-2ubuntu2-Ubuntu <<>> -x 8.8.8.8~
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 14872
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;8~.8.8.8.in-addr.arpa.		IN	PTR

;; AUTHORITY SECTION:
8.8.8.in-addr.arpa.	60	IN	SOA	ns1.google.com. dns-admin.google.com. 504516018 900 900 1800 60

;; Query time: 32 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Thu Jan 26 17:56:28 MSK 2023
;; MSG SIZE  rcvd: 110
```
```
mstepanov@Admin:~$ dig -x 8.8.4.4

; <<>> DiG 9.18.4-2ubuntu2-Ubuntu <<>> -x 8.8.4.4
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 58088
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;4.4.8.8.in-addr.arpa.		IN	PTR

;; ANSWER SECTION:
4.4.8.8.in-addr.arpa.	78276	IN	PTR	dns.google.

;; AUTHORITY SECTION:
8.8.in-addr.arpa.	84170	IN	NS	ns2.level3.net.
8.8.in-addr.arpa.	84170	IN	NS	ns1.level3.net.

;; Query time: 12 msec
;; SERVER: 127.0.0.53#53(127.0.0.53) (UDP)
;; WHEN: Thu Jan 26 17:57:28 MSK 2023
;; MSG SIZE  rcvd: 119
```
