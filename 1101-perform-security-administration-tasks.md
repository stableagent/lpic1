# 110.1 انجام وظایف مدیریت امنیت

## **110.1 انجام وظایف مدیریت امنیت**

**وزن:** ۳

**توضیحات:** داوطلبان باید بدانند چگونه پیکربندی سیستم را بررسی کنند تا از امنیت هاست مطابق با سیاست‌های امنیتی محلی اطمینان حاصل کنند.

**حوزه‌های کلیدی دانش:**

* ممیزی (Audit) سیستم برای یافتن فایل‌های دارای بیت suid/sgid
* تنظیم یا تغییر رمز عبور کاربران و اطلاعات طول عمر رمز عبور
* توانایی استفاده از nmap و netstat برای شناسایی پورت‌های باز در یک سیستم
* تنظیم محدودیت‌ها برای ورود کاربران، فرآیندها و استفاده از حافظه
* تعیین اینکه کدام کاربران به سیستم وارد شده‌اند یا در حال حاضر لاگین هستند
* پیکربندی و استفاده پایه از sudo

**اصطلاحات و ابزارهای کاربردی:**

* find
* passwd
* fuser
* lsof
* nmap
* chage
* netstat
* sudo
* /etc/sudoers
* su
* usermod
* ulimit
* who, w, last

در این درس، ما فقط نگاهی به ممیزی‌های امنیتی پایه می‌اندازیم. ابتدا چندین دستور را که قبلاً یاد گرفته‌ایم از منظر امنیتی مرور می‌کنیم و سپس با چند دستور جدید دیگر آشنا می‌شویم.

## یافتن suid/sgid 

ما قبلاً در مورد suid/sgid در بخش مدیریت مجوزهای فایل و مالکیت صحبت کرده‌ایم؛ برای مرور سریع جدول زیر را ببینید:

| حالت دسترسی    | ** روی فایل**                            | **روی دایرکتوری**                             |
| -------------- | --------------------------------------- | -------------------------------------------- |
| **SUID**       | با مجوزهای مالک فایل اجرا می‌شود | هیچ‌چیز                                      |
| **GUID**       | با مجوزهای گروه اجرا می‌شود  | فایل‌های جدید عضویت گروهی دایرکتوری را می‌گیرند |
| **Sticky Bit** | هیچ‌چیز                                 | فقط مالک می‌تواند فایل‌ها را حذف کند                  |

نگرانی‌های امنیتی در استفاده از suid/sgid وجود دارد؛ مثلاً چه اتفاقی می‌افتد اگر یک برنامه مخرب دارای مجوز suid/sgid باشد؟ چرا برنامه‌های خطرناکی مانند `rm` باید مجوز suid داشته باشند؟ برای جستجوی تمام فایل‌های suid/sgid از دستور `find` استفاده می‌کنیم:

> sudo find / -perm -u+s
>
> sudo find / -perm -g+s

```
root@ubuntu16-1:~# find / -perm -u+s
/bin/ping
/bin/fusermount
/bin/ping6
/bin/mount
/bin/su
/bin/ntfs-3g
/bin/umount
find: ‘/run/user/1001/gvfs’: Permission denied
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/chfn
/usr/lib/x86_64-linux-gnu/oxide-qt/chrome-sandbox
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/xorg/Xorg.wrap
/usr/sbin/pppd
...

root@ubuntu16-1:~# find / -perm -g+s
find: ‘/run/user/1001/gvfs’: Permission denied
/run/log/journal
/run/log/journal/b4f9fc6cf1ca4724b56e6e4235c77155
/usr/bin/wall
/usr/bin/crontab
/usr/bin/bsd-write
/usr/bin/chage
/usr/bin/expiry
/usr/bin/ssh-agent
/usr/bin/mlocate
/usr/share/ppd/custom
...
```

بدیهی است که بررسی تک‌تک این فایل‌ها و فهمیدن عملکرد آن‌ها فراتر از محدوده این دوره است، اما باید چشمان خود را باز نگه داریم تا ببینیم آیا هر یک از این‌ها غیرمنطقی هستند یا خیر (مثلاً موردی در دایرکتوری خانگی کاربران یافت شود). توصیه می‌شود این لیست را برای مقایسه در آینده و شناسایی تغییرات جدید ذخیره کنید.

## جستجوی پورت‌های باز

مهم است که بررسی کنید کدام پورت‌ها در رابط‌های شبکه سرور در حال گوش دادن (listening) هستند. در زیر دسته‌بندی‌های مختلف پورت‌ها آمده است:

1. **۰-۱۰۲۳** – پورت‌های شناخته شده (Well Known Ports) که به آن‌ها پورت‌های سیستم نیز گفته می‌شود.
2. **۱۰۲۴-۴۹۱۵۱** – پورت‌های ثبت شده (Registered Ports) که به نام پورت‌های کاربر نیز شناخته می‌شوند.
3. **۴۹۱۵۲-۶۵۵۳۵** – پورت‌های پویا (Dynamic Ports) که به آن‌ها پورت‌های خصوصی نیز گفته می‌شود.

 ما باید به پورت‌های باز توجه کنیم تا نفوذهای احتمالی را شناسایی کنیم. جدا از بحث نفوذ، برای اهداف عیب‌یابی نیز ممکن است لازم باشد بررسی کنیم که آیا یک پورت قبلاً توسط اپلیکیشن دیگری در سرور ما استفاده شده است یا خیر. به عنوان مثال، ممکن است سرورهای Apache و Nginx را روی یک سیستم نصب کنیم!

 این بخش مراحلی را برای استفاده از دستورات netstat، lsof و nmap جهت بررسی پورت‌های در حال استفاده و مشاهده اپلیکیشنی که از آن پورت استفاده می‌کند، ارائه می‌دهد.

### netstat

 یکی از کاربردهای ابزار خط فرمان **netstat** مانیتورینگ اتصالات ورودی و خروجی شبکه است. به طور پیش‌فرض، netstat لیستی از سوکت‌های باز را نمایش می‌دهد که خیلی مفید نیست، بنابراین معمولاً از آن به همراه سوئیچ‌های `-tuna` استفاده می‌کنیم.

| سوئیچ netstat | کاربرد                                                                                     |
| -------------- | ----------------------------------------------------------------------------------------- |
| -t             | نمایش پورت‌های TCP                                                                            |
| -u             | نمایش پورت‌های UDP                                                                            |
| -n             | نمایش آدرس‌های عددی به جای تلاش برای تعیین نام نمادین هاست، پورت یا کاربر |
| -a             | نمایش هر دو حالت گوش دادن (listening) و غیر گوش دادن (برای TCP به معنای اتصالات برقرار شده است)        |

```
root@ubuntu16-1:~# netstat -tuna
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN     
tcp        0      0 192.168.52.144:41270    172.217.0.227:80        ESTABLISHED
tcp        0      0 192.168.52.144:43418    72.21.91.29:80          TIME_WAIT  
tcp        0      0 192.168.52.144:43416    72.21.91.29:80          TIME_WAIT  
tcp        0      0 127.0.0.1:631           127.0.0.1:52720         ESTABLISHED
...
udp        0      0 0.0.0.0:45821           0.0.0.0:*                          
udp    14400      0 0.0.0.0:51238           0.0.0.0:*                          
udp    11520      0 127.0.1.1:53            0.0.0.0:*                          
udp    10880      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 0.0.0.0:631             0.0.0.0:*                          
udp    24576      0 192.168.52.255:137      0.0.0.0:*                          
udp        0      0 192.168.52.144:137      0.0.0.0:*                          
udp    52224      0 0.0.0.0:137             0.0.0.0:*                          
udp    34560      0 192.168.52.255:138      0.0.0.0:*                          
udp        0      0 192.168.52.144:138      0.0.0.0:*                          
udp     7680      0 0.0.0.0:138             0.0.0.0:*                          
udp     6144      0 0.0.0.0:5353            0.0.0.0:*                          
...
```

 قبل از اینکه یک اتصال TCP برقرار شود، نیاز به سروری با یک شنونده (listener) داریم. شنونده روی یک پورت خاص منتظر اتصالات ورودی می‌ماند؛ این وضعیت با `LISTEN` نشان داده می‌شود. اگر همه چیز به درستی کار کند، اتصال در هر دو نقطه انتهایی به عنوان `ESTABLISHED` علامت‌گذاری می‌شود. در این جداول، `0.0.0.0` به معنای "هر آدرسی" یا "هر رابطی" است.

### lsof

 **lsof** مخفف **‘LiSt Open Files’** است و برای فهمیدن اینکه کدام فایل‌ها توسط کدام فرآیند باز شده‌اند استفاده می‌شود. همانطور که می‌دانیم در لینوکس همه چیز یک فایل است، بنابراین حتی می‌توانیم با استفاده از دستور `lsof` و سوئیچ `-i` ، فایل‌هایی را که توسط اتصالات شبکه در سیستم باز شده‌اند بررسی کنیم:

```
root@ubuntu16-1:~# lsof -i
COMMAND     PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
avahi-dae   791  avahi   12u  IPv4  24139      0t0  UDP *:mdns 
avahi-dae   791  avahi   13u  IPv6  24140      0t0  UDP *:mdns 
avahi-dae   791  avahi   14u  IPv4  24141      0t0  UDP *:45821 
avahi-dae   791  avahi   15u  IPv6  24142      0t0  UDP *:57596 
mysqld      967  mysql   27u  IPv4  27331      0t0  TCP localhost:mysql (LISTEN)
sshd        979   root    3u  IPv4 468063      0t0  TCP *:ssh (LISTEN)
sshd        979   root    4u  IPv6 468070      0t0  TCP *:ssh (LISTEN)
nmbd       1745   root   16u  IPv4  31466      0t0  UDP *:netbios-ns 
nmbd       1745   root   17u  IPv4  31467      0t0  UDP *:netbios-dgm 
nmbd       1745   root   21u  IPv4 468314      0t0  UDP 192.168.52.136:netbios-ns 
nmbd       1745   root   24u  IPv4 468315      0t0  UDP 192.168.52.255:netbios-ns 
nmbd       1745   root   25u  IPv4 468316      0t0  UDP 192.168.52.136:netbios-dgm 
nmbd       1745   root   26u  IPv4 468317      0t0  UDP 192.168.52.255:netbios-dgm 
smbd       1761   root   34u  IPv6  31629      0t0  TCP *:microsoft-ds (LISTEN)
smbd       1761   root   35u  IPv6  31630      0t0  TCP *:netbios-ssn (LISTEN)
smbd       1761   root   36u  IPv4  31631      0t0  TCP *:microsoft-ds (LISTEN)
smbd       1761   root   37u  IPv4  31632      0t0  TCP *:netbios-ssn (LISTEN)
cupsd      3683   root   10u  IPv6  41942      0t0  TCP ip6-localhost:ipp (LISTEN)
cupsd      3683   root   11u  IPv4  41943      0t0  TCP localhost:ipp (LISTEN)
cups-brow  3685   root    8u  IPv4  41958      0t0  UDP *:ipp 
gvfsd-smb 14071  user1   13u  IPv4 465267      0t0  TCP 192.168.52.144:60122->192.168.52.144:netbios-ssn (ESTABLISHED)
gvfsd-smb 14071  user1   14u  IPv4 465449      0t0  TCP 192.168.52.144:60124->192.168.52.144:netbios-ssn (ESTABLISHED)
dnsmasq   14148 nobody    4u  IPv4 466564      0t0  UDP ubuntu:domain 
dnsmasq   14148 nobody    5u  IPv4 466565      0t0  TCP ubuntu:domain (LISTEN)
dnsmasq   14148 nobody   11u  IPv4 466600      0t0  UDP *:44999 
dhclient  14166   root    6u  IPv4 466685      0t0  UDP *:bootpc 
```

این دستور نام برنامه، PID، کاربر اجراکننده، و IP مبدأ و مقصد را نشان می‌دهد و می‌گوید که آیا این یک اتصال LISTENING است یا ESTABLISHED.

| سوئیچ lsof      | کاربرد                                                 |
| ---------------- | ----------------------------------------------------- |
| -iTCP _یا_ -iUDP | فقط اتصالات TCP یا UDP را نشان بده                      |
| -i 4 _یا_ -i 6   | نمایش جداگانه فایل‌های IPv4 و IPv6 |
| -n               | از نام‌های DNS استفاده نکن                                   |
| -P               | شماره پورت‌ها را به نام پورت تبدیل نکن             |

 اگر بخواهیم بررسی کنیم کدام فرآیند از یک پورت خاص استفاده می‌کند، می‌توانیم خروجی دستورات بالا را `grep` کنیم یا به سادگی از دستور `fuser` استفاده کنیم.

### fuser

دستور `fuser` یک ابزار بسیار هوشمند است که برای یافتن فرآیندی که از یک فایل، دایرکتوری یا سوکت استفاده می‌کند، به کار می‌رود. 

دستور زیر یک شنونده TCP روی پورت ۸۰۸۰ ایجاد می‌کند:

```
root@ubuntu16-1:~# nc -l -p 8080

```

از آنجایی که یک سرور TCP روی پورت ۸۰۸۰ در حال گوش دادن است، می‌توان از ابزار `fuser` برای یافتن فرآیندی که از سوکت سرور استفاده می‌کند استفاده کرد. گزینه `-v` برای قرار دادن ابزار در حالت verbose و گزینه `-n` برای انتخاب پروتکل TCP به عنوان فضای نام (namespace) استفاده می‌شود:

```
root@ubuntu16-1:~# fuser -v -n tcp 8080
                     USER        PID ACCESS COMMAND
8080/tcp:            root      15663 F.... nc
```

به طور پیش‌فرض، ابزار `fuser` در هر دو سوکت IPv6 و IPv4 جستجو می‌کند، اما این رفتار را می‌توان با گزینه‌های -4 و -6 تغییر داد. 

### nmap  

 **Nmap** که به عنوان **Network Mapper** نیز شناخته می‌شود، یک ابزار متن‌باز و بسیار همه‌کاره برای مدیران سیستم و شبکه در لینوکس است. **Nmap** برای **جستجوی شبکه‌ها**، **انجام اسکن‌های امنیتی**، **ممیزی شبکه** و **یافتن پورت‌های باز** در ماشین‌های محلی یا راه دور استفاده می‌شود.

{% hint style="danger" %}
لطفاً توجه داشته باشید که اسکن وب‌سایت‌ها با Nmap غیرقانونی است؛ در برخی موارد اگر سعی کنید اسکن‌های عمیقی انجام دهید، به اجازه کتبی از مالک وب‌سایت و دارنده IP نیاز خواهید داشت.
{% endhint %}

```
root@ubuntu16-1:~# nmap localhost

Starting Nmap 7.01 ( https://nmap.org ) at 2020-03-18 00:47 +0330
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000025s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
631/tcp  open  ipp
3306/tcp open  mysql

Nmap done: 1 IP address (1 host up) scanned in 1.62 seconds
```

به طور **پیش‌فرض**، **Nmap** ۱۰۰۰ پورت رایج برای هر پروتکل را اسکن می‌کند. 

| انتخاب هدف در nmap     | توضیحات                   |
| ------------------------- | ----------------------------- |
| nmap 192.168.10.151       | اسکن یک IP واحد              |
| nmap scanme.nmap.org      | اسکن یک هاست                   |
| nmap 192.168.10.150-155   | اسکن یک محدوده از IPها           |
| nmap 192.168.10.0/24      | اسکن یک زیرشبکه (subnet)                 |
| nmap -iL myserverlist.txt | اسکن اهداف از یک فایل متنی |
| nmap -6 [IP-V6-HERE]     | فعال کردن اسکن IPv6        |

nmap سوئیچ‌های زیادی برای کسب اطلاعات بیشتر درباره هاست‌ها دارد.

| سوئیچ nmap   | کاربرد                                          |
| ------------- | ---------------------------------------------- |
| -v            |  ارائه اطلاعات دقیق‌تر               |
| -p_ \<port#>_ | اسکن برای کسب اطلاعات در مورد یک پورت خاص |
| -A            | شناسایی اطلاعات سیستم‌عامل      |
| -O            | نمایش اطلاعات بیشتر در مورد سیستم‌عامل    |

## بررسی پیکربندی sudo 

#### su در مقابل sudo

sudo و su دستورات بسیار مهم و پرکاربرد در لینوکس هستند. برای یک کاربر لینوکس بسیار مهم است که این دو را برای افزایش امنیت و جلوگیری از اتفاقات غیرمنتظره درک کند. ابتدا خواهیم دید که این دستورات چه کاری انجام می‌دهند و سپس تفاوت بین آن‌ها را یاد می‌گیریم.

{% hint style="info" %}
قبل از شروع، در برخی توزیع‌ها مانند اوبونتو، رمز عبور پیش‌فرض root هنگام نصب سیستم‌عامل تنظیم نمی‌شود، بنابراین ابتدا آن را با دستور `sudo passwd root` تنظیم کنید.
{% endhint %}

### su

دستور `su` در لینوکس برای سوئیچ از یک حساب به حساب دیگر استفاده می‌شود. از کاربر رمز عبور حسابی که به آن سوئیچ می‌کند، خواسته خواهد شد.

```
user1@ubuntu16-1:~$ su payam
Password: 
payam@ubuntu16-1:/home/user1$ 
```

کاربران همچنین می‌توانند از `su` برای سوئیچ به حساب root استفاده کنند. اگر کاربر فقط `su` را بدون هیچ گزینه‌ای تایپ کند، به عنوان درخواست برای root در نظر گرفته می‌شود و از او رمز عبور کاربر root خواسته خواهد شد.

```
payam@ubuntu16-1:/home/user1$ su 
Password: 
root@ubuntu16-1:/home/user1# pwd
/home/user1
root@ubuntu16-1:/home/user1# exit
exit
payam@ubuntu16-1:
```

{% hint style="info" %}
**تفاوت بین 'su' و '- su' چیست؟**

تفاوت در متغیرهای محیطی (environment variables) است. `- su` محیط را تغییر می‌دهد، اما `su` خیر. `su` محیط کاربر قدیمی/اصلی را حتی پس از سوئیچ به root حفظ می‌کند، در حالی که `- su` یک محیط جدید ایجاد می‌کند (همانطور که در `~/.bashrc` کاربر root مشخص شده است)، مشابه حالتی که مستقیماً به عنوان کاربر root لاگین می‌کنید.

```
payam@ubuntu16-1:/home/user1$ su -
Password: 
root@ubuntu16-1:~# pwd
/root
```

لطفاً توجه داشته باشید که سوئیچ‌های `-` ، `-l` و `--login` همگی یکسان هستند.
{% endhint %}

### sudo

همانطور که می‌دانیم، لینوکس از راه‌های زیادی از کامپیوتر کاربران در برابر استفاده‌های سوء توسط افراد بدطینت محافظت می‌کند. استفاده از `sudo` یکی از آن راه‌های خوب است. هر زمان که یک کاربر بخواهد نرم‌افزاری را نصب، حذف یا تغییر دهد، باید امتیازات root را برای انجام چنین کارهایی داشته باشد. دستور `sudo` برای دادن چنین مجوزهایی به هر دستور خاصی که کاربر می‌خواهد اجرا کند، استفاده می‌شود. `sudo` از کاربر می‌خواهد رمز عبور خود را وارد کند تا مجوزهای سیستمی را دریافت کند. به عنوان مثال، کاربر می‌خواهد سیستم‌عامل را با دستور زیر به‌روزرسانی کند:

```
payam@ubuntu16-1:~$ apt-get update
Reading package lists... Done
W: chmod 0700 of directory /var/lib/apt/lists/partial failed - SetupAPTPartialDirectory (1: Operation not permitted)
E: Could not open lock file /var/lib/apt/lists/lock - open (13: Permission denied)
E: Unable to lock directory /var/lib/apt/lists/
W: Problem unlinking the file /var/cache/apt/pkgcache.bin - RemoveCaches (13: Permission denied)
W: Problem unlinking the file /var/cache/apt/srcpkgcache.bin - RemoveCaches (13: Permission denied)
```

این خطا به دلیل نداشتن امتیازات root توسط کاربر ‘payam’ است. امتیازات root را می‌توان با آوردن `sudo` در ابتدای دستور به دست آورد، مانند زیر:

```
payam@ubuntu16-1:~$ sudo apt-get update
[sudo] password for payam: 
Hit:1 http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial InRelease                         
Hit:2 http://archive.ubuntu.com/ubuntu xenial InRelease
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [109 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [107 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-security InRelease [109 kB]
0% [5 InRelease 240 B/109 kB 0%]
...
```

### /etc/sudoers

اما `sudo` از کجا می‌داند چه کسی باید اجازه root داشته باشد؟ کدام دستور می‌تواند تحت امتیازات root اجرا شود؟ `sudo` تنظیمات خود را در فایل `/etc/sudoers` نگه می‌دارد:

```
root@ubuntu16-1:~# cat /etc/sudoers
#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
#
# See the man page for details on how to write a sudoers file.
#
Defaults    env_reset
Defaults    mail_badpass
Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"

# Host alias specification

# User alias specification

# Cmnd alias specification

# User privilege specification
root    ALL=(ALL:ALL) ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo    ALL=(ALL:ALL) ALL

# See sudoers(5) for more information on "#include" directives:

#includedir /etc/sudoers.d
```

 سینتکس یک قانون در فایل `sudoers` به این صورت است:

> user (host)=(user:group) commands

۳ خط مهم:  

* `(root ALL=(ALL) ALL)` به root اجازه می‌دهد هر کاری را در هر ماشینی به عنوان هر کاربری انجام دهد. 
*  `(%admin ALL=(ALL) ALL)` به هر کسی در گروه `admin` اجازه می‌دهد هر چیزی را به عنوان هر کاربری اجرا کند. 
* `%sudo ALL=(ALL:ALL) ALL` تمام کاربران در گروه `sudo` امتیاز اجرای هر دستوری را دارند.

> نکته: در CentOS، به جای گروه `sudo` اغلب گروه **wheel** دیده می‌شود.

{% hint style="info" %}
#### تفاوت بین گروه wheel/sudo و کاربر sudo

در CentOS و Debian، کاربری که به گروه wheel/sudo تعلق دارد می‌تواند `su` را اجرا کرده و مستقیماً به root تبدیل شود. در همین حال، یک کاربر sudo ابتدا باید از `sudo su` استفاده کند. در واقع تفاوت واقعی به جز سینتکس استفاده شده برای **تبدیل شدن به root** وجود ندارد و کاربران هر دو گروه می‌توانند از دستور `sudo` استفاده کنند.
{% endhint %}

**چگونه فایل `/etc/sudoers` را ویرایش کنیم؟** اگر از یک ویرایشگر معمولی استفاده کنید، سینتکس را اشتباه بنویسید و ذخیره کنید... `sudo` (احتمالاً) دیگر کار نخواهد کرد و از آنجایی که `/etc/sudoers` فقط توسط root قابل تغییر است، شما به مشکل خواهید خورد! بنابراین ما در عوض از **visudo** استفاده می‌کنیم. `visudo` فایل `sudoers` را به روشی ایمن و با انجام دو کار ویرایش می‌کند:

* `visudo` سینتکس فایل را **قبل** از بازنویسی واقعی فایل `sudoers` بررسی می‌کند. 
* علاوه بر این، `visudo` فایل `sudoers` را در برابر چندین ویرایش همزمان قفل می‌کند. این قفل کردن زمانی مهم است که بخواهید اطمینان حاصل کنید هیچ‌کس دیگری نمی‌تواند تغییرات پیکربندی شما را به هم بزند.

## مدیریت منابع سیستم

سیستم‌عامل‌های لینوکس این قابلیت را دارند که میزان منابع مختلف سیستم را که در اختیار یک فرآیند کاربر قرار می‌گیرد، محدود کنند. این محدودیت‌ها شامل مواردی چون تعداد فایل‌هایی که یک فرآیند می‌تواند باز کند، حداکثر اندازه فایلی که کاربر می‌تواند ایجاد کند و میزان حافظه‌ای که اجزای مختلف فرآیند می‌توانند استفاده کنند، می‌شود. **ulimit** دستوری است که برای انجام این کار استفاده می‌شود.

### ulimit

دستور `ulimit` امکان کنترل منابع موجود برای شل و/یا فرآیندهای شروع شده توسط آن را فراهم می‌کند.

```
user1@ubuntu16-1:~$ ulimit 
unlimited
```

برای دریافت گزارش با جزئیات، فلگ `-a` را در انتها اضافه کنید. این کار تمام محدودیت‌های منابع را برای کاربر فعلی چاپ می‌کند.

![](.gitbook/assets/performsecadmin-ulimit.jpg)

برای تنظیم مقدار ulimit روی یک پارامتر از دستور زیر استفاده کنید:

`ulimit -<letter Option> <NewValue>`

به عنوان مثال، بیایید محدودیت‌هایی را برای اندازه فایل در شل فعلی تنظیم کنیم:

```
user1@ubuntu16-1:~$ ulimit -f 0

user1@ubuntu16-1:~$ ulimit -a | grep file
core file size          (blocks, -c) 0
file size               (blocks, -f) 0
open files                      (-n) 1024
file locks                      (-x) unlimited

user1@ubuntu16-1:~$ vim new.txt 
Vim: Caught deadly signal XFSZ
Vim: Finished.



File size limit exceeded (core dumped)
```

 برای اینکه تنظیمات `ulimit` پس از ریبوت باقی بمانند، باید مقادیر آن را در فایل پیکربندی **/etc/security/limits.conf** تنظیم کنیم. این فایل همچنین برای محدودیت‌های سراسری سیستم استفاده می‌شود:

```
root@ubuntu16-1:~# cat /etc/security/limits.conf 
# /etc/security/limits.conf
#
#Each line describes a limit for a user in the form:
#
#<domain>        <type>  <item>  <value>
#
#Where:
#<domain> can be:
#        - a user name
#        - a group name, with @group syntax
#        - the wildcard *, for default entry
#        - the wildcard %, can be also used with %group syntax,
#                 for maxlogin limit
#        - NOTE: group and wildcard limits are not applied to root.
#          To apply a limit to the root user, <domain> must be
#          the literal username root.
#
#<type> can have the two values:
#        - "soft" for enforcing the soft limits
#        - "hard" for enforcing hard limits
#
#<item> can be one of the following:
#        - core - limits the core file size (KB)
#        - data - max data size (KB)
#        - fsize - maximum filesize (KB)
#        - memlock - max locked-in-memory address space (KB)
#        - nofile - max number of open files
#        - rss - max resident set size (KB)
#        - stack - max stack size (KB)
#        - cpu - max CPU time (MIN)
#        - nproc - max number of processes
#        - as - address space limit (KB)
#        - maxlogins - max number of logins for this user
#        - maxsyslogins - max number of logins on the system
#        - priority - the priority to run user process with
#        - locks - max number of file locks the user can hold
#        - sigpending - max number of pending signals
#        - msgqueue - max memory used by POSIX message queues (bytes)
#        - nice - max nice priority allowed to raise to values: [-20, 19]
#        - rtprio - max realtime priority
#        - chroot - change root to directory (Debian-specific)
#
#<domain>      <type>  <item>         <value>
#

#*               soft    core            0
#root            hard    core            100000
#*               hard    rss             10000
#@student        hard    nproc           20
#@faculty        soft    nproc           20
#@faculty        hard    nproc           50
#ftp             hard    nproc           0
#ftp             -       chroot          /ftp
#@student        -       maxlogins       4

```

دو نوع محدودیت وجود دارد: **محدودیت نرم (soft limit)** که مانند یک هشدار عمل می‌کند و **محدودیت سخت (hard limit)** که حداکثر محدودیت واقعی است. برای مثال، تنظیمات زیر باعث می‌شود هر کسی در گروه faculty نتواند بیش از ۵۰ فرآیند داشته باشد و در ۲۰ فرآیند به او هشدار داده شود. 

نکته: محدودیت نرم نمی‌تواند بیشتر از محدودیت سخت باشد.

> تنظیمات `ulimit` بخشی از سیستم ماژول احراز هویت قابل پلاک (PAM) است که در کتاب LPIC-2 مورد بحث قرار خواهد گرفت.

## بررسی کاربران در سیستم

به عنوان یک مدیر سیستم، ممکن است بخواهید بدانید در هر لحظه چه کسانی در سیستم هستند. همچنین ممکن است بخواهید بدانید آن‌ها چه کاری انجام می‌دهند. در این بخش، ۳ روش مختلف برای شناسایی افرادی که در سیستم لینوکس شما هستند را بررسی می‌کنیم.

### w

 دستور `w` در لینوکس برای نشان دادن اینکه چه کسانی لاگین کرده‌اند و چه کاری انجام می‌دهند استفاده می‌شود. این دستور اطلاعاتی درباره کاربران فعلی ماشین و فرآیندهای آن‌ها نشان می‌دهد.

```
root@ubuntu16-1:~# w
 01:24:45 up  4:33,  4 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
user1    tty7     :0               15:11   10:13m 40.45s  0.27s /sbin/upstart -
payam    pts/19   127.0.0.1        01:11   12:47   0.04s  0.04s -bash
user2    pts/21   127.0.0.1        01:24   13.00s  0.04s  0.04s -bash
root     pts/22   192.168.52.133   01:16    3:41   0.03s  0.03s -bash
```

خروجی دستور `w` شامل ستون‌های زیر است:

۱. هدر (header) به ترتیب شامل زمان فعلی، مدت زمان روشن بودن سیستم (uptime)، تعداد کاربران لاگین شده و میانگین بار سیستم (load average) در ۱، ۵ و ۱۵ دقیقه گذشته است. 
۲. موارد زیر برای هر کاربر نمایش داده می‌شود:

* `نام کاربر`
* `شماره ماشین کاربر یا شماره tty`
* `آدرس ماشین راه دور`
* `زمان ورود کاربر`
* `مدت زمان بیکاری (Idle time)`
* `زمان استفاده شده توسط تمام فرآیندهای متصل به tty (زمان JCPU)`
* `زمان استفاده شده توسط فرآیند فعلی (زمان PCPU)`
* `دستوری که در حال حاضر توسط کاربر در حال اجراست`

دستور `w` گزینه‌هایی دارد که می‌توانید با `w --help` آن‌ها را ببینید.

### who

دستور `who` برای دریافت اطلاعات درباره کاربرانی که در حال حاضر به سیستم وارد شده‌اند، استفاده می‌شود.

```
root@ubuntu16-1:~# who
user1    tty7         2020-03-22 15:11 (:0)
payam    pts/19       2020-03-23 01:11 (127.0.0.1)
user2    pts/21       2020-03-23 01:24 (127.0.0.1)
root     pts/22       2020-03-23 01:16 (192.168.52.133)
```

اگر هیچ گزینه‌ای ارائه نشود، دستور `who` اطلاعات زیر را برای هر کاربر لاگین شده نمایش می‌دهد:

1. `نام لاگین کاربران`
2. `شماره خط ترمینال`
3. `زمان ورود کاربران به سیستم`
4. `نام میزبان راه دورِ کاربر`

دستور `who` گزینه‌های زیادی دارد؛ `who --help` را امتحان کنید.

{% hint style="info" %}
دستورات `w` و `who` اطلاعات خود را از فایل `/var/run/utmp` می‌خوانند. این فایل حاوی اطلاعاتی درباره کاربرانی است که در حال حاضر به سیستم وارد شده‌اند.
{% endhint %}

بنابراین ما به دستور دیگری نیاز داریم تا اطلاعات افرادی را که خارج شده‌اند (log out) به دست آوریم، و آن دستور `last` است.

### last

 دستور `last` در لینوکس برای نمایش لیست تمام کاربرانی که وارد و خارج شده‌اند، استفاده می‌شود.

```
root@ubuntu16-1:~# last
user2    pts/21       127.0.0.1        Mon Mar 23 01:24   still logged in
root     pts/22       192.168.52.133   Mon Mar 23 01:16   still logged in
user2    pts/21       127.0.0.1        Mon Mar 23 01:13 - 01:21  (00:08)
payam    pts/19       127.0.0.1        Mon Mar 23 01:11   still logged in

wtmp begins Mon Mar 23 01:11:58 2020
```

خروجی این دستور شامل ستون‌های زیر است:

1. `نام کاربر`
2. `شماره دستگاه Tty`
3. `تاریخ و زمان ورود`
4. `زمان خروج`
5. `کل زمان فعالیت`

{% hint style="info" %}
دستور `last` از فایل `/var/log/wtmp` برای نمایش لیست آخرین کاربران لاگین شده استفاده می‌کند. این فایل مانند تاریخچه‌ای برای فایل `utmp` است، یعنی لاگ‌های تمام کاربران وارد شده و خارج شده (در گذشته) را نگه می‌دارد.
{% endhint %}

{% hint style="info" %}
فایل `/var/log/btmp` تلاش‌های ناموفق برای ورود را ردیابی می‌کند. بنابراین دستور `last -f /var/log/btmp` را برای بررسی آخرین ورودهای ناموفق امتحان کنید.
{% endhint %}

دستور `last` همچنین اطلاعاتی درباره آخرین ریبوت‌های سیستم به ما می‌دهد؛ فراموش نکنید که نگاهی به `last --help` بیندازید.

.

.

.

[https://www.cyberciti.biz/faq/unix-linux-check-if-port-is-in-use-command/](https://www.cyberciti.biz/faq/unix-linux-check-if-port-is-in-use-command/)

[https://www.tecmint.com/find-open-ports-in-linux/](https://www.tecmint.com/find-open-ports-in-linux/)

[https://blog.confirm.ch/tcp-connection-states/](https://blog.confirm.ch/tcp-connection-states/) (tcp 3-way handshake)

[https://jadi.gitbooks.io/lpic1/content/1101\_perform_security_administration_tasks.html](https://jadi.gitbooks.io/lpic1/content/1101\_perform_security_administration_tasks.html)

[https://www.geeksforgeeks.org/lsof-command-in-linux-with-examples/](https://www.geeksforgeeks.org/lsof-command-in-linux-with-examples/)

[https://www.cyberciti.biz/faq/how-to-check-open-ports-in-linux-using-the-cli/](https://www.cyberciti.biz/faq/how-to-check-open-ports-in-linux-using-the-cli/)

[https://linux.die.net/man/8/lsof](https://linux.die.net/man/8/lsof)

[https://www.digitalocean.com/community/tutorials/how-to-use-the-linux-fuser-command](https://www.digitalocean.com/community/tutorials/how-to-use-the-linux-fuser-command)

[https://www.tecmint.com/nmap-command-examples/](https://www.tecmint.com/nmap-command-examples/)

[https://www.tecmint.com/nmap-command-examples/](https://www.tecmint.com/nmap-command-examples/)

[https://phoenixnap.com/kb/nmap-command-linux-examples](https://phoenixnap.com/kb/nmap-command-linux-examples)

[https://www.linux.com/training-tutorials/how-use-sudo-and-su-commands-linux-introduction/](https://www.linux.com/training-tutorials/how-use-sudo-and-su-commands-linux-introduction/)

[https://superuser.com/questions/580568/any-differences-between-su-vs-su-beside-the-pathing](https://superuser.com/questions/580568/any-differences-between-su-vs-su-beside-the-pathing)

[https://www.howtoforge.com/tutorial/sudo-vs-su/](https://www.howtoforge.com/tutorial/sudo-vs-su/)

[https://help.ubuntu.com/community/Sudoers](https://help.ubuntu.com/community/Sudoers)

[https://www.hostinger.com/tutorials/sudo-and-the-sudoers-file/](https://www.hostinger.com/tutorials/sudo-and-the-sudoers-file/)

[https://support.hostway.com/hc/en-us/articles/115001509750-How-To-Install-and-Configure-Sudo](https://support.hostway.com/hc/en-us/articles/115001509750-How-To-Install-and-Configure-Sudo)

[https://unix.stackexchange.com/questions/27594/why-do-we-need-to-use-visudo-instead-of-directly-modifying-the-sudoers-file](https://unix.stackexchange.com/questions/27594/why-do-we-need-to-use-visudo-instead-of-directly-modifying-the-sudoers-file)

[https://www.computerhope.com/unix/visudo.htm](https://www.computerhope.com/unix/visudo.htm)

[https://www.thegeekdiary.com/understanding-etc-security-limits-conf-file-to-set-ulimit/](https://www.thegeekdiary.com/understanding-etc-security-limits-conf-file-to-set-ulimit/)

[http://geekswing.com/geek/quickie-tutorial-ulimit-soft-limits-hard-limits-soft-stack-hard-stack/](http://geekswing.com/geek/quickie-tutorial-ulimit-soft-limits-hard-limits-soft-stack-hard-stack/)

[https://gerardnico.com/os/linux/limits.conf](https://gerardnico.com/os/linux/limits.conf)

[https://www.thegeekstuff.com/2009/03/4-ways-to-identify-who-is-logged-in-on-your-linux-system/](https://www.thegeekstuff.com/2009/03/4-ways-to-identify-who-is-logged-in-on-your-linux-system/)

[https://www.geeksforgeeks.org/w-command-in-linux-with-examples/](https://www.geeksforgeeks.org/w-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/who-command-in-linux/](https://www.geeksforgeeks.org/who-command-in-linux/)

[https://www.geeksforgeeks.org/last-command-in-linux-with-examples/](https://www.geeksforgeeks.org/last-command-in-linux-with-examples/)

.
