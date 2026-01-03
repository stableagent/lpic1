# 103.6 اصلاح اولویت‌های اجرای پردازش (process execution priorities)

### **103.6 اصلاح اولویت‌های اجرای پردازش (process execution priorities)**

**وزن:** 2

**توضیحات:** داوطلبان باید قادر به مدیریت اولویت‌های اجرای پردازش باشند.

**حوزه‌های کلیدی دانش:**

* دانستن اولویت پیش‌فرض یک کار (job) که ایجاد می‌شود
* اجرای یک برنامه با اولویت بالاتر یا پایین‌تر از پیش‌فرض
* تغییر اولویت یک پردازش در حال اجرا

**اصطلاحات و ابزارهای کاربردی:**

* nice
* ps
* renice
* top

لینوکس، مانند اکثر سیستم‌عامل‌های مدرن، می‌تواند چندین پردازش را اجرا کند. این کار را با به اشتراک گذاشتن CPU و سایر منابع بین پردازش‌ها انجام می‌دهد. اگر یک پردازش بتواند از 100 درصد CPU استفاده کند، آنگاه سایر پردازش‌ها ممکن است بی‌پاسخ (unresponsive) شوند. ما شما را با روشی که لینوکس اولویت‌ها را برای وظایف تعیین می‌کند آشنا می‌کنیم (قبلاً در بخش قبل درباره دستورات ps و top صحبت کرده‌ایم).

### nice

در لینوکس ما می‌توانیم دستورالعمل‌هایی را برای CPU تعیین کنیم تا هنگام بررسی تمام کارهایی که باید انجام دهد، از آن‌ها پیروی کند. این دستورالعمل‌ها _**niceness**_ یا _**nice value**_ نامیده می‌شوند.

```
top - 03:15:57 up 3 days, 20:17,  1 user,  load average: 0.03, 0.01, 0.00
Tasks: 235 total,   1 running, 171 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.1 us,  1.4 sy,  0.0 ni, 96.2 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :   985080 total,   116124 free,   436040 used,   432916 buff/cache
KiB Swap:  1045500 total,   374404 free,   671096 used.   335984 avail Mem 

   PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND     
   949 root      20   0  496000  30668  15864 S  2.0  3.1  11:17.57 Xorg        
 55222 user1     20   0  667544  18964  12132 S  1.3  1.9   0:22.76 gnome-term+ 
  2112 user1     20   0 2346400  53212  24204 S  0.7  5.4  36:38.03 compiz      
  1916 user1     20   0  477868   5304   3464 S  0.3  0.5   0:07.53 ibus-ui-gt+ 
  1948 user1     20   0  188388   1384   1108 S  0.3  0.1   0:18.18 ibus-engin+ 
 55210 root      20   0    6536    600    572 S  0.3  0.1   0:19.60 ping        
 64405 root      20   0   41920   3760   3040 R  0.3  0.4   0:00.13 top         
     1 root      20   0  185244   3996   2500 S  0.0  0.4   0:08.10 systemd 
```

**NI column ** Represents a Nice Value of task. The Linux niceness scale goes from -20 to 19. The lower the number the more priority that task gets. If the niceness value is high number like 19 the task will be set to the lowest priority and the CPU will process it whenever it gets a chance. The default nice value is **zero**.

![](.gitbook/assets/modifyprocess-nice.jpg)

Different OS distributions can have different default values for new processes. The simplest method to determine the default value is to simply run the nice command with no arguments. By default nice will simply return the current niceness value

```
root@ubuntu16-1:~# nice
0
```

#### تعیین مقدار niceness یک پردازش فعلی <a href="determining-the-niceness-value-of-a-current-process" id="determining-the-niceness-value-of-a-current-process"></a>

مقدار niceness پردازش‌های فعلی نیز به سادگی قابل یافتن است زیرا در قالب کامل و طولانی دستور ps قابل مشاهده است:

```
root@ubuntu16-1:~# sleep 11111 &
[1] 65128
root@ubuntu16-1:~# sleep 22222 &
[2] 65129
root@ubuntu16-1:~# ps -fl
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 wait   Oct21 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 wait   Oct21 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 wait   04:36 pts/17   00:00:00 su - user1
4 S root      65097  65015  0  80   0 - 13594 wait   04:45 pts/17   00:00:00 su - root
4 S root      65098  65097  0  80   0 -  5691 wait   04:45 pts/17   00:00:00 -su
0 S root      65128  65098  0  80   0 -  1822 hrtime 04:48 pts/17   00:00:00 sleep 11111
0 S root      65129  65098  0  80   0 -  1822 hrtime 04:48 pts/17   00:00:00 sleep 22222
0 R root      65133  65098  0  80   0 -  9341 -      04:49 pts/17   00:00:00 ps -fl
```

> `-f` do full-format listing and  `-l `is for Long format.

#### تغییر مقدار nice یک پردازش جدید <a href="changing-the-nice-value-of-a-new-process" id="changing-the-nice-value-of-a-new-process"></a>

Changing the niceness value of a new process is fairly simple. The nice command itself will run the supplied command with the desired niceness value.(Please note we are logged in  as a user here)

```
user1@ubuntu16-1:~$ sleep 11111 &
[1] 65043
user1@ubuntu16-1:~$ nice -n 10 sleep 22222 &
[2] 65046
user1@ubuntu16-1:~$ nice -n 19 sleep 33333 &
[3] 65047
user1@ubuntu16-1:~$ nice -n -10 sleep 44444 &
[4] 65048
user1@ubuntu16-1:~$ nice: cannot set niceness: Permission denied
```

توجه: root user is the only person who can start an application with the high priority  (lower than zero), but, any body can start an application with low priority (higher than zero).

if we try to run an application with high priority without root permissions, it would trough an error and starts application with priority zero.

```
user1@ubuntu16-1:~$ ps -fl
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
0 S user1     55624  55222  0  80   0 -  5669 wait   Oct21 pts/17   00:00:00 bash
4 S user1     65015  65014  0  80   0 -  5677 wait   04:36 pts/17   00:00:00 -su
0 S user1     65043  65015  0  80   0 -  1822 hrtime 04:36 pts/17   00:00:00 sleep 11111
0 S user1     65046  65015  0  90  10 -  1822 hrtime 04:37 pts/17   00:00:00 sleep 22222
0 S user1     65047  65015  0  99  19 -  1822 hrtime 04:37 pts/17   00:00:00 sleep 33333
0 S user1     65048  65015  0  80   0 -  1822 hrtime 04:37 pts/17   00:00:00 sleep 44444
0 R user1     65085  65015  0  80   0 -  9341 -      04:40 pts/17   00:00:00 ps -fl
```

حالا بیایید دستور آخر را با استفاده از کاربر root امتحان کنیم:

```
user1@ubuntu16-1:~$ su - root
Password: 
root@ubuntu16-1:~# nice -n -20 sleep 55555 &
[1] 65112
root@ubuntu16-1:~# ps -fl
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 wait   Oct21 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 wait   Oct21 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 wait   04:36 pts/17   00:00:00 su - user1
4 S root      65097  65015  0  80   0 - 13594 wait   04:45 pts/17   00:00:00 su - root
4 S root      65098  65097  0  80   0 -  5691 wait   04:45 pts/17   00:00:00 -su
4 S root      65112  65098  0  60 -20 -  1822 hrtime 04:45 pts/17   00:00:00 sleep 55555
0 R root      65113  65098  0  80   0 -  9341 -      04:45 pts/17   00:00:00 ps -fl
```

خوب است بدانید که دستور nice دارای سه سینتکس متفاوت است:

```
$ nice -n niceness-value [command args] 
OR
$ nice -niceness-value [command args] 	#it’s confusing for negative values
OR
$ nice --adjustment=niceness-value [command args]
```

| nice command example  | Description      |
| --------------------- | ---------------- |
| nice --20 application | بالاترین اولویت |
| nice --15 application | بسیار بالا |
| nice -10 application  | متوسط رو به پایین |
| nice -19 application  | پایین‌ترین اولویت |

#### تغییر مقدار nice یک پردازش در حال اجرا <a href="changing-the-nice-value-of-a-running-process" id="changing-the-nice-value-of-a-running-process"></a>

### renice

To change the niceness of a running process to a negative value we will use the `renice `command again.

```
renice value PID
```

مهم است توجه داشته باشید که تغییر مقدار niceness یک پردازش به مقدار منفی نیاز به امتیازات root دارد. زیرا اثرات دادن اولویت بالاتر به یک پردازش می‌تواند اثرات مضری بر سیستم داشته باشد.

```
user1@ubuntu16-1:~$ sleep 88888 &
[1] 67534
user1@ubuntu16-1:~$ ps -alf
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 -      Oct22 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 -      Oct22 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 -      Oct22 pts/17   00:00:00 su - user1
4 S user1     65015  65014  0  80   0 -  5677 wait   Oct22 pts/17   00:00:00 -su
0 S user1     67534  65015  0  80   0 -  1822 hrtime 05:59 pts/17   00:00:00 sleep 88888
0 R user1     67535  65015  0  80   0 -  9341 -      05:59 pts/17   00:00:00 ps -alf
user1@ubuntu16-1:~$ renice 10 67534
67534 (process ID) old priority 0, new priority 10
user1@ubuntu16-1:~$ ps -alf
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 -      Oct22 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 -      Oct22 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 -      Oct22 pts/17   00:00:00 su - user1
4 S user1     65015  65014  0  80   0 -  5677 wait   Oct22 pts/17   00:00:00 -su
0 S user1     67534  65015  0  90  10 -  1822 hrtime 05:59 pts/17   00:00:00 sleep 88888
0 R user1     67539  65015  0  80   0 -  9341 -      05:59 pts/17   00:00:00 ps -alf
user1@ubuntu16-1:~$ renice 5 67534
renice: failed to set priority for 67534 (process ID): Permission denied
```

> کاربر فقط می‌تواند سطح nice را افزایش دهد (اولویت را کم کند).

```
user1@ubuntu16-1:~$ su - root
Password: 
root@ubuntu16-1:~# renice -1 67534
67534 (process ID) old priority 10, new priority -1
root@ubuntu16-1:~# ps -alf
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 wait   Oct22 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 wait   Oct22 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 wait   Oct22 pts/17   00:00:00 su - user1
4 S user1     65015  65014  0  80   0 -  5677 wait   Oct22 pts/17   00:00:00 -su
0 S user1     67534  65015  0  79  -1 -  1822 hrtime 05:59 pts/17   00:00:00 sleep 88888
4 S root      67541  65015  0  80   0 - 13594 wait   06:00 pts/17   00:00:00 su - root
4 S root      67542  67541  0  80   0 -  5691 wait   06:00 pts/17   00:00:00 -su
4 R root      67558  67542  0  80   0 -  9341 -      06:00 pts/17   00:00:00 ps -alf
```

ما همچنین می‌توانیم از گزینه -p قبل از دادن PID استفاده کنیم، اما ضروری نیست.

| renice command example |                  |
| ---------------------- | ---------------- |
| renice -20 -p PID      | بالاترین اولویت |
| renice -15 -p PID      | بسیار بالا        |
| renice 10 -p PID       | متوسط رو به پایین |
| renice 19 -p PID       | پایین‌ترین اولویت |

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-103-6/](https://developer.ibm.com/tutorials/l-lpic1-103-6/)

[https://www.nixtutor.com/linux/changing-priority-on-linux-processes/](https://www.nixtutor.com/linux/changing-priority-on-linux-processes/)

[https://bencane.com/2013/09/09/setting-process-cpu-priority-with-nice-and-renice/](https://bencane.com/2013/09/09/setting-process-cpu-priority-with-nice-and-renice/)

[https://www.tecmint.com/set-linux-process-priority-using-nice-and-renice-commands/](https://www.tecmint.com/set-linux-process-priority-using-nice-and-renice-commands/)

.
