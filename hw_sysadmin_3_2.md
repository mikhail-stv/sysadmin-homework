1. Какого типа команда ```cd```? Попробуйте объяснить, почему она именно такого типа: опишите ход своих мыслей, если считаете, что она могла бы быть другого типа.

Команда ```cd``` является встроенной в ```shell```, ее выполнение происходит быстро, так как оболоке не надо искать ее по заданному пути в переменной 
```PATH```, а так же не надо создавать процесс для ее выполнения.

2. Какая альтернатива без pipe команде ```grep <some_string> <some_file> | wc -l```?
```
vagrant@vagrant: grep some_string some_file -c
1
vagrant@vagrant: grep some_string some_file | wc -l
1
```
3. Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?

```systemd(1)```

4. Как будет выглядеть команда, которая перенаправит вывод stderr ```ls``` на другую сессию терминала?

Запуск команды в первой сессии терминала:

```
vagrant@vagrant:/$ ls /mount 2>/dev/pts/2
vagrant@vagrant:/$
```

Вывод ошибки во второй сессии терминала:

```
vagrant@vagrant:~$ ls: cannot access 'root': No such file or directory
ls: cannot access 'root': No such file or directory
```
 
5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? 
Приведите работающий пример
```
vagrant@vagrant:~$ cat file
1
2
3
wdsa
sad
c
vagrant@vagrant:~$ cat < file > new_file
vagrant@vagrant:~$ cat new_file
1
2
3
wdsa
sad
c
vagrant@vagrant:~$
```

6. Получится ли, находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY?  
Сможете ли вы наблюдать выводимые данные?

```root@Sky:/dev/pts# echo TEST PTY > /dev/tty1```
```root@Sky:/dev/pts#```

В tty получим вывод: 

```mike@sky: TETS PTY```

7. Выполните команду ```bash 5>&1```. К чему она приведет? Что будет, если вы выполните ```echo netology > /proc/$$/fd/5```?  
Почему так происходит?

```bash 5>&1``` создаст новый дескрипотр 5:

```
vagrant@vagrant:~$ bash 5>&1
vagrant@vagrant:/proc/1246/fd$ ls -lha
total 0
dr-x------ 2 vagrant vagrant  0 Jan  4 10:50 .  
dr-xr-xr-x 9 vagrant vagrant  0 Jan  4 10:50 ..  
lrwx------ 1 vagrant vagrant 64 Jan  4 10:51 0 -> /dev/pts/0  
lrwx------ 1 vagrant vagrant 64 Jan  4 10:51 1 -> /dev/pts/0  
lrwx------ 1 vagrant vagrant 64 Jan  4 10:51 2 -> /dev/pts/0  
lrwx------ 1 vagrant vagrant 64 Jan  4 10:51 255 -> /dev/pts/0  
lrwx------ 1 vagrant vagrant 64 Jan  4 10:50 5 -> /dev/pts/0
```

выполнение ```echo netology > /proc/$$/fd/5``` выведет данные в дескрипотр 5:  

```
vagrant@vagrant:~$ echo netology > /proc/$$/fd/5  
netology
```

8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty?

``` ls -l /root 7>&2 2>&1 1>&7 | wc -l```

```
vagrant@vagrant:~$ ls -l /root 7>&2 2>&1 1>&7 | wc -l
1
```
Cоздается новый дескриптор 7 и перенаправляется в stderr (7>&2) далее stderr перенаправляется в stdout (2>&1) и stdout в новый дескриптор (1>&7)

9. Что выведет команда ```cat /proc/$$/environ?``` Как еще можно получить аналогичный по содержанию вывод?

```cat /proc/$$/environ``` - выведет переменные окружения. Аналогичный вывод можно получить с помощью env. 

10. Используя ```man```, опишите что доступно по адресам ```/proc/<PID>/cmdline```, ```/proc/<PID>/exe```.

```/proc/<PID>/cmdline``` - файл, доступный только для чтения, содержит полный путь до исполняемого файла процесса [PID], если только процесс не является 
зомби. Строка 238 ```/proc/<PID>/exe``` - содержит ссылку до файла запущенного процесса [PID]. Строка 295

11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью ```/proc/cpuinfo```.

```SSE2```
```
vagrant@vagrant:~$ cat /proc/cpuinfo | grep sse
flags: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush 
mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt rdtscp lm constant_tsc rep_good nopl 
nonstop_tsc cpuid extd_apicid tsc_known_freq pni pclmulqdq ssse3 cx16 sse4_1 sse4_2 
x2apic movbe popcnt aes xsave avx rdrand hypervisor lahf_lm cmp_legacy cr8_legacy abm 
sse4a misalignsse 3dnowprefetch ssbd vmmcall fsgsbase avx2 rdseed clflushopt arat

flags: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush 
mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt rdtscp lm constant_tsc rep_good nopl 
nonstop_tsc cpuid extd_apicid tsc_known_freq pni pclmulqdq ssse3 cx16 sse4_1 sse4_2 
x2apic movbe popcnt aes xsave avx rdrand hypervisor lahf_lm cmp_legacy cr8_legacy abm 
sse4a misalignsse 3dnowprefetch ssbd vmmcall fsgsbase avx2 rdseed clflushopt arat
```

12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty.
Это можно подтвердить командой tty, которая упоминалась в лекции 3.2.
Однако:
```
vagrant@netology1:~$ ssh localhost 'tty'
not a tty
```
В мануале по SSH прописано, что ssh не имеет локального tty. Для изменения данного поведения требуется запустить с опцией -t
```
vagrant@vagrant:~$ ssh -t localhost 'tty'
vagrant@localhost's password: 
/dev/pts/1
Connection to localhost closed.
vagrant@vagrant:~$ 
```

13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись ```reptyr```. 
Например, так можно перенести в ```screen``` процесс, который вы запустили по ошибке в обычной SSH-сессии.

В ```/etc/sysctl.d/10-ptrace.conf``` поменять значение kernel.yama.ptrace_scope на 0, перезагрузиь машиную, запустить новый процесс 

```
vagrant@vagrant:~$ top  
vagrant@vagrant:~$ bg  
vagrant@vagrant:~$ ps -a (запомнить PID процесса)
```  

```screen -S new``` (через screen запустить отдельную сессию)  
```reptyr 'PID процесса```

14. ```sudo echo string > /root/new_file``` не даст выполнить перенаправление под обычным пользователем, 
так как перенаправлением занимается процесс shell'а, который запущен без ```sudo``` под вашим пользователем. 
Для решения данной проблемы можно использовать конструкцию ```echo string | sudo tee /root/new_file```. 
Узнайте? что делает команда ```tee``` и почему в отличие от ```sudo echo``` команда с ```sudo tee``` будет работать.
 
Команда ```tee``` считывет ввод и записывает вывод одновременно и в файл, и в stdout. В данном примере команда получает вывод из stdin, перенаправленный 
через pipe от stdout команды echo и так как команда запущена от sudo, соотвественно имеет права на запись в файл.


