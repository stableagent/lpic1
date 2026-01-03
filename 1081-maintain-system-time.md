# 108.1 نگهداری زمان سیستم

## **108.1 نگهداری زمان سیستم**

**وزن:** ۳

**توضیحات:** داوطلبان باید قادر به نگهداری صحیح زمان سیستم و همگام‌سازی ساعت از طریق NTP باشند.

**حوزه‌های کلیدی دانش:**

* تنظیم تاریخ و زمان سیستم
* تنظیم ساعت سخت‌افزاری روی زمان صحیح در قالب UTC
* پیکربندی منطقه زمانی صحیح
* پیکربندی اولیه NTP
* دانش استفاده از سرویس pool.ntp.org
* آگاهی از دستور ntpq

**اصطلاحات و ابزارهای کاربردی:**

* /usr/share/zoneinfo/
* /etc/timezone
* /etc/localtime
* /etc/ntp.conf
* date
* hwclock
* ntpd
* ntpdate
* pool.ntp.org

هنگامی که یک سیستم لینوکس را به صورت گرافیکی نصب می‌کنیم، ساعت را تنظیم کرده و یک منطقه زمانی مناسب با نیازهای خود انتخاب می‌کنیم. همچنین می‌توانیم استفاده از پروتکل زمان شبکه (NTP) را برای تنظیم خودکار ساعت انتخاب کنیم. در این درس نشان می‌دهیم که چگونه فراتر از رابط‌های گرافیکی رفته و جنبه‌های مختلف مربوط به زمان را در سیستم لینوکس پیکربندی کنیم.

#### لینوکس چگونه زمان را ردیابی می‌کند

 در هر کامپیوتر ۲ ساعت وجود دارد. اولی ساعت سخت‌افزاری (Hardware Clock) است. این ساعتی است روی یک تراشه در مادربرد که حتی زمانی که دستگاه خاموش است، زمان را نگه می‌دارد. 

ساعت دیگر، ساعت مجازی سیستم (System Clock) است. لینوکس هنگام روشن شدن، از تراشه ساعت سخت‌افزاری می‌پرسد که ساعت چند است و سپس خودش با استفاده از نرم‌افزار زمان را ردیابی می‌کند. 

ساعت سخت‌افزاری می‌تواند زمان محلی (منطقه زمانی کامپیوتر شما) یا زمان UTC (زمان استاندارد) باشد. 

> ما می‌توانیم با بررسی فایل `/etc/adjtime` تعیین کنیم کدام‌یک تنظیم شده است. این فایل خالی است مگر اینکه سخت‌افزار به صورت دستی تنظیم شده باشد.

معمولاً ساعت سخت‌افزاری روی UTC تنظیم می‌شود، بنابراین هر زمان که سیستم بوت می‌شود، ساعت نرم‌افزاری ساعت سخت‌افزاری را می‌خواند و سپس تفاوت را بر اساس منطقه زمانی ما محاسبه می‌کند.

#### تنظیم ساعت سیستم

### date

 دستور **date** برای نمایش تاریخ و زمان سیستم استفاده می‌شود. به طور پیش‌فرض، دستور date تاریخ را به زمان محلی نمایش می‌دهد، حتی اگر ساعت سخت‌افزاری شما UTC باشد. از گزینه `-u` برای نمایش UTC استفاده کنید. 

```
root@ubuntu16-1:~# date
Mon Feb 17 19:17:52 +0330 2020
```

از گزینه `-u` برای نمایش UTC استفاده کنید:

```
root@ubuntu16-1:~# date -u
Mon Feb 17 15:50:20 UTC 2020
```

دستور date همچنین برای تنظیم تاریخ و زمان سیستم استفاده می‌شود (تنظیم خودکار (ntp) نباید فعال باشد، در غیر این صورت کار نخواهد کرد):

```
root@ubuntu16-1:~# date
Mon Feb 17 19:37:30 +0330 2020
root@ubuntu16-1:~# date --set="20170413 21:14:00"
Thu Apr 13 21:14:00 +0430 2017
root@ubuntu16-1:~# date
Thu Apr 13 21:14:02 +0430 2017
```

 اگرچه می‌توانیم زمان را با استفاده از دستور date تنظیم کنیم، مشکل بزرگ این ایده این است که تغییر زمان فقط تا ریبوت بعدی باقی می‌ماند، مگر اینکه به نوعی زمان سیستم را در ساعت سخت‌افزاری تنظیم کنیم.

### تنظیم ساعت سخت‌افزاری <a href="_setting_the_hardware_clock" id="_setting_the_hardware_clock"></a>

### hwclock

برای تغییر ساعت سخت‌افزاری، می‌توانید از ابزار BIOS مادربرد هنگام راه‌اندازی استفاده کنید، اما اگر آن فرصت را از دست دادید، هنوز امیدی هست: دستور "hwclock".

 دستور `hwclock` ابزاری برای دسترسی به ساعت سخت‌افزاری است که به آن ساعت بلادرنگ (RTC) نیز گفته می‌شود. همانطور که ذکر شد، این ساعت مستقل از سیستم‌عاملی است که استفاده می‌کنید و حتی زمانی که دستگاه خاموش است کار می‌کند.

```
root@ubuntu16-1:~# hwclock
Mon 17 Feb 2020 08:53:26 PM +0330  .972851 seconds
root@ubuntu16-1:~# date
Mon Feb 17 20:53:33 +0330 2020
```

>  خروجی `hwclock` تاریخ را به زمان محلی نشان می‌دهد (زمان پس از اضافه کردن منطقه زمانی به زمان UTC)، حتی زمانی که ساعت سخت‌افزاری روی UTC تنظیم شده است!

سینتکس hwclock به این صورت است: `hwclock [function] [option...]` که در آن:

```
 ### Functions:
 -r, --show           خواندن ساعت سخت‌افزاری و چاپ نتیجه
      --get            خواندن ساعت سخت‌افزاری و چاپ نتیجه اصلاح شده
      --set            تنظیم RTC روی زمان داده شده با --date
 -s, --hctosys        تنظیم زمان سیستم از روی ساعت سخت‌افزاری
 -w, --systohc        تنظیم ساعت سخت‌افزاری از روی زمان فعلی سیستم
      --systz          تنظیم زمان سیستم بر اساس منطقه زمانی فعلی
### Options:
--localtime            ساعت سخت‌افزاری زمان محلی را نگه می‌دارد
  -u, --utc            ساعت سخت‌افزاری در قالب UTC نگه داشته می‌شود
```

بیایید چند مثال ببینیم:

```
### بیایید ساعت سخت‌افزاری را در قالب utc تنظیم کنیم
root@ubuntu16-1:~# hwclock --set --date="01/02/1999 10:20:30"
root@ubuntu16-1:~# hwclock
Sat 02 Jan 1999 10:20:34 AM +0330  .274999 seconds
root@ubuntu16-1:~# cat /etc/adjtime 
0.000000 915259830 0.000000
915259830
UTC
root@ubuntu16-1:~# hwclock 
Sat 02 Jan 1999 10:21:48 AM +0330  .259334 seconds
root@ubuntu16-1:~# date
Mon Feb 17 21:36:41 +0330 2020


### بیایید آن را با تنظیم زمان محلی در ساعت سخت‌افزاری اصلاح کنیم

root@ubuntu16-1:~# hwclock 
Sat 02 Jan 1999 10:31:10 AM +0330  .009381 seconds
root@ubuntu16-1:~# date
Mon Feb 17 21:39:56 +0330 2020
root@ubuntu16-1:~# hwclock --localtime --set --date="02/17/2020 21:40:00"
root@ubuntu16-1:~# cat /etc/adjtime 
0.000000 1581963000 0.000000
1581963000
LOCAL
root@ubuntu16-1:~# hwclock 
Mon 17 Feb 2020 09:40:08 PM +0330  .290810 seconds
root@ubuntu16-1:~# date
Mon Feb 17 21:40:55 +0330 2020

```

دستور `hwclock --localtime -w` همان کار را انجام می‌دهد، اما تنظیم ساعت سخت‌افزاری روی زمان محلی ایده خوبی نیست، بنابراین به جای آن از `hwclock -u -w` استفاده کنید. 

### NTP

**پروتکل زمان شبکه (NTP)** یک پروتکل لایه اپلیکیشن است که برای همگام‌سازی ساعت بین هاست‌ها در یک شبکه TCP/IP استفاده می‌شود. هدف NTP این است که اطمینان حاصل کند تمام کامپیوترهای یک شبکه در مورد زمان توافق دارند، زیرا حتی یک اختلاف کوچک می‌تواند مشکلاتی ایجاد کند.

 NTP از یک سیستم سلسله‌مراتبی از منابع زمانی استفاده می‌کند. در بالای این ساختار، منابع زمانی بسیار دقیق قرار دارند - معمولاً ساعت‌های اتمی یا GPS. این ساعت‌ها به عنوان سرورهای **stratum 0** شناخته می‌شوند. سرورهای **Stratum 1** مستقیماً به سرورهای stratum 0 متصل هستند و کامپیوترهایی که سرورهای NTP را اجرا می‌کنند، زمان را به سرورهای **stratum 2** تحویل می‌دهند و به همین ترتیب الی آخر (منبع تصویر: Wikipedia):

![](.gitbook/assets/maintaintime-ntp.jpg)

> NTP از معماری کلاینت-سرور استفاده می‌کند؛ یک هاست به عنوان سرور NTP پیکربندی می‌شود و تمام هاست‌های دیگر در شبکه به عنوان کلاینت‌های NTP پیکربندی می‌شوند.

### pool.ntp.org

پروژه pool.ntp.org یک کلاستر مجازی بزرگ از سرورهای زمان است که سرویس NTP قابل اعتماد و با کاربری آسان را برای میلیون‌ها کلاینت فراهم می‌کند.

این "استخر" (pool) توسط صدها میلیون سیستم در سراسر جهان استفاده می‌شود. این سرویس، "سرور زمان" پیش‌فرض برای اکثر توزیع‌های اصلی لینوکس و بسیاری از دستگاه‌های تحت شبکه است.

### ntpdate

 دستور **ntpdate** تاریخ و زمان محلی را با نظرسنجی (polling) از سرور(های) پروتکل زمان شبکه (NTP) که به عنوان آرگومان داده شده‌اند، تنظیم می‌کند تا زمان صحیح را تعیین کند. این دستور باید به عنوان root در هاست محلی اجرا شود. (ممکن است نیاز به نصب آن داشته باشید). گزینه `-v` برای نمایش جزئیات (verbose) است. 

```
root@ubuntu16-1:~# ntpdate -v pool.ntp.org
17 Feb 22:59:34 ntpdate[4365]: ntpdate 4.2.8p4@1.3265-o Tue Jan  7 15:08:24 UTC 2020 (1)
17 Feb 22:59:46 ntpdate[4365]: adjust time server 194.225.150.25 offset -0.005153 sec
```

پس از این کار، باید ساعت سخت‌افزاری را با زمان اصلاح شده سیستم تنظیم کنیم؛ این کار با دستور `sudo hwclock -w` یا `hwclock -u -w` انجام می‌شود تا مطمئن شوید آن را روی UTC تنظیم می‌کنید. 

> سوئیچ `-q` زمان را پرس‌وجو کرده و فقط نتیجه را بدون تنظیم آن نشان می‌دهد.

### ntpd

به جای تنظیم دستی زمان در هر بار، می‌توانیم از یک سرویس لینوکس به نام ntp استفاده کنیم. ابزار `ntpd` یک دیمون سیستم‌عامل است که زمان سیستم را در همگام‌سازی با سرورهای زمان استاندارد اینترنت تنظیم و نگهداری می‌کند.

```
root@ubuntu16-1:~# apt install ntp
root@ubuntu16-1:~# systemctl start ntp
root@ubuntu16-1:~# systemctl enable ntp
```

نکته: زمانی که سرویس ntp در حال اجراست، نمی‌توانیم از `ntpdate` استفاده کنیم:

```
root@ubuntu16-1:~# ntpdate pool.ntp.org
17 Feb 23:15:34 ntpdate[5740]: the NTP socket is in use, exiting
```

### /etc/ntp.conf

 فایل پیکربندی ntpd در مسیر **/etc/ntp.conf** قرار دارد. این فایل در هنگام راه‌اندازی اولیه توسط دیمون ntpd خوانده می‌شود تا منابع همگام‌سازی مناسب را مشخص کند:

```
root@ubuntu16-1:~# cat /etc/ntp.conf 
# /etc/ntp.conf, configuration for ntpd; see ntp.conf(5) for help

driftfile /var/lib/ntp/ntp.drift

# Enable this if you want statistics to be logged.
#statsdir /var/log/ntpstats/

statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable

# Specify one or more NTP servers.

# Use servers from the NTP Pool Project. Approved by Ubuntu Technical Board
# on 2011-02-08 (LP: #104525). See http://www.pool.ntp.org/join.html for
# more information.
pool 0.ubuntu.pool.ntp.org iburst
pool 1.ubuntu.pool.ntp.org iburst
pool 2.ubuntu.pool.ntp.org iburst
pool 3.ubuntu.pool.ntp.org iburst

# Use Ubuntu's ntp server as a fallback.
pool ntp.ubuntu.com

# Access control configuration; see /usr/share/doc/ntp-doc/html/accopt.html for
# details.  The web page <http://support.ntp.org/bin/view/Support/AccessRestrictions>
# might also be helpful.
#
# Note that "restrict" applies to both servers and clients, so a configuration
# that might be intended to block requests from certain clients could also end
# up blocking replies from your own upstream servers.

# By default, exchange time with everybody, but don't allow configuration.
restrict -4 default kod notrap nomodify nopeer noquery limited
restrict -6 default kod notrap nomodify nopeer noquery limited

# Local users may interrogate the ntp server more closely.
restrict 127.0.0.1
restrict ::1

# Needed for adding pool entries
restrict source notrap nomodify noquery

# Clients from this (example!) subnet have unlimited access, but only if
# cryptographically authenticated.
#restrict 192.168.123.0 mask 255.255.255.0 notrust


# If you want to provide time to your local subnet, change the next line.
# (Again, the address is an example only.)
#broadcast 192.168.123.255

# If you want to listen to time broadcasts on your local subnet, de-comment the
# next lines.  Please do this only if you trust everybody on the network!
#disable auth
#broadcastclient

#Changes recquired to use pps synchonisation as explained in documentation:
#http://www.ntp.org/ntpfaq/NTP-s-config-adv.htm#AEN3918

#server 127.127.8.1 mode 135 prefer    # Meinberg GPS167 with PPS
#fudge 127.127.8.1 time1 0.0042        # relative to PPS for my hardware

#server 127.127.22.1                   # ATOM(PPS)
#fudge 127.127.22.1 flag3 1            # enable PPS API
```

می‌توانید سرورهای ntp را به سرور(های) مورد نظر خود تغییر دهید. فراموش نکنید که پس از هرگونه تغییر، سرویس را ری‌استارت کنید.

### ntpq

 برنامه کمکی **ntpq** برای نظارت بر عملیات دیمون NTP (`ntpd`) و تعیین عملکرد آن استفاده می‌شود.

 **`-p`**: لیستی از پیرها (peers) شناخته شده برای سرور و همچنین خلاصه ای از وضعیت آن‌ها را چاپ می‌کند.

```
root@ubuntu16-1:~# ntpq -p
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 0.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
  1.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 2.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 3.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 ntp.ubuntu.com  .POOL.          16 p    -   64    0    0.000    0.000   0.000
-77.104.104.100  37.156.28.13     3 u   20   64  345    5.641  -40.539  13.503
+77.104.70.70    194.225.150.25   3 u   25   64  377    4.835   11.343   7.680
-5-144-132-2.sta 119.28.183.184   3 u   28   64  377    4.803   -7.711  14.597
+ntp5.mobinnet.n 62.12.173.12     2 u   46   64  377    4.920  -11.823   6.280
*golem.canonical 17.253.34.251    2 u   59   64  377   85.408   11.230   8.712
```

 **`-n`**: تمام آدرس‌های هاست را به صورت عددی (dotted-quad) خروجی می‌دهد، به جای اینکه آن‌ها را به نام‌های هاست استاندارد تبدیل کند.

```
root@ubuntu16-1:~# ntpq -pn
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 0.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
  1.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 2.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 3.ubuntu.pool.n .POOL.          16 p    -   64    0    0.000    0.000   0.000
 ntp.ubuntu.com  .POOL.          16 p    -   64    0    0.000    0.000   0.000
-77.104.104.100  37.156.28.13     3 u    8   64  313    4.862  -20.085  13.553
+77.104.70.70    194.225.150.25   3 u    8   64  377    4.835   11.343   6.174
-5.144.132.2     119.28.183.184   3 u    6   64  377    5.062    3.365   5.087
+37.156.28.13    62.12.173.12     2 u   13   64  377    4.920  -11.823   4.775
*91.189.89.199   17.253.34.251    2 u   18   64  377   85.408   11.230   6.346
```

معانی علائم:

```
* همگام‌سازی شده با این پیر
# تقریباً همگام‌سازی شده با این پیر
+ پیر انتخاب شده برای همگام‌سازی احتمالی
– پیر کاندیدای انتخاب است
~ پیر به صورت استاتیک پیکربندی شده است
```

تمام.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-108-1/](https://developer.ibm.com/tutorials/l-lpic1-108-1/)

[http://xed.ch/help/time.html](http://xed.ch/help/time.html)

[https://www.geeksforgeeks.org/date-command-linux-examples/](https://www.geeksforgeeks.org/date-command-linux-examples/)

[https://jadi.gitbooks.io/lpic1/content/1081\_maintain_system_time.html](https://jadi.gitbooks.io/lpic1/content/1081\_maintain_system_time.html)

[https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-configuring_the_date_and_time-hwclock](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-configuring_the_date_and_time-hwclock)

[https://www.geeksforgeeks.org/hwclock-command-in-linux-with-examples/](https://www.geeksforgeeks.org/hwclock-command-in-linux-with-examples/)

[https://geek-university.com/ccna/network-time-protocol/](https://geek-university.com/ccna/network-time-protocol/)

[https://www.ntppool.org/en/](https://www.ntppool.org/en/)

[https://linux.die.net/man/8/ntpdate](https://linux.die.net/man/8/ntpdate)

[https://docs.ntpsec.org/latest/ntpd.html](https://docs.ntpsec.org/latest/ntpd.html)

[https://detailed.wordpress.com/2017/10/22/understanding-ntpq-output/](https://detailed.wordpress.com/2017/10/22/understanding-ntpq-output/)

.
