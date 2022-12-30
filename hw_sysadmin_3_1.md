1. По умолчанию выделено 1024mb и 2cpu, виртуальный диск на 64Gb

2. Количесвто памяти и параметры процессора выделяемое ВМ, указваются в настройках vagrantfile: 

vb.memory = 1024 
v.cpus = 2

5.
- HISTFILESIZE, задает длину журнала history. Строка 930
- ignoreboth, опция переменной HISTCONTROL, сочетает действие двух пций: ignorespace - игнорируются
строки начинающиесся символом пробела и ignoredups - игнорируются строки совпадающие 
с поледней выполненной командой.

6. Используются в списках команд, значений. В сравнении с () выполняется в контексте текущей оболочки.
Строка 281

7. touch {000001..100000}.txt
300000 создать не получится, так как слишлом длинный список аргументов

8. Уловный выражения, проверяет наличие каталога tmp и возвращает значение 0 или 1 (ложь или истина)
В данном примере -d проверяет является ли tmp каталогом

9. vagrant@vagrant:~$ mkdir /tmp/new_path_directory/```
vagrant@vagrant:~$ cd /bin/bash /tmp/new_patn_directory/```
-bash: cd: too many arguments```
vagrant@vagrant:~$ cp /bin/bash /tmp/new_patn_directory/```
cp: cannot create regular file '/tmp/new_patn_directory/': Not a directory```
vagrant@vagrant:~$ cp /bin/bash /tmp/new_path_directory/```
vagrant@vagrant:~$ PATH=/tmp/new_path_directory/:$PATH```
vagrant@vagrant:~$ type -a bash```
bash is /tmp/new_path_directory/bash```
bash is /usr/bin/bash```
bash is /bin/bash```

10. at используется для назначения одноразового задания на заданное время,
batch — для назначения одноразовых задач, которые должны выполняться,
когда загрузка системы становится меньше 0,8
