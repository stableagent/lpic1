# 108.2 گزارش‌گیری سیستم (System Logging)

**وزن:** ۳

**توضیحات:** داوطلبان باید قادر به پیکربندی دیمون syslog باشند. این هدف همچنین شامل پیکربندی دیمون گزارش‌گیری برای ارسال خروجی لاگ به یک سرور لاگ مرکزی یا پذیرش خروجی لاگ به عنوان یک سرور لاگ مرکزی است. استفاده از زیرسیستم journal در systemd نیز پوشش داده شده است. همچنین، آگاهی از rsyslog و syslog-ng به عنوان سیستم‌های گزارش‌گیری جایگزین گنجانده شده است.

**حوزه‌های کلیدی دانش:**

* پیکربندی دیمون syslog
* درک facilityها، اولویت‌ها (priorities) و عملکردهای (actions) استاندارد
* پیکربندی logrotate
* آگاهی از rsyslog و syslog-ng

**اصطلاحات و ابزارهای کاربردی:**

* syslog.conf
* syslogd
* klogd
* /var/log/
* logger
* logrotate
* /etc/logrotate.conf
* /etc/logrotate.d/
* journalctl
* /etc/systemd/journald.conf
* /var/log/journal/

#### چرا گزارش‌گیری (Logging)؟

یک سیستم لینوکس دارای زیرسیستم‌ها و اپلیکیشن‌های بسیاری در حال اجرا است. ما از سیستم لاگینگ برای جمع‌آوری داده‌ها درباره سیستم در حال اجرای خود از لحظه بوت شدن استفاده می‌کنیم. گاهی اوقات فقط نیاز داریم بدانیم که همه چیز خوب پیش می‌رود. در مواقع دیگر از این داده‌ها برای حسابرسی (auditing)، عیب‌یابی (debugging)، اطلاع از اتمام ظرفیت دیسک یا سایر منابع و بسیاری اهداف دیگر استفاده می‌کنیم. 

### syslog

 Syslog استانداردی برای ارسال و دریافت پیام‌های اعلان - با فرمتی خاص - از دستگاه‌های مختلف شبکه است. این پیام‌ها شامل برچسب‌های زمانی، پیام‌های رویداد، سطح اهمیت (severity)، آدرس‌های IP هاست، موارد تشخیصی و غیره هستند.

### syslogd

دیمون syslog یک فرآیند سروری است که امکان گزارش‌گیری پیام‌ها را برای اپلیکیشن‌ها و فرآیندهای سیستم فراهم می‌کند. متأسفانه گزارش‌گیری در لینوکس یکی از جنبه‌هایی است که در فاز انتقال قرار دارد. امکان سنتی syslog و دیمون syslogd آن توسط سایر امکانات گزارش‌گیری مانند rsyslog، syslog-ng و زیرسیستم journal در systemd تکمیل یا جایگزین شده است.

### syslog.conf

 فایل `syslog.conf` فایل پیکربندی اصلی برای **syslogd** است. هر زمان که syslogd یک پیام لاگ دریافت می‌کند، بر اساس نوع پیام (یا facility) و اولویت آن (که فیلد انتخاب‌گر یا selector نامیده می‌شود) عمل می‌کند.

```
type (facility).priority (severity)  destination(where to send the log)
```

**Facilityها** به زبان ساده همان دسته‌بندی‌ها هستند. برخی از facilityها در لینوکس عبارتند از: `auth, user, kern, cron, daemon, mail, local1, local2, ...`

* **`auth`**` `: پیام‌های امنیتی/احراز هویت
* **`user `**: پیام‌های سطح کاربر
* **`kern `**: پیام‌های هسته (Kernel)
* **`cron `**: دیمون ساعت
* **`daemon `**: دیمون‌های سیستم
* **`mail `**: سیستم ایمیل
* **`local0 – local7`** : facilityهایی که به صورت محلی استفاده می‌شوند

**اولویت‌ها (priorities)** برخلاف facilityها که هیچ رابطه‌ای با یکدیگر ندارند، سلسله‌مراتبی هستند. اولویت‌های ممکن در لینوکس عبارتند از: `emerg/panic, alert, crit, err/error, warn/warning, notice, info, debug`

1. **`emerg : `**سیستم غیرقابل استفاده است
2. **`alert : `**اقدام فوری باید انجام شود
3. **`critical : `**شرایط بحرانی
4. **`err : `**شرایط خطا
5. **`warning : `**شرایط هشدار
6. **`notice : `**شرایط عادی اما مهم
7. **`info : `**پیام‌های اطلاعاتی 
8. **`debug : `**پیام‌های سطح عیب‌یابی

> اگر یک اولویت خاص را لاگ کنیم، تمام موارد با اهمیت بالاتر نیز لاگ خواهند شد.

**عملکرد (Action):** هر خط در این فایل یک یا چند انتخاب‌گر facility/priority را مشخص می‌کند که به دنبال آن یک عملکرد می‌آید. در فیلد عملکرد می‌توانیم مواردی مانند زیر داشته باشیم:

| عملکرد   | مثال           | توضیحات                                                                                                  |
| -------- | ----------------- | ------------------------------------------------------------------------------------------------------ |
| filename | /var/log/messages | نوشتن لاگ‌ها در فایل مشخص شده                                                                          |
| username | user2             | اعلان به آن شخص روی صفحه نمایش                                                                  |
| @ip      | @192.168.10.42    | ارسال لاگ‌ها به سرور لاگ مشخص شده و آن سرور بر اساس تنظیمات خود در مورد لاگ‌ها تصمیم می‌گیرد. |

 در خط زیر از `syslog.conf` ، مقدار `mail.notice` انتخاب‌گر و `/var/log/mail` عملکرد است (یعنی: "پیام‌ها را در `/var/log/mail` بنویس"):

```
mail.notice     /var/log/mail
```

در داخل انتخاب‌گر، "mail" همان facility (دسته پیام) و "notice" سطح اولویت است. می‌توانید بخشی از `syslog.conf` (در CentOS6) را ببینید:

```
#### RULES ####

# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none                /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 *

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Save boot messages also to boot.log
local7.*                                                /var/log/boot.log

```

* `*`: وایلدکارت (wildcard)؛ به معنای "هر facility" یا "هر اولویت".

* خط تیره `-`: به این معنی است که می‌تواند از حافظه کش استفاده کند (زمان را برای نوشتن مداوم روی دیسک تلف نمی‌کند).

* علامت مساوی `=`: برای لاگ کردن **فقط** یک سطح خاص از اولویت. `facility.=priority action`

همچنین دایرکتوری `/etc/rsyslog.d/` وجود دارد و بهتر است نرم‌افزارهای مختلف و مدیران سیستم تنظیمات خاص خود را در آنجا اضافه کنند، به جای اینکه فایل پیکربندی اصلی را ویرایش کنند (اوبونتو ۱۶ را ببینید).

### klogd

 پیام‌های هسته در زمان بوت، قبل از اینکه حتی سیستم‌فایل مونت شود، چگونه لاگ می‌شوند؟ هسته پیام‌ها را در یک بافر حلقوی (ring buffer) در حافظه ذخیره می‌کند. دیمون `klogd` این پیام‌ها را مستقیماً به کنسول، یا فایلی مانند `/var/log/dmesg` ، یا از طریق امکان syslog پردازش می‌کند.

### /var/log

تقریباً تمام فایل‌های لاگ در زیر دایرکتوری `/var/log` و زیر‌دایرکتوری‌های آن در لینوکس قرار دارند (CentOS6).

```
[root@centos6-1 ~]# ls /var/log
anaconda.ifcfg.log    cron              messages-20200217  tallylog
anaconda.log          cron-20200217     ntpstats           vmware-caf
anaconda.program.log  cups              pm-powersave.log   vmware-install.log
anaconda.storage.log  dmesg             ppp                vmware-tools-upgrader.log
anaconda.syslog       dmesg.old         prelink            vmware-vgauthsvc.log.0
anaconda.xlog         dracut.log        sa                 vmware-vmsvc.log
anaconda.yum.log      gdm               samba              wpa_supplicant.log
audit                 httpd             secure             wtmp
boot.log              lastlog           secure-20200217    Xorg.0.log
btmp                  maillog           spice-vdagent.log  Xorg.0.log.old
btmp-20200217         maillog-20200217  spooler            yum.log
ConsoleKit            messages          spooler-20200217   yum.log-20200217
```

می‌توانید از ویرایشگر متن مورد علاقه خود یا دستورات `less` یا `tail` به همراه `grep` برای خواندن این فایل‌های لاگ استفاده کنید.

### ایجاد یک شنونده (listener) rsyslog

ما می‌توانیم یک شنونده rsyslog ایجاد کنیم و پیام‌های لاگ سیستم‌های دیگر را دریافت کنیم. این کار بسیار آسان است. 

```
###CentOS 6
vi /etc/rsyslog.conf

###Change from 
#$UDPServerRun 514
#$ModLoad imtcp
#$InputTCPServerRun 514

### to
$UDPServerRun 514
$ModLoad imtcp
$InputTCPServerRun 514
```

```
###ubuntu 16
vim /etc/default/rsyslog

### Change From
RSYSLOGD_OPTIONS=""

### To
RSYSLOGD_OPTIONS="-r"
```

و در نهایت فراموش نکنید که سرویس را با `systemctl restart rsyslog` ری‌استارت کنید.

### journalctl

Systemd همچنین برنامه ژورنالینگ خود را به نام **journald** دارد و موارد را در فایل‌های باینری ذخیره می‌کند. ما نمی‌توانیم مانند آنچه در syslog/rsyslog انجام دادیم، فایل‌های متنی را ببینیم، بنابراین باید از ابزار خاصی به نام `journalctl` برای دسترسی به آن‌ها استفاده کنیم (CentOS7):

```
[root@centos7-1 ~]# journalctl 
-- Logs begin at Mon 2020-02-10 02:51:48 EST, end at Tue 2020-02-18 09:19:13 EST. --
Feb 10 02:51:48 localhost.localdomain systemd-journal[106]: Runtime journal is using 8.
Feb 10 02:51:48 localhost.localdomain kernel: Initializing cgroup subsys cpuset
Feb 10 02:51:48 localhost.localdomain kernel: Initializing cgroup subsys cpu
Feb 10 02:51:48 localhost.localdomain kernel: Initializing cgroup subsys cpuacct
Feb 10 02:51:48 localhost.localdomain kernel: Linux version 3.10.0-693.el7.x86_64 (buil
Feb 10 02:51:48 localhost.localdomain kernel: Command line: BOOT_IMAGE=/vmlinuz-3.10.0-
Feb 10 02:51:48 localhost.localdomain kernel: Disabled fast string operations
Feb 10 02:51:48 localhost.localdomain kernel: e820: BIOS-provided physical RAM map:
Feb 10 02:51:48 localhost.localdomain kernel: BIOS-e820: [mem 0x0000000000000000-0x0000
Feb 10 02:51:48 localhost.localdomain kernel: BIOS-e820: [mem 0x000000000009ec00-0x0000
Feb 10 02:51:48 localhost.localdomain kernel: BIOS-e820: [mem 0x00000000000dc000-0x0000
```

همانطور که قبلاً ذکر شد، گزارش‌گیری لینوکس یکی از جنبه‌هایی است که در حال تغییر است. توزیع‌های دارای systemd دارای journald هستند؛ علاوه بر آن، برخی از آن‌ها هنوز rsyslog را حفظ کرده‌اند و برخی دیگر نه. سعی کنید سیستم گزارش‌گیری لینوکس خود را پیدا کنید.

### /etc/systemd/journald.conf

فایل پیکربندی journalctl در مسیر `/etc/systemd/journald.conf` قرار دارد (CentOS7).

```
[root@centos7-1 ~]# cat  /etc/systemd/journald.conf 
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
#
# Entries in this file show the compile time defaults.
# You can change settings by editing this file.
# Defaults can be restored by simply deleting this file.
#
# See journald.conf(5) for details.

[Journal]
#Storage=auto
#Compress=yes
#Seal=yes
#SplitMode=uid
#SyncIntervalSec=5m
#RateLimitInterval=30s
#RateLimitBurst=1000
#SystemMaxUse=
#SystemKeepFree=
#SystemMaxFileSize=
#RuntimeMaxUse=
#RuntimeKeepFree=
#RuntimeMaxFileSize=
#MaxRetentionSec=
#MaxFileSec=1month
#ForwardToSyslog=yes
#ForwardToKMsg=no
#ForwardToConsole=no
#ForwardToWall=yes
#TTYPath=/dev/console
#MaxLevelStore=debug
#MaxLevelSyslog=debug
#MaxLevelKMsg=notice
#MaxLevelConsole=info
#MaxLevelWall=emerg
```

### logger

دستور `logger` در لینوکس راه آسانی برای تولید دستی لاگ فراهم می‌کند (CentOS6).

```
[root@centos6-1 ~]# logger local1.emerg Hello! This is my log!
```

 و این لاگ در `/var/log/syslog` (یا `/var/log/messages`) ظاهر خواهد شد:

```
[root@centos6-1 ~]# tail -5 /var/log/messages
Feb 18 06:54:42 server1 NetworkManager[2183]: <info>   nameserver '172.16.43.2'
Feb 18 06:54:42 server1 NetworkManager[2183]: <info>   domain name 'localdomain'
Feb 18 06:54:42 server1 dhclient[29269]: bound to 172.16.43.137 -- renewal in 693 seconds.
Feb 18 06:54:42 server1 nm-dispatcher.action: Script '/etc/NetworkManager/dispatcher.d/13-named' exited with error status 1.
Feb 18 06:59:34 server1 payam: Hello! This is my log!

```

### logrotate

 با توجه به حجم بالای گزارش‌گیری، ما باید بتوانیم اندازه فایل‌های لاگ را کنترل کنیم. این کار با استفاده از ابزار `logrotate` انجام می‌شود که معمولاً به عنوان یک کار cron اجرا می‌گردد.

فایل‌های مهمی که باید به آن‌ها توجه کرد عبارتند از:

* `/usr/sbin/logrotate`: خودِ دستور logrotate (فایل اجرایی)
* `/etc/cron.daily/logrotate`: اسکریپت شلی که logrotate را به صورت روزانه اجرا می‌کند (توجه داشته باشید که در برخی سیستم‌ها ممکن است `/etc/cron.daily/logrotate.cron` باشد).
* `/etc/logrotate.conf`: فایل پیکربندی چرخش لاگ

فایل مهم دیگر `/etc/logrotate.d` است که از طریق یک خط در فایل `/etc/logrotate.conf` در فرآیند گنجانده شده است:

### /etc/logrotate.conf

از فایل پیکربندی `/etc/logrotate.conf` برای تعیین نحوه چرخش و بایگانی لاگ‌ها استفاده کنید.

```
[root@centos6-1 ~]# cat /etc/logrotate.conf 
# see "man logrotate" for details
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
dateext

# uncomment this if you want your log files compressed
#compress

# RPM packages drop log rotation information into this directory
include /etc/logrotate.d

# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
    monthly
    create 0664 root utmp
    minsize 1M
    rotate 1
}

/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}

# system-specific logs may be also be configured here.
```

هر فایل لاگ می‌تواند به صورت روزانه، هفتگی، ماهانه یا زمانی که خیلی بزرگ می‌شود، مدیریت شود. 

| پارامتر                    | معنا                                                                                                                         |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| missingok                    | اگر فایل لاگ وجود نداشت، پیام خطا نده                                                                         |
| daily, weekly, monthly       | چرخش لاگ‌ها به صورت روزانه، هفتگی، ماهانه                                                                                              |
| rotate _N_                   | نگهداری آخرین N لاگ و حذف قدیمی‌ترها                                                                              |
| compress                     | فشرده‌سازی لاگ (ایجاد فایل‌های gz)                                                                                            |
|  **create** mode owner group |  بلافاصله پس از چرخش (قبل از اجرای اسکریپت **postrotate**)، فایل لاگ جدید با این دسترسی و مالک ایجاد می‌شود         |
| minsize N                    | فایل‌های لاگ وقتی بزرگتر از N بایت شوند چرخیده می‌شوند، اما نه قبل از بازه زمانی مشخص شده (روزانه و ...) |

این فایل شامل برخی تنظیمات پیش‌فرض است و چرخش را برای چند لاگ که متعلق به هیچ پکیج سیستمی نیستند، تنظیم می‌کند. همچنین از عبارت `include` برای وارد کردن تنظیمات از هر فایلی در دایرکتوری `/etc/logrotate.d` استفاده می‌کند (CentOS6).

### /etc/logrotate.d

هر پکیجی که نصب می‌کنیم و نیاز به چرخش لاگ داشته باشد، تنظیمات Logrotate خود را در اینجا قرار می‌دهد.

```
[root@centos6-1 ~]# ls /etc/logrotate.d/
ConsoleKit  cups  dracut  httpd  named  ppp  psacct  syslog  wpa_supplicant  yum

[root@centos6-1 ~]# cat /etc/logrotate.d/httpd 
/var/log/httpd/*log {
    missingok
    notifempty
    sharedscripts
    delaycompress
    postrotate
        /sbin/service httpd reload > /dev/null 2>/dev/null || true
    endscript
}
```

در اینجا معنای برخی از این پارامترها آمده است:

| پارامتر      | معنا                                                                           |
| -------------- | --------------------------------------------------------------------------------- |
| missingok      | اگر فایل لاگ وجود نداشت، پیام خطا نده                           |
| notifempty     | اگر فایل لاگ خالی بود، آن را نچرخان                                         |
| shared scripts |  اجرای اسکریپت‌های **prerotate** و **postrotate** برای هر فایل لاگی که چرخیده می‌شود |
| delaycompress  | به تأخیر انداختن فشرده‌سازی فایل لاگ قبلی تا چرخه چرخش بعدی          |

تمام!

.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-108-2/](https://developer.ibm.com/tutorials/l-lpic1-108-2/)

[https://stackify.com/syslog-101/](https://stackify.com/syslog-101/)

[https://www.ibm.com/support/knowledgecenter/en/SSB23S\_1.1.0.15/gtpc1/hsyslog.html](https://www.ibm.com/support/knowledgecenter/en/SSB23S\_1.1.0.15/gtpc1/hsyslog.html)

[https://linux.die.net/man/5/syslog.conf](https://linux.die.net/man/5/syslog.conf)

[https://www.linuxjournal.com/article/5476](https://www.linuxjournal.com/article/5476)

[https://jadi.gitbooks.io/lpic1/content/1082\_system_logging.html](https://jadi.gitbooks.io/lpic1/content/1082\_system_logging.html)

[https://en.wikipedia.org/wiki/Syslog](https://en.wikipedia.org/wiki/Syslog)

[https://renenyffenegger.ch/notes/Linux/logging/klogd/index](https://renenyffenegger.ch/notes/Linux/logging/klogd/index)

[https://www.tecmint.com/create-centralized-log-server-with-rsyslog-in-centos-7/](https://www.tecmint.com/create-centralized-log-server-with-rsyslog-in-centos-7/)

[https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04)

[https://linux.die.net/man/8/logrotate](https://linux.die.net/man/8/logrotate)

[https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04)

.
