1. Подключиться утилитой телнет к сайту stackoverflow.com telnet stackoverflow.com 80  
```
mstepanov@Admin:~$ telnet stackoverflow.com 80
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
"HTTP 403 Forbidden"  указывает, что сервер понял запрос, но отказывается его авторизовать.

Сделал аналогичный запрос на ```ya.ru```
```
mstepanov@Admin:~$ telnet ya.ru 80
Trying 87.250.250.242...
Connected to ya.ru.
Escape character is '^]'.
GET /questions HTTP/1.0
HOST: ya.ru             

HTTP/1.1 302 Moved temporarily
Content-Length: 0
Location: https://ya.ru/questions
NEL: {"report_to": "network-errors", "max_age": 100, "success_fraction": 0.001, "failure_fraction": 0.1}
Report-To: { "group": "network-errors", "max_age": 100, "endpoints": [{"url": "https://dr.yandex.net/nel", "priority": 1}, {"url": "https://dr2.yandex.net/nel", "priority": 2}]}
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
X-Yandex-Req-Id: 1675101961355631-2680215709650431823-vla1-3235-vla-l7-balancer-8080-BAL
set-cookie: is_gdpr=0; Path=/; Domain=.ya.ru; Expires=Wed, 29 Jan 2025 18:06:01 GMT
set-cookie: is_gdpr_b=CPvaURDtowE=; Path=/; Domain=.ya.ru; Expires=Wed, 29 Jan 2025 18:06:01 GMT
set-cookie: _yasc=ioh8dWE9rCKFG68gfK1HG+KMM4I35yvY7B6kOx48/gGPCPuR8Lqp56tFXohNlM8=; domain=.ya.ru; path=/; expires=Thu, 27-Jan-2033 18:06:01 GMT; secure

Connection closed by foreign host.
```
Получил код ответа 302 Found означающий, что запрошенный ресурс был временно перемещён по адресу, указанному в заголовке  

2. Повторите задание 1 в браузере, используя консоль разработчика F12.
```
Request URL: https://stackoverflow.com/
Request Method: GET
Status Code: 200 
Remote Address: 151.101.193.69:443
Referrer Policy: strict-origin-when-cross-origin
```
![](https://github.com/mikhail-stv/sysadmin-homework/blob/main/Snapshot_2023-01-29_20-41-50.png)

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
mstepanov@Admin:~$ traceroute -An 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  10.0.2.2 [*]  0.193 ms  0.154 ms  0.135 ms
 2  10.1.1.1 [*]  1.535 ms  1.289 ms  1.120 ms
 3  185.171.192.57 [AS8492]  2.275 ms  2.164 ms  2.034 ms
 4  172.29.194.40 [*]  2.599 ms  2.480 ms  5.000 ms
 5  172.29.193.142 [*]  2.238 ms  4.779 ms  4.598 ms
 6  172.29.255.3 [*]  4.572 ms  1.998 ms  1.941 ms
 7  172.29.255.5 [*]  1.910 ms  2.052 ms  2.333 ms
 8  172.29.255.7 [*]  2.311 ms  2.575 ms  2.541 ms
 9  85.114.1.12 [AS8492]  2.549 ms  2.532 ms  2.521 ms
10  72.14.198.236 [AS15169]  2.514 ms  3.417 ms  3.388 ms
11  74.125.244.180 [AS15169]  3.383 ms  3.376 ms 74.125.244.132 [AS15169]  14.249 ms
12  142.251.61.219 [AS15169]  31.776 ms 72.14.232.85 [AS15169]  16.767 ms 216.239.48.163 [AS15169]  19.441 ms
13  172.253.51.221 [AS15169]  21.178 ms 142.251.51.187 [AS15169]  27.673 ms 142.251.61.221 [AS15169]  6.642 ms
14  * 142.250.209.35 [AS15169]  6.712 ms 209.85.254.179 [AS15169]  13.748 ms
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  8.8.8.8 [AS15169/AS263411]  6.515 ms  14.747 ms  6.504 ms
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
Наибольшая задержка - delay на 8ом Шаге  

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
