1. Команда ```cd``` является встроенной в ```shell```, ее выполнение происходит быстро, так как оболоке не надо искать ее по заданному путив переменной ```PATH```, а так же не надо создавать проццесс для ее выполнения.

2. 
```vagrant@vagrant: grep some_string some_file -c
1
vagrant@vagrant: grep some_string some_file | wc -l
1
```
3. systemd(1)

4. Запуск команды в первой сессии терминала:

```
vagrant@vagrant:/$ ls /mount 2>/dev/pts/2
vagrant@vagrant:/$
```

Вывод ошибки во второй сессии терминала:

```
vagrant@vagrant:~$ ls: cannot access 'root': No such file or directory
ls: cannot access 'root': No such file or directory
```
 
5. 
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

6. 
```root@Sky:/dev/pts# echo TEST PTY > /dev/tty1```
```root@Sky:/dev/pts#```

В tty получим вывод: 

```mike@sky: TETS PTY```

7. ```bash 5>&1``` создаст новый дескрипотр 5:

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

8. ``` ls -l 7>&2 2>&1 1>&7 ```

9. ```cat /proc/$$/environ``` - выведет переменные окружения. Аналогичный вывод можно получить с 
помощью env. 

10. ```/proc/<PID>/cmdline``` - файл, доступный только для чтения, содержит полный путь до 
исполняемого файла процесса [PID], если только процесс не является зомби. Строка 238
/proc/<PID>/exe - содержит ссылку до файла запущенного процесса [PID]. Строка 295

11. SSE2
```vagrant@vagrant:~$ cat /proc/cpuinfo | grep sse
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

12. В мануале по SSH прописано, что ssh не имеет локального tty. Для изменения данного 
поведения требуется запустить с опцией -t

```
vagrant@vagrant:~$ ssh -t localhost 'tty'
vagrant@localhost's password: 
/dev/pts/1
Connection to localhost closed.
vagrant@vagrant:~$ 
```

13. В ```/etc/sysctl.d/10-ptrace.conf``` поменять значение kernel.yama.ptrace_scope на 0, 
перезагрузиь машиную
- запустить новый процесс 

```
vagrant@vagrant:~$ top  
vagrant@vagrant:~$ bg  
vagrant@vagrant:~$ ps -a (запомнить PID процесса)
```  

```screen -S new``` (через screen запустить отдельную сессию)  
```reptyr 'PID процесса```

14. Команда ```tee``` считывет ввод и записывает вывод одновременно и в файл, и в stdout.
В данном примере команда получает вывод из stdin, перенаправленный через pipe от 
stdout команды echo и так как команда запущена от sudo, соотвественно имеет права на 
запись в файл.


