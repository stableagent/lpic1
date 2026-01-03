# 107.2 خودکارسازی وظایف مدیریت سیستم با زمان‌بندی کارها

**وزن:** ۴

**توضیحات:** داوطلبان باید قادر به استفاده از cron یا anacron برای اجرای کارها در فواصل زمانی منظم و استفاده از at برای اجرای کارها در یک زمان خاص باشند.

**حوزه‌های کلیدی دانش:**

* مدیریت کارهای cron و at
* پیکربندی دسترسی کاربر به سرویس‌های cron و at
* پیکربندی anacron

**اصطلاحات و ابزارهای کاربردی:**

* /etc/cron.{d,daily,hourly,monthly,weekly}/
* /etc/at.deny
* /etc/at.allow
* /etc/crontab
* /etc/cron.allow
* /etc/cron.deny
* /var/spool/cron/
* crontab
* at
* atq
* atrm
* anacron
* /etc/anacrontab

بسیاری از وظایف مدیریت سیستم باید به طور منظم انجام شوند، مانند چرخاندن (rotating) فایل‌های لاگ، پشتیبان‌گیری از فایل‌ها یا پایگاه‌های داده، تهیه گزارش‌ها یا نصب به‌روزرسانی‌های سیستم. در این درس یاد خواهیم گرفت که چگونه با تنظیم زمان‌بندی، این نوع کارها را خودکار کنیم.

با زمان‌بندی:

* می‌توانیم یک کار را در زمانی شروع کنیم که استفاده از سیستم کم است.
* خطاها به دلیل نیاز کمتر به تعامل دستی کاهش می‌یابند.
* مطمئن هستیم که کارها همیشه به یک شکل اجرا می‌شوند.
* مدیران سیستم می‌توانند بیشتر بخوابند!

### اجرای کارها در فواصل زمانی منظم <a href="run-jobs-at-regular-intervals" id="run-jobs-at-regular-intervals"></a>

سیستم‌های لینوکس دارای دو امکان برای زمان‌بندی کارها جهت اجرا در فواصل زمانی منظم هستند:

* امکان اصلی `cron` برای سرورها و سیستم‌هایی که به طور مداوم روشن هستند، بهترین گزینه است.
* امکان `anacron` (یا `cron` ناهمزمان) برای سیستم‌هایی مانند دسکتاپ یا لپ‌تاپ که ممکن است در حالت خواب (sleep) باشند یا با باتری کار کنند، مناسب است.

| cron                                                 | anacron                                |
| ---------------------------------------------------- | -------------------------------------- |
| مناسب برای سرورها                                     | مناسب برای لپ‌تاپ‌ها و دسکتاپ‌ها          |
| دقت از یک دقیقه تا یک سال              | دقت روزانه، هفتگی و ماهانه |
| کار فقط در صورتی اجرا می‌شود که سیستم در زمان زمان‌بندی شده روشن باشد | کار در اولین باری که سیستم در دسترس قرار گیرد اجرا می‌شود |
| توسط کاربران معمولی قابل استفاده است                          | به دسترسی root نیاز دارد                   |

### زمان‌بندی کارهای دوره‌ای با cron <a href="schedule-periodic-jobs-with-cron" id="schedule-periodic-jobs-with-cron"></a>

 امکان اصلی `cron` شامل دیمون (daemon) `cron` و مجموعه‌ای از جداول است که شرح می‌دهند چه کاری باید با چه فرکانسی انجام شود. دیمون `cron` معمولاً توسط فرآیند `init` ، `upstart` یا `systemd` هنگام راه‌اندازی سیستم شروع می‌شود. 

### سینتکس فایل crontab و عملگرها

فایل `crontab` (جدول cron) یک فایل متنی است که زمان‌بندی کارهای cron را مشخص می‌کند. دیمون `cron` هر دقیقه بیدار می‌شود و هر crontab را برای کارهایی که نیاز به اجرا دارند بررسی می‌کند. 

 دو نوع فایل crontab وجود دارد: فایل‌های crontab کاربر به صورت انفرادی و فایل‌های crontab سراسری سیستم.

#### سینتکس و عملگرهای crontab

هر خط در فایل crontab کاربر شامل شش فیلد است که با فاصله از هم جدا شده‌اند و در ادامه دستوری که باید اجرا شود می‌آید.

```
* * * * * command(s)
- - - - -
| | | | |
| | | | ----- روز هفته (۰-۷) (یکشنبه=۰ یا ۷)
| | | ------- ماه (۱-۱۲)
| | --------- روز ماه (۱-۳۱)
| ----------- ساعت (۰-۲۳)
------------- دقیقه (۰-۵۹)
```

* `*`: عملگر ستاره به معنای هر مقداری یا همیشه است.
* `,`: عملگر کاما به شما اجازه می‌دهد لیستی از مقادیر را برای تکرار مشخص کنید. 
* `-`: عملگر خط تیره به شما اجازه می‌دهد محدوده‌ای از مقادیر را مشخص کنید. 
* `/`: عملگر اسلش به شما اجازه می‌دهد مقادیری را مشخص کنید که در فواصل زمانی معینی بین آن‌ها تکرار می‌شوند.

> همچنین اگر به جای فیلدهای زمان از `@reboot` یا `@daily` استفاده کنید، دستور یک بار پس از ریبوت یا به صورت روزانه اجرا خواهد شد.

بیایید چند مثال ببینیم:

```
@reboot      ### on reboot

50 13 * * *  ### 1:50 PM daily

40 6 4 * *   ### 4th of every month at 6:40AM

05 * * 1 0   ### 5 mintues past everyhour,each Sunday in January

0 15 29 11 5 ### 3:00PM Every November 29th  that lands on a Friday

1,5,10 * * * * # 1,5,10 mintues past every hour

30 20 * * 1-5 ## weekdays at 8:20 PM

*/5 * * * *  ### ever 5 mintues
```

## cronهای مخصوص کاربر

### /var/spool/cron/

 فایل‌های crontab کاربران با نام کاربر ذخیره می‌شوند و مکان آن‌ها بر اساس سیستم‌عامل متفاوت است. در سیستم‌های مبتنی بر Red Hat مانند CentOS، فایل‌های crontab در دایرکتوری `/var/spool/cron` ذخیره می‌شوند، در حالی که در دبیان و اوبونتو این فایل‌ها در دایرکتوری `/var/spool/cron/crontabs` ذخیره می‌گردند.

اگرچه می‌توانید فایل‌های crontab کاربر را به صورت دستی ویرایش کنید، اما توصیه می‌شود از دستور `crontab` استفاده کنید.

### دستور crontab

دستور `crontab` به شما اجازه می‌دهد یک فایل crontab را برای ویرایش نصب یا باز کنید.

می‌توانید از دستور `crontab` برای مشاهده، افزودن، حذف یا تغییر کارهای cron با استفاده از گزینه‌های زیر استفاده کنید:

* `crontab -e` : ویرایش فایل crontab، یا ایجاد آن اگر از قبل وجود نداشته باشد.
* `crontab -l` : نمایش محتویات فایل crontab.
* `crontab -r` : حذف فایل crontab فعلی شما.
* `crontab -i` : حذف فایل crontab فعلی شما با تایید قبل از حذف.
* `crontab -u <username>` : ویرایش فایل crontab کاربر دیگر. به امتیازات مدیر سیستم نیاز دارد.

```
user1@ubuntu16-1:~$ crontab -e
no crontab for user1 - using an empty one

Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        <---- easiest
  3. /usr/bin/vim.basic
  4. /usr/bin/vim.tiny

Choose 1-4 [2]: 3
```

دستور `crontab -e` فایل crontab را با استفاده از ویرایشگری که توسط متغیرهای محیطی `VISUAL` یا `EDITOR` مشخص شده است، باز می‌کند.

```
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
~                                                                               
"/tmp/crontab.f5VKYq/crontab" 22L, 888C                       1,1           All
```

به عنوان مثال با افزودن خط زیر، هر ۵ دقیقه یک ایمیل ارسال می‌شود:

```
5 * * * * echo "Hello" | mail -s "Cron Test" user1@localhost.com
```

دستور `crontab -e` همچنین سینتکس را قبل از خروج از فایل بررسی می‌کند که بسیار مفید است.

دستور `crontab -l` محتویات بالا را نشان می‌دهد. بیایید بررسی کنیم که آیا فایل crontab کاربر ایجاد شده است یا خیر:

```
root@ubuntu16-1:/var/spool/cron/crontabs# ls -l
total 4
-rw------- 1 user1 crontab 1154 Feb 15 05:07 user1
```

## cron سراسری سیستم

 علاوه بر فایل‌های crontab کاربر در `/var/spool/cron` ، دیمون `cron` همچنین `/etc/crontab` و هر crontab موجود در دایرکتوری `/etc/cron.d` را بررسی می‌کند. 

### /etc/crontab , /etc/cron.d

 فایل `/etc/crontab` و فایل‌های داخل دایرکتوری `/etc/cron.d` فایل‌های **crontab** سراسری سیستم هستند که فقط توسط مدیران سیستم قابل ویرایش می‌باشند.

> فایل `/etc/crontab` با ویرایش مستقیم به‌روزرسانی می‌شود. شما نمی‌توانید از دستور `crontab` برای به‌روزرسانی این فایل یا فایل‌های موجود در دایرکتوری `/etc/cron.d` استفاده کنید.

#### فایل‌های Crontab سراسری سیستم <a href="system-wide-crontab-files" id="system-wide-crontab-files"></a>

سینتکس فایل‌های crontab سراسری سیستم کمی با crontabهای کاربر متفاوت است. این فایل شامل یک فیلد کاربر اجباری اضافی است که مشخص می‌کند کدام کاربر کار cron را اجرا خواهد کرد.

```
* * * * * <username> command(s)
```

این فایل باید مستقیماً با یک ویرایشگر ویرایش شود و ما می‌توانیم ذکر کنیم که کدام کاربر دستور(ات) را اجرا می‌کند.

```
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
~                                                                               
~                                                                               
~                                                                                                                                                                                                                                         
"/etc/crontab" 15L, 722C                                      14,27-35      All
```

### /etc/cron.{daily,hourly,monthly,weekly}/

در بیشتر توزیع‌های لینوکس، می‌توانید **اسکریپت‌ها** را داخل دایرکتوری‌های `/etc/cron.{hourly,daily,weekly,monthly}` قرار دهید و این اسکریپت‌ها هر ساعت/روز/هفته/ماه اجرا خواهند شد.

```
root@ubuntu16-1:~# tree /etc/cron*
/etc/cron.d
├── anacron
├── php
└── popularity-contest
/etc/cron.daily
├── 0anacron
├── apache2
├── apport
├── apt-compat
├── aptitude
├── bsdmainutils
├── cracklib-runtime
├── dpkg
├── logrotate
├── man-db
├── mlocate
├── passwd
├── popularity-contest
├── quota
├── samba
├── update-notifier-common
└── upstart
/etc/cron.hourly
/etc/cron.monthly
└── 0anacron
/etc/crontab [error opening dir]
/etc/cron.weekly
├── 0anacron
├── fstrim
├── man-db
└── update-notifier-common

0 directories, 25 files
```

به عنوان مثال بیایید نگاهی به یکی از آن‌ها بیندازیم:

```
root@ubuntu16-1:~# cat /etc/cron.daily/passwd 
#!/bin/sh

cd /var/backups || exit 0

for FILE in passwd group shadow gshadow; do
        test -f /etc/$FILE              || continue
        cmp -s $FILE.bak /etc/$FILE     && continue
        cp -p /etc/$FILE $FILE.bak && chmod 600 $FILE.bak
done
```

### anacron

امکان cron برای سیستم‌هایی که به طور مداوم کار می‌کنند به خوبی عمل می‌کند. اگر سیستم در زمانی که cron باید وظیفه‌ای را اجرا کند خاموش باشد، آن کار تا نوبت بعدی اجرا نخواهد شد! اما anacron هر بار که یک کار روزانه، هفتگی یا ماهانه اجرا می‌شود، یک برچسب زمانی (timestamp) ایجاد می‌کند. 

> نکته: anacron برچسب‌های زمانی را در زمان بوت (BOOT TIME) بررسی می‌کند و کارهایی که باید به صورت ساعتی یا هر دقیقه اجرا شوند را مدیریت نمی‌کند.

### /etc/anacrontab

 جدول کارهای `anacron` در فایل `/etc/anacrontab` ذخیره می‌شود که فرمت آن کمی با `/etc/crontab` متفاوت است.

```
root@ubuntu16-1:/# cat /etc/anacrontab 
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
HOME=/root
LOGNAME=root

# These replace cron's entries
1    5    cron.daily    run-parts --report /etc/cron.daily
7    10    cron.weekly    run-parts --report /etc/cron.weekly
@monthly    15    cron.monthly    run-parts --report /etc/cron.monthly
```

> درست مانند `/etc/crontab` ، فایل `/etc/anacrontab` نیز با ویرایش مستقیم به‌روزرسانی می‌شود. 

{% hint style="info" %}
#### فرمت Anacrontab

```
period   delay   job-identifier   command
```

* **period in days**: فرکانس اجرای یک کار را بر حسب تعداد روز (N) مشخص می‌کند.
* **delay in minutes**: تعداد دقیقه‌هایی که anacron باید پس از ریبوت قبل از اجرای کار منتظر بماند.
* **job-identifier**: نام فایل برچسب زمانی کار است. باید برای هر کار منحصر به فرد باشد. این فایل در دایرکتوری `/var/spool/anacron` در دسترس خواهد بود.
* **command**: دستوری که باید اجرا شود را مشخص می‌کند. 
{% endhint %}

### /var/spool/anacron

 `anacron` یک فایل برچسب زمانی برای هر کار در `/var/spool/anacron` نگه می‌دارد تا زمان اجرای کار را ثبت کند. وقتی anacron اجرا می‌شود، بررسی می‌کند که آیا تعداد روزهای لازم از آخرین باری که یک کار اجرا شده گذشته است یا خیر و در صورت نیاز کار را اجرا می‌کند.

```
root@ubuntu16-1:/# ls -l /var/spool/anacron/
total 12
-rw------- 1 root root 9 Feb 15 07:35 cron.daily
-rw------- 1 root root 9 Feb  1 07:45 cron.monthly
-rw------- 1 root root 9 Feb 10 00:46 cron.weekly
```

این فایل شامل یک خط واحد خواهد بود که نشان‌دهنده آخرین باری است که این کار اجرا شده است.

```
root@ubuntu16-1:/# cat /var/spool/anacron/cron.daily 
20200215
```

### at

 گاهی اوقات نیاز دارید که یک کار را در آینده فقط یک بار اجرا کنید، نه به طور منظم. برای این منظور از دستور `at` استفاده می‌کنید. (در اوبونتو: `apt install at`)

 یک توالی معمولی دستور **at** به این صورت است:

```
at 5:45
```

با اجرای دستور at، شما را در یک پرامپت مخصوص قرار می‌دهد که در آن می‌توانید دستور (یا مجموعه‌ای از دستورات) را که باید در زمان زمان‌بندی شده اجرا شوند، تایپ کنید. پس از پایان کار، در یک خط جدید کلیدهای **Control-D** را فشار دهید تا دستور شما در صف قرار گیرد.

```
user1@ubuntu16-1:~$ at 5:45
warning: commands will be executed using /bin/sh
at> touch BlaHBlaH 
at> <EOT>
job 3 at Sat Feb 15 05:45:00 2020

user1@ubuntu16-1:~$ ls -ltrh | grep -i blah
-rw-rw-r-- 1 user1 user1    0 Feb 15 05:45 BlaHBlaH
```

> warning: commands will be executed using /bin/sh

برخی مثال‌های دیگر از دستور at:

```
   مثال                  زمان‌بندی کار در
-----------                -------------------
at 10:00 AM                ساعت ۱۰ صبح آینده
at 10:00 AM Sun            ساعت ۱۰ صبح یکشنبه آینده
at 10:00 AM July 25        ساعت ۱۰ صبح ۲۵ جولای آینده
at 10:00 AM 6/22/2019      ساعت ۱۰ صبح ۲۲ ژوئن ۲۰۱۹
at 10:00 AM next month     ساعت ۱۰ صبح همین تاریخ در ماه آینده
at 10:00 AM tomorrow       ساعت ۱۰ صبح فردا
at teatime                 اجرا در ساعت ۴ بعد از ظهر آینده
at midnight                اجرا در ساعت ۱۲ شب آینده
at now + 1 hour            اجرا دقیقا ۱ ساعت بعد
at now + 1 week            اجرا دقیقا ۱ هفته بعد
at now + 1 month           اجرا دقیقا ۱ ماه بعد
at now + 1 year            اجرا دقیقا ۱ سال بعد
```

دستور at اعضای دیگری نیز در خانواده خود دارد:

### atq

لیست کارهای در انتظار کاربران را نشان می‌دهد.

```
user1@ubuntu16-1:~$ at 06:20
warning: commands will be executed using /bin/sh
at> touch file1
at> <EOT>
job 4 at Sat Feb 15 06:20:00 2020
user1@ubuntu16-1:~$ at 06:30
warning: commands will be executed using /bin/sh
at> touch file2
at> <EOT>
job 5 at Sat Feb 15 06:30:00 2020

user1@ubuntu16-1:~$ atq
4    Sat Feb 15 06:20:00 2020 a user1
5    Sat Feb 15 06:30:00 2020 a user1
```

### atrm 

 کارها را با شماره کارشان حذف می‌کند.

```
user1@ubuntu16-1:~$ atrm 5
user1@ubuntu16-1:~$ atq
4    Sat Feb 15 06:20:00 2020 a user1
```

  دستور `atq` فقط لیست کارها را نشان می‌دهد، اما اگر می‌خواهید ببینید چه اسکریپت/دستوراتی با آن کار زمان‌بندی شده‌اند، از دستور `at -c JobNum` استفاده کنید و خط آخر را ببینید.

{% hint style="info" %}
هر دو cron و at سرویس‌های سیستمی هستند.
{% endhint %}

## پیکربندی دسترسی کاربر به زمان‌بندی کارها

ما می‌توانیم دسترسی به دستور `crontab` را با استفاده از دو فایل در دایرکتوری `/etc/cron.d` کنترل کنیم: `cron.deny` و `cron.allow`. این فایل‌ها فقط به کاربران مشخص شده اجازه می‌دهند وظایف دستور crontab مانند ایجاد، ویرایش، نمایش یا حذف فایل‌های crontab خودشان را انجام دهند.

> فایل‌های `cron.deny` و `cron.allow` شامل لیستی از نام‌های کاربری هستند، هر نام کاربری در یک خط.

### /etc/cron.allow , /etc/cron.deny

این فایل‌های کنترل دسترسی به صورت زیر با هم کار می‌کنند:

* اگر `cron.allow` وجود داشته باشد، فقط کاربرانی که در این فایل لیست شده‌اند می‌توانند فایل‌های crontab را ایجاد، ویرایش، مشاهده یا حذف کنند.
* اگر `cron.allow` وجود نداشته باشد، تمام کاربران می‌توانند فایل‌های crontab را ارسال کنند، به جز کاربرانی که در `cron.deny` لیست شده‌اند.
* اگر هیچ‌کدام از فایل‌های `cron.allow` یا `cron.deny` وجود نداشته باشند، برای اجرای دستور crontab به امتیازات superuser نیاز است.

برای ویرایش یا ایجاد فایل‌های `cron.deny` و `cron.allow` به امتیازات superuser نیاز است.

```
root@ubuntu16-1:~# cat /etc/cron.deny
user2
```

###  /etc/at.allow , /etc/at.deny

فایل‌های متناظر `/etc/at.allow` و `/etc/at.deny` اثرات مشابهی برای امکان `at` دارند.

.

.

.

{% hint style="info" %}
### متغیرهای Crontab <a href="crontab-variables" id="crontab-variables"></a>

دیمون cron به طور خودکار چندین متغیر محیطی را تنظیم می‌کند.

* مسیر پیش‌فرض روی `PATH=/usr/bin:/bin` تنظیم شده است. اگر دستوری که فراخوانی می‌کنید در مسیر مشخص شده توسط cron وجود دارد، می‌توانید از مسیر مطلق دستور استفاده کنید یا متغیر `$PATH` در cron را تغییر دهید. شما نمی‌توانید به صورت ضمنی `:$PATH` را مانند یک اسکریپت معمولی اضافه کنید.
* شل پیش‌فرض روی `/bin/sh` تنظیم شده است. می‌توانید با تغییر متغیر `SHELL` شل متفاوتی را تنظیم کنید.
* Cron دستور را از دایرکتوری خانگی کاربر فراخوانی می‌کند. متغیر `HOME` را می‌توان با تنظیمات در crontab بازنویسی کرد.
* اعلان ایمیل به صاحب crontab ارسال می‌شود. برای تغییر این رفتار پیش‌فرض، می‌توانید از متغیر محیطی `MAILTO` با لیستی (جدا شده با کاما) از تمام آدرس‌های ایمیلی که می‌خواهید اعلان‌ها را دریافت کنند، استفاده کنید. اگر `MAILTO` تعریف شده اما خالی باشد (`MAILTO=""`)، هیچ ایمیلی ارسال نمی‌شود.
{% endhint %}

.

.

[https://developer.ibm.com/tutorials/l-lpic1-107-2/](https://developer.ibm.com/tutorials/l-lpic1-107-2/)

[https://linuxize.com/post/scheduling-cron-jobs-with-crontab/](https://linuxize.com/post/scheduling-cron-jobs-with-crontab/)

[https://jadi.gitbooks.io/lpic1/content/1072\_automate_system_administration_tasks_by_scheduling_jobs.html](https://jadi.gitbooks.io/lpic1/content/1072\_automate_system_administration_tasks_by_scheduling_jobs.html)

[https://www.thegeekdiary.com/centos-rhel-anacron-basics-what-is-anacron-and-how-to-configure-it/](https://www.thegeekdiary.com/centos-rhel-anacron-basics-what-is-anacron-and-how-to-configure-it/)

[https://www.thegeekstuff.com/2011/05/anacron-examples/](https://www.thegeekstuff.com/2011/05/anacron-examples/)

[https://www.computerhope.com/unix/uat.htm](https://www.computerhope.com/unix/uat.htm)

[https://tecadmin.net/one-time-task-scheduling-using-at-commad-in-linux/](https://tecadmin.net/one-time-task-scheduling-using-at-commad-in-linux/)

[https://docs.oracle.com/cd/E19253-01/817-0403/sysrescron-23/index.html](https://docs.oracle.com/cd/E19253-01/817-0403/sysrescron-23/index.html)

.
