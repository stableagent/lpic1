# 107.3 بومی‌سازی و بین‌المللی‌سازی

**وزن:** ۳

**توضیحات:** داوطلبان باید قادر به بومی‌سازی یک سیستم به زبانی غیر از انگلیسی باشند. همچنین، درک اینکه چرا `LANG=C` هنگام اسکریپت‌نویسی مفید است.

**حوزه‌های کلیدی دانش:**

* پیکربندی تنظیمات locale و متغیرهای محیطی
* پیکربندی تنظیمات منطقه زمانی (timezone) و متغیرهای محیطی

**اصطلاحات و ابزارهای کاربردی:**

* /etc/timezone
* /etc/localtime
* /usr/share/zoneinfo/
* LC_\*
* LC_ALL
* LANG
* TZ
* /usr/bin/locale
* tzselect
* timedatectl
* date
* iconv
* UTF-8
* ISO-8859
* ASCII
* Unicode

هزاران زبان مختلف در سراسر جهان استفاده می‌شود. اعداد و تاریخ‌ها می‌توانند به اشکال متفاوتی فرمت‌بندی شوند و بیش از ۴۰ الفبای مختلف وجود دارد. مردم از ساعت‌های ۱۲ ساعته یا ۲۴ ساعته برای زمان استفاده می‌کنند. همچنین سیستم‌های اندازه‌گیری مختلفی وجود دارد. در این درس یاد خواهیم گرفت که چگونه سیستم لینوکس خود را پیکربندی کنیم تا با _locale_ ما سازگار شود.

## منطقه زمانی (timezone)

اصطلاح منطقه زمانی (time zone) می‌تواند برای توصیف چندین مورد مختلف استفاده شود، اما بیشتر به زمان محلی یک منطقه یا کشور اشاره دارد. زمان محلی در یک منطقه زمانی بر اساس اختلاف آن با زمان هماهنگ جهانی (UTC) که استاندارد زمانی جهان است، تعریف می‌شود.

 تعدادی ابزار مدیریت زمان در لینوکس وجود دارد، مانند دستورات **date** و **timedatectl** برای دریافت منطقه زمانی فعلی سیستم.

### date

 دستور **date** برای نمایش تاریخ و زمان سیستم استفاده می‌شود. این دستور همچنین برای تنظیم تاریخ و زمان سیستم به کار می‌رود. به طور پیش‌فرض، دستور date تاریخ را در منطقه زمانی که سیستم‌عامل یونیکس/لینوکس روی آن پیکربندی شده است، نمایش می‌دهد. برای تغییر تاریخ و زمان باید کاربر ارشد (root) باشید.

```
user1@ubuntu16-1:~$ date
Sun Feb 16 03:27:44 PST 2020
```

دستور `date -u` زمان را در منطقه زمانی GMT (زمان میانگین گرینویچ) یا UTC (زمان هماهنگ جهانی) نمایش می‌دهد.

```
user1@ubuntu16-1:~$ date -u
Sun Feb 16 11:30:09 UTC 2020
```

**فرمت تاریخ**: FORMAT توالی از کاراکترهاست که نحوه نمایش خروجی را مشخص می‌کند. سینتکس آن به این صورت است: `date +% <format-options>` :

| گزینه فرمت تاریخ | هدف گزینه                |
| ------------------- | -------------------------------- |
| %Y                  | سال                             |
| %y                  | دو رقم آخر سال (۰۰-۹۹) |
| %m                  | ماه (۰۱-۱۲)                   |
| %d                  | روز ماه (مثلا ۰۱)          |
| %D                  | تاریخ؛ مشابه %m/%d/%y        |
| %H                  | ساعت (۰۰-۲۳)                    |
| %M                  | دقیقه (۰۰-۵۹)                  |

```
user1@ubuntu16-1:~$ date +"%Y%m%d-%H:%M"
20200216-03:49
```

### timedatectl

برای تمام توزیع‌های لینوکس که از systemd استفاده می‌کنند، دستور `timedatectl` وجود دارد.

دستور `timedatectl` به ما اجازه می‌دهد تا پیکربندی ساعت سیستم و تنظیمات آن را مشاهده و تغییر دهیم. ما می‌توانیم از این دستور برای تنظیم یا تغییر تاریخ، زمان و منطقه زمانی فعلی یا فعال کردن همگام‌سازی خودکار ساعت سیستم با یک سرور NTP راه دور (درس بعدی) استفاده کنیم.

```
user1@ubuntu16-1:~$ timedatectl
      Local time: Sun 2020-02-16 03:57:22 PST
  Universal time: Sun 2020-02-16 11:57:22 UTC
        RTC time: Sun 2020-02-16 11:57:22
       Time zone: America/Los_Angeles (PST, -0800)
 Network time on: yes
NTP synchronized: yes
 RTC in local TZ: no
```

#### پیکربندی منطقه زمانی کاربر

ما می‌توانیم منطقه زمانی خود را در طول فرآیند نصب سیستم‌عامل یا با استفاده از رابط گرافیکی (GUI) پیکربندی کنیم، اما مثل همیشه ابزارهای ترمینالی وجود دارند که به ما کمک می‌کنند. در گذشته از دستور `tzconfig` استفاده می‌شد اما اکنون منسوخ شده است، به جای آن از `tzselect` استفاده کنید:

### tzselect 

 برنامه **tzselect** اطلاعاتی درباره مکان فعلی از کاربر می‌پرسد و توضیحات منطقه زمانی حاصل را در خروجی استاندارد چاپ می‌کند. این خروجی به عنوان مقداری برای متغیر محیطی **TZ** مناسب است.

```
user1@ubuntu16-1:~$ tzselect 
Please identify a location so that time zone rules can be set correctly.
Please select a continent, ocean, "coord", or "TZ".
 1) Africa
 2) Americas
 3) Antarctica
 4) Asia
 5) Atlantic Ocean
 6) Australia
 7) Europe
 8) Indian Ocean
 9) Pacific Ocean
10) coord - I want to use geographical coordinates.
11) TZ - I want to specify the time zone using the Posix TZ format.
```

در پایان این فرآیند، برنامه پیشنهاد می‌دهد که متغیر محیطی TZ (Time Zone) را تنظیم کنیم:

```
You can make this change permanent for yourself by appending the line
    TZ='Asia/Tehran'; export TZ
to the file '.profile' in your home directory; then log out and log in again.
```

 همانطور که در خروجی ذکر شده، اگر می‌خواهید از منطقه زمانی متفاوتی با منطقه زمانی سیستم خود استفاده کنید، می‌توانید آن را در فایل `.profile` خود تنظیم و export کنید.

#### پیکربندی منطقه زمانی سیستم

### /usr/share/zoneinfo

دایرکتوری `/usr/share/zoneinfo` مکانی است که تمام اطلاعات مربوط به مناطق زمانی را نگه می‌دارد.

```
root@ubuntu16-1:~# ls -l /usr/share/zoneinfo/ | head
total 328
drwxr-xr-x  2 root root  4096 Nov  4  2018 Africa
drwxr-xr-x  6 root root 20480 Nov  4  2018 America
drwxr-xr-x  2 root root  4096 Nov  4  2018 Antarctica
drwxr-xr-x  2 root root  4096 Nov  4  2018 Arctic
drwxr-xr-x  2 root root 12288 Nov  4  2018 Asia
drwxr-xr-x  2 root root  4096 Nov  4  2018 Atlantic
drwxr-xr-x  2 root root  4096 Nov  4  2018 Australia
drwxr-xr-x  2 root root  4096 Nov  4  2018 Brazil
drwxr-xr-x  2 root root  4096 Nov  4  2018 Canada
...
```

و شامل فایل‌های باینری مورد نیاز مناطق زمانی است:

```
root@ubuntu16-1:~# cat /usr/share/zoneinfo/Asia/Tehran 
TZif2el}Ht-@@0:@UgEJ7-( v(۝)˜
*"+H,V8-./o7H0a81Pj2B324%u5#H
686V7ܸ8֊H98 . . .
```

### /etc/localtime

لینوکس به `/etc/localtime` نگاه می‌کند تا زمان فعلی ماشین شما را تعیین کند. این فایل می‌تواند یک لینک نمادین (symbolic link) به منطقه زمانی صحیح یا یک کپی مستقیم از فایل منطقه زمانی باشد.

```
root@ubuntu16-1:~# ls -l /etc/localtime 
lrwxrwxrwx 1 root root 39 Nov  4  2018 /etc/localtime -> /usr/share/zoneinfo/America/Los_Angeles
```

ما می‌توانیم از یکی از دستورات زیر برای تغییر منطقه زمانی سیستم (مثلا به تهران) استفاده کنیم:

* `ln -s /usr/share/zoneinfo/Asia/Tehran /etc/localtime`
* `cp /usr/share/zoneinfo/Asia/Tehran /etc/localtime`

> اگر هنگام ایجاد لینک نمادین با خطا مواجه شدید، ابتدا آن را حذف کنید: `sudo unlink /etc/localtime` یا `sudo rm -rf /etc/localtime`

### /etc/timezone

این فایل نام منطقه زمانی را در سیستم‌های مبتنی بر دبیان نگه می‌دارد. در سیستم‌های مبتنی بر RHEL، فایل `/etc/sysconfig/clock` این وظیفه را بر عهده دارد.

{% hint style="info" %}
راه‌های دیگری نیز برای پیکربندی منطقه زمانی در توزیع‌های لینوکس وجود دارد:

* استفاده از **timedatectl** در توزیع‌های دارای systemd: `timedatectl set-timezone Europe/Amsterdam`
* استفاده از **dpkg-reconfigure** در توزیع‌های (Debian/Ubuntu): `dpkg-reconfigure tzdata`
{% endhint %}

## پیکربندی زبان‌ها

ما می‌توانیم زبان‌های سیستم را از طریق تنظیمات (Regional & Languages) پیکربندی کنیم، اما همیشه ابزارهای ترمینالی نیز وجود دارند.

### locale

 یک **locale** مجموعه‌ای از متغیرهای محیطی است که زبان، کشور و تنظیمات کدگذاری کاراکتر (یا هر ترجیح خاص دیگری) را برای اپلیکیشن‌ها و نشست شل شما در یک سیستم لینوکس تعریف می‌کند. این متغیرهای محیطی توسط کتابخانه‌های سیستم و برنامه‌هایی که از locale آگاه هستند، استفاده می‌شوند.

> برای مشاهده اطلاعات درباره locale نصب شده فعلی، از دستور `locale` استفاده کنید:

```
root@ubuntu16-1:~# locale
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

> فرمت متغیرها به این صورت است: `"Language_COUNTRY.ENCODING"`

### LANG

 مقدار متغیر محیطی **LANG** در زمان نصب تعیین می‌شود. (این متغیر مقدار پیش‌فرض را برای متغیرهای `LC_*` فراهم می‌کند، مگر اینکه آن متغیرها مستقیماً تنظیم شده باشند). 

* LANGUAGE
* LC_CTYPE: نحوه طبقه‌بندی کاراکترها به عنوان حروف، اعداد و غیره. این متغیر مواردی مانند نحوه تبدیل حروف به بزرگ و کوچک را تعیین می‌کند. 
* LC_NUMERIC: نحوه فرمت‌بندی اعداد. به عنوان مثال، در بسیاری از کشورها از نقطه (.) به عنوان جداکننده اعشار استفاده می‌شود، در حالی که برخی دیگر از کاما (,) استفاده می‌کنند.
* LC_TIME: نحوه فرمت‌بندی زمان و تاریخ. به عنوان مثال از "en_DK.UTF-8" برای داشتن ساعت ۲۴ ساعته در برخی برنامه‌ها استفاده می‌شود. 
* LC_COLLATE: نحوه مرتب‌سازی حروف‌الفبایی رشته‌ها (نام فایل‌ها و غیره). استفاده از locale "C" یا "POSIX" در اینجا منجر به ترتیبی مشابه `strcmp()` می‌شود که ممکن است نسبت به localeهای خاصِ زبان ترجیح داده شود. 
* LC_MONETARY: واحد پولی مورد استفاده، نام و نماد آن. 
* LC_MESSAGES: زبانی که باید برای پیام‌های سیستم استفاده شود. 
* LC_PAPER: اندازه‌های کاغذ: ۱۱ در ۱۷ اینچ، A4 و غیره. 
* LC_NAME: نحوه نمایش نام‌ها (نام خانوادگی اول یا آخر و غیره). 
* LC_ADDRESS: نحوه فرمت‌بندی آدرس‌ها (کشور اول یا آخر، مکان قرارگیری کد پستی و غیره). 
* LC_TELEPHONE: فرمت شماره‌های تلفن.
* LC_MEASUREMENT: واحدهای اندازه‌گیری مورد استفاده (فوت، متر، پوند، کیلو و غیره).
* LC_IDENTIFICATION: متادیتا (Metadata) درباره اطلاعات locale.

به عنوان مثال، بیایید `LC_TIME` را به چیز دیگری تغییر دهیم:

```
root@ubuntu16-1:~# LC_TIME=en_GB.UTF-8 date
Sun 16 Feb 19:04:37 +0330 2020
root@ubuntu16-1:~# LC_TIME=en_US.UTF-8 date
Sun Feb 16 19:04:44 +0330 2020
```

### LC_ALL

 مقادیر متغیر محیطی **LANG** و تمام متغیرهای محیطی دیگر **LC_*** را بازنویسی (override) می‌کند.

```
root@ubuntu16-1:~# export LC_ALL="en_GB.UTF-8"
root@ubuntu16-1:~# locale
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_GB.UTF-8"
LC_NUMERIC="en_GB.UTF-8"
LC_TIME="en_GB.UTF-8"
LC_COLLATE="en_GB.UTF-8"
LC_MONETARY="en_GB.UTF-8"
LC_MESSAGES="en_GB.UTF-8"
LC_PAPER="en_GB.UTF-8"
LC_NAME="en_GB.UTF-8"
LC_ADDRESS="en_GB.UTF-8"
LC_TELEPHONE="en_GB.UTF-8"
LC_MEASUREMENT="en_GB.UTF-8"
LC_IDENTIFICATION="en_GB.UTF-8"
LC_ALL=en_GB.UTF-8
root@ubuntu16-1:~# unset LC_ALL
```

> همچنین می‌توانیم از دستورات زیر برای کار با locale و تنظیم یا نصب زبان‌ها استفاده کنیم:
>
> * `dpkg-reconfigure locales` (در دبیان) 
> * `system-config-language` (در ردهت)
>
> همچنین در سیستم‌های دارای systemd، دستور `localectl` وجود دارد که تنظیمات locale سیستم را نمایش داده و کنترل می‌کند.

### LANG=C

دستور `LANG=C` دو کار انجام می‌دهد:

* اپلیکیشن‌ها را مجبور می‌کند از زبان پیش‌فرض برای خروجی استفاده کنند:

```
$ LC_ALL=es_ES man
¿Qué página de manual desea?

$ LC_ALL=C man
What manual page do you want?
```

* مرتب‌سازی را به صورت بایتی (byte-wise) اجبار می‌کند.

```
$ LC_ALL=en_US sort <<< $'a\nb\nA\nB'
a
A
b
B

$ LC_ALL=C sort <<< $'a\nb\nA\nB'
A
B
a
b
```

همچنین امکان استفاده از `LC_ALL=C` نیز وجود دارد.

### کدگذاری کاراکترها (Character Encoding)

کامپیوتر اطلاعات را با اعداد نشان می‌دهد و زمانی که نیاز است این اطلاعات به انسان‌ها منتقل شود (و برعکس)، باید کدگذاری (encode) شوند.

* **ASCII**: یک تکنیک کدگذاری ۷ بیتی است که به هر یک از ۱۲۸ کاراکتری که بیشترین استفاده را در انگلیسی آمریکایی دارند، یک عدد اختصاص می‌دهد. این به اکثر کامپیوترها اجازه می‌دهد تا متن‌های پایه را ثبت و نمایش دهند. ASCII شامل نمادهایی که در کشورهای دیگر استفاده می‌شود (مانند نماد پوند بریتانیا) نیست. تقریباً تمام نرم‌افزارهای ایمیل و ارتباطی ASCII را درک می‌کنند.
* **ISO 8859**: یک افزونه ۸ بیتی برای ASCII است که توسط ISO (سازمان بین‌المللی استانداردسازی) توسعه یافته است. ISO 8859 شامل ۱۲۸ کاراکتر ASCII به همراه ۱۲۸ کاراکتر اضافی مانند نماد پوند بریتانیا و سنت آمریکا است. چندین نوع از استاندارد ISO 8859 برای خانواده‌های زبانی مختلف وجود دارد.
* **Unicode**: تلاشی از سوی ISO و کنسرسیوم یونیکد برای توسعه یک سیستم کدگذاری برای متن‌های الکترونیکی است که شامل تمام الفباهای نوشتاری موجود در جهان می‌شود. یونیکد بسته به نمایش خاص آن، از کاراکترهای ۸، ۱۶ یا ۳۲ بیتی استفاده می‌کند، بنابراین اسناد یونیکد اغلب تا دو برابر بیشتر از ASCII به فضای دیسک نیاز دارند.



* ASCII: 7 bits. 128 code points.
* ISO-8859-1: 8 bits. 256 code points.
* UTF-8: 8-32 bits (1-4 bytes). 1,112,064 code points.

> کدگذاری‌های موجود در سیستم خود را با دستور `locale -m` بررسی کنید.

> از دستور `file` برای مشاهده کدگذاری کاراکترهای یک فایل استفاده کنید.

### iconv

ما می‌توانیم از برنامه `iconv` برای تبدیل بین کدگذاری‌های مختلف کاراکتر استفاده کنیم. بدیهی است که اگر از یک مجموعه کاراکتر بزرگ به یک مجموعه کوچکتر بروید، تبدیل به درستی انجام نمی‌شود.

```
iconv [options] [-f from-encoding] [-t to-encoding] [inputfile]...
```

مثال:

```
iconv -f ASCII -t UTF-8 /path/to/MyOldFile > MyNewFile
```

اگر هیچ فایل ورودی ارائه نشود، برنامه از ورودی استاندارد می‌خواند. به همین ترتیب، اگر فایل خروجی داده نشود، در خروجی استاندارد می‌نویسد. مثال:

> `iconv -l` تمام کدگذاری‌های شناخته شده مجموعه کاراکترها را لیست می‌کند.

.

.

[https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/](https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/)

[https://www.tecmint.com/check-linux-timezone/](https://www.tecmint.com/check-linux-timezone/)

[https://www.geeksforgeeks.org/date-command-linux-examples/](https://www.geeksforgeeks.org/date-command-linux-examples/)

[https://www.computerhope.com/unix/udate.htm](https://www.computerhope.com/unix/udate.htm)

[https://www.tecmint.com/set-time-timezone-and-synchronize-time-using-timedatectl-command/](https://www.tecmint.com/set-time-timezone-and-synchronize-time-using-timedatectl-command/)

[https://www.timeanddate.com/time/time-zones.html](https://www.timeanddate.com/time/time-zones.html)

[https://jadi.gitbooks.io/lpic1/content/1073\_localisation_and_internationalisation.html](https://jadi.gitbooks.io/lpic1/content/1073\_localisation_and_internationalisation.html)

[https://linux.die.net/man/8/tzselect](https://linux.die.net/man/8/tzselect)

[https://linuxacademy.com/blog/linux/changing-the-time-zone-in-linux-command-line/](https://linuxacademy.com/blog/linux/changing-the-time-zone-in-linux-command-line/)

[https://www.2daygeek.com/linux-change-check-timezone/](https://www.2daygeek.com/linux-change-check-timezone/)

[https://linux-audit.com/configure-the-time-zone-tz-on-linux-systems/](https://linux-audit.com/configure-the-time-zone-tz-on-linux-systems/)

[https://help.ubuntu.com/community/Locale](https://help.ubuntu.com/community/Locale)

[https://docs.oracle.com/cd/E23824\_01/html/E26033/glmbx.html](https://docs.oracle.com/cd/E23824\_01/html/E26033/glmbx.html)

[https://unix.stackexchange.com/questions/87745/what-does-lc-all-c-do](https://unix.stackexchange.com/questions/87745/what-does-lc-all-c-do)

[https://www.praim.com/en/news/character-encodings-in-linux-ascii-utf-8-and-iso-8859/](https://www.praim.com/en/news/character-encodings-in-linux-ascii-utf-8-and-iso-8859/)

[https://kb.iu.edu/d/ahfr](https://kb.iu.edu/d/ahfr)

[https://www.geeksforgeeks.org/iconv-command-in-linux-with-examples/](https://www.geeksforgeeks.org/iconv-command-in-linux-with-examples/)

.
