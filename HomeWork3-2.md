#_Домашнее задание занятию "3.1. Работа в терминале, лекция 2"_ #
##Выполнил  - Каплин Владимир ##



1. Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.

Ответ:
Запустим команду type и увидим, что команда cd является встроенной командой shell.
Если бы команда cd была внешней, то при ее выполнении bash создавал бы новые процессы, в рамках которых и менялась бы 
директория. В текущем процессе директория оставалась бы неизменной
```
vagrant@vagrant:~$ type cd
cd is a shell builtin
```
2. Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l? man grep поможет в ответе на этот вопрос. Ознакомьтесь с документом 
о других подобных некорректных вариантах использования pipe.

Ответ:

Ключ -l для команды wc выводит кол-во строк
```
-l, --lines  print the newline counts
```

Запустим команду с Pipe, результат ниже:
```
vagrant@vagrant:~$ grep 'sudo' .bash_history | wc -l
34
```

Далее для команды grep ключ -с выполняет аналогичную функцию. Запустим:
```
vagrant@vagrant:~$ grep 'sudo' .bash_history -c
34
```
Результат тот же.


3. Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?

Ответ: запустим команду pstree -p. Увидим процесс с PID 1  - systemd
```
vagrant@vagrant:~$ pstree -p
systemd(1)─┬─VBoxService(977)─┬─{VBoxService}(978)
           │                  ├─{VBoxService}(979)
           │                  ├─{VBoxService}(980)
           │                  ├─{VBoxService}(981)
           │                  ├─{VBoxService}(982)
           │                  ├─{VBoxService}(983)
           │                  ├─{VBoxService}(984)
           │                  └─{VBoxService}(985)
           ├─accounts-daemon(641)─┬─{accounts-daemon}(642)
           │                      └─{accounts-daemon}(742)
           ├─agetty(740)
           ├─apache2(671)─┬─apache2(801)
           │              ├─apache2(803)
           │              ├─apache2(811)
           │              ├─apache2(812)
           │              ├─apache2(814)
           │              └─apache2(5033)
           ├─atd(695)
           ├─cron(659)
           ├─dbus-daemon(660)
           ├─fwupd(4549)─┬─{fwupd}(4552)
           │             ├─{fwupd}(4553)
           │             ├─{fwupd}(4554)
           │             └─{fwupd}(4555)
           ├─irqbalance(667)───{irqbalance}(669)
           ├─multipathd(537)─┬─{multipathd}(538)
           │                 ├─{multipathd}(539)
           │                 ├─{multipathd}(540)
           │                 ├─{multipathd}(541)
           │                 ├─{multipathd}(542)
           │                 └─{multipathd}(543)
           ├─mysqld(927)─┬─{mysqld}(1144)
```


4. Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?

Командой pstree -g найдем PPID соседнего терминала. Мы находимся в терминале 2007.
Далее по PPID поймем какой dev\pts\x  у другого терминала, номер 
другого терминала 7715. 
Для вывод ошибок из терминала 2007 в терминал выполним команду ls kkkk 2>/dev/pts/2 (показать содержимое несуществующей
деректории kkkk)

```
vagrant@vagrant:~$ echo $$
2007
vagrant@vagrant:~$ ls -l /proc/2007/fd
total 0
lrwx------ 1 vagrant vagrant 64 Jan 24 20:00 0 -> /dev/pts/0
lrwx------ 1 vagrant vagrant 64 Jan 24 20:00 1 -> /dev/pts/0
lrwx------ 1 vagrant vagrant 64 Jan 24 20:00 2 -> /dev/pts/0
lrwx------ 1 vagrant vagrant 64 Jan 24 20:25 255 -> /dev/pts/0
vagrant@vagrant:~$ ls -l /proc/7715/fd
total 0
lrwx------ 1 vagrant vagrant 64 Jan 26 20:32 0 -> /dev/pts/2
lrwx------ 1 vagrant vagrant 64 Jan 26 20:32 1 -> /dev/pts/2
lrwx------ 1 vagrant vagrant 64 Jan 26 20:32 2 -> /dev/pts/2
lrwx------ 1 vagrant vagrant 64 Jan 26 20:33 255 -> /dev/pts/2
vagrant@vagrant:~$ ls kkkk 2>/dev/pts/2
```


Вывод в терминале 7715
```
vagrant@vagrant:~$ echo $$
7715
vagrant@vagrant:~$ ls: cannot access 'kkkk': No such file or directory
```
5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? 
Приведите работающий пример

Создадим два файла. 1.txt содержит команду ls -al для текущей деректории, файл 2.txt для вывода данных
```
vagrant@vagrant:~$ touch 1.txt
vagrant@vagrant:~$ touch 2.txt
vagrant@vagrant:~$ ls -al
total 64
drwxr-xr-x 6 vagrant vagrant 4096 Jan 26 20:51 .
drwxr-xr-x 3 root    root    4096 Dec 19 19:42 ..
-rw-rw-r-- 1 vagrant vagrant    0 Jan 26 20:50 1.txt
-rw-rw-r-- 1 vagrant vagrant    0 Jan 26 20:51 2.txt
```

Добавим в файл 1.txt команду ls -al
```
vagrant@vagrant:~$ cat 1.txt
ls -al
vagrant@vagrant:~$ ls -al
total 72
drwxr-xr-x 6 vagrant vagrant 4096 Jan 26 21:04 .
drwxr-xr-x 3 root    root    4096 Dec 19 19:42 ..
-rw-rw-r-- 1 vagrant vagrant    7 Jan 26 21:04 1.txt
-rw-rw-r-- 1 vagrant vagrant    0 Jan 26 21:04 2.txt
-rw------- 1 vagrant vagrant 6129 Jan 26 20:55 .bash_history
-rw-r--r-- 1 vagrant vagrant  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 vagrant vagrant 3771 Feb 25  2020 .bashrc
drwx------ 2 vagrant vagrant 4096 Dec 19 19:42 .cache
drwx------ 3 vagrant vagrant 4096 Jan 18 20:36 .config
-rw------- 1 vagrant vagrant  288 Jan 18 18:33 .lesshst
drwxrwxr-x 3 vagrant vagrant 4096 Jan 18 20:36 .local
-rw-r--r-- 1 vagrant vagrant  866 Jan 12 21:11 .profile
drwx------ 2 vagrant root    4096 Jan  8 16:37 .ssh
-rw-r--r-- 1 vagrant vagrant    0 Dec 19 19:42 .sudo_as_admin_successful
-rw-r--r-- 1 vagrant vagrant    6 Dec 19 19:42 .vbox_version
-rw------- 1 vagrant vagrant 8781 Jan 26 21:04 .viminfo
-rw-r--r-- 1 root    root     180 Dec 19 19:44 .wget-hsts

```
Выполним команду bash c направлением соответствующим потоков (0 и 1) в соответствующие файлы (1.txt  и 2.txt). 
Видим, что 2.txt изменил свой размер и внутри записан вывод команды ls -al.
```
vagrant@vagrant:~$ bash 0<1.txt 1>2.txt
vagrant@vagrant:~$ ls -al
total 76```
drwxr-xr-x 6 vagrant vagrant 4096 Jan 26 21:04 .Результат
drwxr-xr-x 3 root    root    4096 Dec 19 19:42 ..
-rw-rw-r-- 1 vagrant vagrant    7 Jan 26 21:04 1.txt```
-rw-rw-r-- 1 vagrant vagrant  965 Jan 26 21:05 2.txt```
-rw------- 1 vagrant vagrant 6129 Jan 26 20:55 .bash_history
-rw-r--r-- 1 vagrant vagrant  220 Feb 25  2020 .bash_logout6. 
-rw-r--r-- 1 vagrant vagrant 3771 Feb 25  2020 .bashrc7. 
drwx------ 2 vagrant vagrant 4096 Dec 19 19:42 .cache8. 
drwx------ 3 vagrant vagrant 4096 Jan 18 20:36 .config9. 
-rw------- 1 vagrant vagrant  288 Jan 18 18:33 .lesshst10. 
drwxrwxr-x 3 vagrant vagrant 4096 Jan 18 20:36 .local11. 
-rw-r--r-- 1 vagrant vagrant  866 Jan 12 21:11 .profile12. 
drwx------ 2 vagrant root    4096 Jan  8 16:37 .ssh13. 
-rw-r--r-- 1 vagrant vagrant    0 Dec 19 19:42 .sudo_as_admin_successful14. 
-rw-r--r-- 1 vagrant vagrant    6 Dec 19 19:42 .vbox_version15. 
-rw------- 1 vagrant vagrant 8781 Jan 26 21:04 .viminfo16. 
-rw-r--r-- 1 root    root     180 Dec 19 19:44 .wget-hsts17. man bash 

vagrant@vagrant:~$ cat 2.txt
total 72
drwxr-xr-x 6 vagrant vagrant 4096 Jan 26 21:04 .
drwxr-xr-x 3 root    root    4096 Dec 19 19:42 ..
-rw-rw-r-- 1 vagrant vagrant    7 Jan 26 21:04 1.txt
-rw-rw-r-- 1 vagrant vagrant    0 Jan 26 21:05 2.txt
-rw------- 1 vagrant vagrant 6129 Jan 26 20:55 .bash_history
-rw-r--r-- 1 vagrant vagrant  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 vagrant vagrant 3771 Feb 25  2020 .bashrc
drwx------ 2 vagrant vagrant 4096 Dec 19 19:42 .cache
drwx------ 3 vagrant vagrant 4096 Jan 18 20:36 .config
-rw------- 1 vagrant vagrant  288 Jan 18 18:33 .lesshst
drwxrwxr-x 3 vagrant vagrant 4096 Jan 18 20:36 .local
-rw-r--r-- 1 vagrant vagrant  866 Jan 12 21:11 .profile
drwx------ 2 vagrant root    4096 Jan  8 16:37 .ssh
-rw-r--r-- 1 vagrant vagrant    0 Dec 19 19:42 .sudo_as_admin_successful
-rw-r--r-- 1 vagrant vagrant    6 Dec 19 19:42 .vbox_version
-rw------- 1 vagrant vagrant 8781 Jan 26 21:04 .viminfo
-rw-r--r-- 1 root    root     180 Dec 19 19:44 .wget-hsts

```
Вариант использования более простой нотации:
- Создаем файлы
```
vagrant@vagrant:~$ touch 1.txt 2.txt
vagrant@vagrant:~$ ls -al
total 84
drwxr-xr-x 6 vagrant vagrant  4096 Jan 30 18:49 .
drwxr-xr-x 3 root    root     4096 Dec 19 19:42 ..
-rw-rw-r-- 1 vagrant vagrant     0 Jan 30 18:49 1.txt
-rw-rw-r-- 1 vagrant vagrant     0 Jan 30 18:49 2.txt
```
- Записываем в файл  строку 'ls -al'
```
vagrant@vagrant:~$ echo ls -al >1.txt
vagrant@vagrant:~$ ls -al
total 88
drwxr-xr-x 6 vagrant vagrant  4096 Jan 30 18:49 .
drwxr-xr-x 3 root    root     4096 Dec 19 19:42 ..
-rw-rw-r-- 1 vagrant vagrant     7 Jan 30 18:52 1.txt
-rw-rw-r-- 1 vagrant vagrant     0 Jan 30 18:49 2.txt
```
- Используем 1.txt как stdin и 2.txt как stdout
```
vagrant@vagrant:~$ cat <1.txt >2.txt
vagrant@vagrant:~$ cat 2.txt
ls -al
```
6. Получится ли находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY? 
Сможете ли вы наблюдать выводимые данные?
Непонятный вопрос. Не могу это смоделировать на своей машине. Нет Windows X.
Теоретически такой процесс возможен, т.к. есть пара   slave - master, почему бы не направить вывод master на TTY
Устройство dev/ptmx так и не смог запустить у себя с системе.

8. Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? 
Почему так происходит?

~~При выполнении команды 5>&1 создается новый файловый дескриптор со значением 5 и перенаправление выходного потока с 
данного дескриптора на страдартный вывод, т.е. дескриптор 1. 
Поэтому результат команды echo netology > /proc/$$/fd/5 появляется на экране.~~

При выполнении команды 5>&1 создается файловый дискриптор 5 аналогичный файловому дискриптору 1 (stdout),
т.е. FD 5 ссылается на файл терминала аналогично FD 1, при перенаправлении вывода на FD 5 
в команде `/dev/pts$ echo netology > /proc/$$/fd/5` мы увидим "netology" на экране терминал 
```
vagrant@vagrant:/dev/pts$ lsof -p $$
COMMAND   PID    USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
bash    11202 vagrant    0u   CHR  136,0      0t0       3 /dev/pts/0
bash    11202 vagrant    1u   CHR  136,0      0t0       3 /dev/pts/0
bash    11202 vagrant    2u   CHR  136,0      0t0       3 /dev/pts/0
bash    11202 vagrant    5u   CHR  136,0      0t0       3 /dev/pts/0
bash    11202 vagrant  255u   CHR  136,0      0t0       3 /dev/pts/0

vagrant@vagrant:/dev/pts$ echo netology > /proc/$$/fd/5
netology
```


9. Получится ли в качестве входного потока для pipe использовать только stderr команды, 
не потеряв при этом отображение stdout на pty? Напоминаем: по умолчанию через pipe передается только stdout команды слева 
от | на stdin команды справа. Это можно сделать, поменяв стандартные потоки 
местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.

Получится, можно перенаправить поток ошибок в поток вывода.
Дополнительных дескрипторов не нужно.

Деректория кккк не существует.
Укажем ее в командах ls | grep, grep найдет слово cannot
```
vagrant@vagrant:/dev$ ls -al  kkkk 2>&1 |grep cannot -c
1
vagrant@vagrant:/dev$ ls -al kkkk | grep cannot
ls: cannot access 'kkkk': No such file or directory
```
Уберем некорректную деректорию и далее поищем слово root. Видно, что отображение stdout на pty не потеряно.
```
vagrant@vagrant:/dev$ ls -al  2>&1 |grep cannot -c
0
vagrant@vagrant:/dev$ ls -al  2>&1 |grep root -c
197
```

10. Что выведет команда cat /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?
Команда cat /proc/$$/environ

Применим команду printenv, она показывает переменные окружения или их часть.

```
vagrant@vagrant:~$ printenv
SHELL=/bin/bash
PWD=/home/vagrant
LOGNAME=vagrant
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/home/vagrant
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
SSH_CONNECTION=10.0.2.2 50214 10.0.2.15 22
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm-256color
LESSOPEN=| /usr/bin/lesspipe %s
USER=vagrant
SHLVL=2
XDG_SESSION_ID=5
XDG_RUNTIME_DIR=/run/user/1000
SSH_CLIENT=10.0.2.2 50214 22
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
SSH_TTY=/dev/pts/0
OLDPWD=/etc
_=/usr/bin/printenv
```
11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo.

Командой cat /proc/cpuinfo | grep sse находим sse4_2
```
vagrant@vagrant:~$ vagrant@vagrant:~$ cat /proc/cpuinfo | grep sse
 sse4_2 hypervisor
```
12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. Это можно подтвердить командой tty, которая упоминалась в лекции 3.2. Однако:
```
vagrant@netology1:~$ ssh localhost 'tty'
not a tty
```
Почитайте, почему так происходит, и как изменить поведение.

Решение:
Так происходит, потому что терминал не запускает на хосте, к которому мы пытаемся подсоединиться. Протокол SSH 
старается запустить выполнить команду без запуска псевдо терминала.

Изменить поведение можно, добавив ключ -t, который запустит псевдо терминал.

```
   -t      Force pseudo-terminal allocation.  This can be used to execute arbitrary screen-based programs on a remote machine, which can be very useful, e.g. when implementing menu services.  Multiple
             -t options force tty allocation, even if ssh has no local tty.
```

```
vagrant@vagrant:~$ ssh -t localhost 'tty'
vagrant@localhost's password:
/dev/pts/3
Connection to localhost closed.
vagrant@vagrant:~$
```
13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись reptyr. 
Например, так можно перенести в screen процесс, который вы запустили по ошибке в обычной SSH-сессии.

Решение:

Разрешим отлаживать сессии пользователя.
```
vagrant@vagrant:~$ cat /proc/sys/kernel/yama/ptrace_scope
1
vagrant@vagrant:~$ sudo bash -c 'echo 0 > /proc/sys/kernel/yama/ptrace_scope'
vagrant@vagrant:~$ cat /proc/sys/kernel/yama/ptrace_scope
0
```
Теперь можно присоединить процесс. Используем ключ -T для успешной работы.

```
sshd(721)─┬─sshd(13288)───sshd(13330)───bash(13331)
           │           ├─sshd(14428)───sshd(14471)───bash(14472)───screen(14697)───screen(14698)───bash(14699)
           │           └─sshd(14484)───sshd(14526)───bash(14527)───pstree(14706)
vagrant@vagrant:~$ sudo reptyr -T 14699

─sshd(14484)───sshd(14526)───bash(14527)───sudo(14709)───reptyr(14710)
```

14. sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, 
так как перенаправлением занимается процесс shell'а, который запущен без sudo под вашим пользователем. 
Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file. 
Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать.

Строка sudo echo string > /root/new_file не отработает, т.к. echo запускается с правами root, а вот перенаправление 
уже запускается под обычными правами.

tee как раз может писать в файл и на экран и вот она уже запускает с правами root