# 108.4 مدیریت چاپگرها و چاپ

**وزن:** ۲

**توضیحات:** داوطلبان باید قادر به مدیریت صف‌های چاپ و کارهای چاپ کاربر با استفاده از CUPS و رابط سازگاری LPD باشند.

**حوزه‌های کلیدی دانش:**

* پیکربندی اولیه CUPS (برای چاپگرهای محلی و راه دور)
* مدیریت صف‌های چاپ کاربر
* عیب‌یابی مشکلات عمومی چاپ
* افزودن و حذف کارها از صف‌های چاپگر پیکربندی شده

**اصطلاحات و ابزارهای کاربردی:**

* CUPS configuration files, tools and utilities
* /etc/cups/
* lpd legacy interface (lpr, lprm, lpq)

اگرچه امروزه بسیاری از ارتباطات ما الکترونیکی و بدون کاغذ است، اما هنوز نیاز قابل توجهی به چاپ مطالب از کامپیوترهایمان داریم.

### CUPS

CUPS یک سیستم چاپ متن‌باز و مبتنی بر استاندارد است که توسط شرکت Apple برای macOS و سایر سیستم‌عامل‌های شبه یونیکس توسعه یافته است. این نام مخفف Common UNIX Printing System است. سیستم CUPS می‌تواند به عنوان یک سرور چاپ برای یک ماشین محلی یا شبکه‌ای از ماشین‌ها عمل کند.

CUPS شامل موارد زیر است:

* **Print spooler/scheduler**: کارهای چاپ را برای ارسال به چاپگر به صف می‌کند.
* **Filter system**: داده‌ها را طوری تبدیل می‌کند که چاپگر متصل شده بتواند آن‌ها را درک و فرمت‌بندی کند.
* **Backend system**: داده‌ها را از فیلترها به چاپگر منتقل می‌کند.

![](.gitbook/assets/manageprinters-cupsdiagram.jpg)



> در قلب سیستم چاپ CUPS، سرور چاپ `cupsd` قرار دارد که به عنوان یک فرآیند دیمون (daemon) اجرا می‌شود.

#### رابط وب CUPS

 رابط‌های مختلفی برای CUPS وجود دارد مانند رابط‌های گرافیکی (GUI)، رابط‌های وب و حتی رابط‌های خط فرمان سنتی. در اینجا ما ابزار مدیریت وب CUPS را (`http://localhost:631` یا `http://127.0.0.1:631`) برای جستجو یا افزودن چاپگرها نشان می‌دهیم.

![](.gitbook/assets/manageprinters-cupsweb.jpg)

> اگر هنگام دسترسی به رابط وب CUPS از شما نام کاربری و رمز عبور خواسته شد، از نام کاربری و رمز عبور خود استفاده کنید. 
>
> بدیهی است که افزودن چاپگر به دسترسی root نیاز دارد. (در اوبونتو، اعضای گروه ادمین cups نیز پذیرفته می‌شوند).

```
### cups tree of menus:
- خانه (Home)
- مدیریت (Administration) ---> برای افزودن چاپگرها، مدیریت کارها و پیکربندی نحوه عملکرد CUPS به عنوان سرور.
- کلاس‌ها (Classes)
- راهنما (Help)
- کارها (Jobs) ---> برای بررسی کارهایی که CUPS در حال مدیریت آن‌هاست.
- چاپگرها (Printers) ---> نمایش چاپگرها
```

{% hint style="info" %}
شما باید بدانید از چه درایوری برای چاپگر خود استفاده کنید. همه چاپگرها به طور کامل در لینوکس پشتیبانی نمی‌شوند و برخی ممکن است اصلاً کار نکنند یا با محدودیت‌هایی مواجه باشند. وب‌سایت سازنده چاپگر را بررسی کنید یا به OpenPrinting.org نگاهی بیندازید.
{% endhint %}

### /etc/cups

دایرکتوری `/etc/cups` شامل سایر فایل‌های پیکربندی مربوط به CUPS است (Fedora 30).

```
[root@earth ~]# ls -l /etc/cups
total 100
-rw-------  1 root lp     128 Dec  9 15:01 classes.conf
-rw-r-----  1 root lp     128 Dec  9 14:47 classes.conf.O
-rw-r--r--. 1 root lp       0 Nov  9  2018 client.conf
-rw-r--r--. 1 root root 27075 Apr 25  2018 cups-browsed.conf
-rw-r-----. 1 root lp    6278 Nov  9  2018 cupsd.conf
-rw-r-----. 1 root lp    6278 Nov  9  2018 cupsd.conf.default
-rw-r-----. 1 root lp    3000 Dec  4  2018 cups-files.conf
-rw-r-----. 1 root lp    3000 Nov  9  2018 cups-files.conf.default
-rw-r--r--. 1 root lp       0 Nov  9  2018 lpoptions
drwxr-xr-x. 2 root lp    4096 Dec  9 14:56 ppd
-rw-------  1 root lp     646 Jan  1 14:55 printers.conf
-rw-------  1 root lp     596 Jan  1 14:30 printers.conf.O
-rw-r--r--. 1 root lp     142 Nov  9  2018 snmp.conf
-rw-r-----. 1 root lp     142 Nov  9  2018 snmp.conf.default
drwx------. 2 root lp    4096 Nov  9  2018 ssl
-rw-r-----  1 root lp     421 Feb 19 17:39 subscriptions.conf
-rw-r-----  1 root lp     421 Feb 19 16:41 subscriptions.conf.O
-rw-r--r--. 1 root root    91 Dec  8  2018 thnuclnt.convs
-rw-r--r--. 1 root root    75 Dec  8  2018 thnuclnt.types
```

فایل پیکربندی CUPS معمولاً در `/etc/cups/cupsd.conf` قرار دارد.

```
MaxLogSize 0
#
# Configuration file for the CUPS scheduler.  See "man cupsd.conf" for a
# complete description of this file.
#

# Log general information in error_log - change "warn" to "debug"
# for troubleshooting...
LogLevel warn


# Only listen for connections from the local machine.
Listen localhost:631
Listen /var/run/cups/cups.sock

# Show shared printers on the local network.
Browsing On
BrowseLocalProtocols dnssd

# Default authentication type, when authentication is required...
DefaultAuthType Basic

# Web interface setting...
WebInterface Yes

# Restrict access to the server...
<Location />
  Order allow,deny
</Location>

# Restrict access to the admin pages...
<Location /admin>
  Order allow,deny
</Location>

# Restrict access to configuration files...
<Location /admin/conf>
  AuthType Default
  Require user @SYSTEM
  Order allow,deny
</Location>

# Restrict access to log files...
<Location /admin/log>
  AuthType Default
  Require user @SYSTEM
  Order allow,deny
</Location>

# Set the default printer/job policies...
...
```

> نکته ۱: پیکربندی پیش‌فرض CUPS، مدیریت را فقط به ماشین محلی محدود می‌کند.
>
> نکته ۲: بیشتر تنظیمات از طریق رابط وب در دسترس هستند و ویرایش دستی این فایل توصیه نمی‌شود.

اگر چاپگری پیکربندی شده باشد، تنظیمات آن در `/etc/cups/printers.conf` ذخیره می‌شود.

```
# Printer configuration file for CUPS v2.2.6
# Written by cupsd on 2020-01-01 14:55
# DO NOT EDIT THIS FILE WHEN CUPSD IS RUNNING
<DefaultPrinter HP-LaserJet-p2055d>
UUID urn:uuid:b55fdcb1-be5e-30a4-6fc5-d6e237a3c41a
AuthInfoRequired username,password
Info HP LaserJet p2055d
MakeModel HP LaserJet p2055d pcl3, hpcups 3.18.6
DeviceURI smb://172.16.130.2/HP%20LaserJet%20P2050%20Series%20PCL6
State Stopped
StateMessage Rendering completed
StateTime 1577877637
ConfigTime 1575890776
Reason paused
Type 36892
Accepting Yes
Shared No
JobSheets none none
QuotaPeriod 0
PageLimit 0
KLimit 0
OpPolicy default
ErrorPolicy stop-printer
</DefaultPrinter>

```

> **زمانی که CUPSD در حال اجراست این فایل را ویرایش نکنید!**

### رابط قدیمی LPD 

در سیستم‌های یونیکس و لینوکس، چاپ در ابتدا از زیرسیستم چاپ Berkeley Software Distribution (BSD) استفاده می‌کرد که شامل یک دیمون چاپگر (lpd) بود که به عنوان سرور اجرا می‌شد، و دستورات کلاینت مانند `lpr` برای ارسال کارها جهت چاپ.

امروزه بسیاری از این ابزارهای قدیمی برای حفظ سازگاری با گذشته (backward compatibility) هنوز وجود دارند.

| دستور | کاربرد |
| ---------- | -------------------- |
| lpr        | ارسال فایل به چاپگر |
| lpq        | نمایش کارهای چاپ      |
| lprm       | حذف کارهای چاپ    |
| lpc status | نمایش وضعیت چاپگر  |

**lpq**: حرف `q` مخفف `queue` (صف) است و زمانی مفید است که می‌خواهیم کارهای چاپگر را ببینیم.

* `-P` : نمایش کارهای یک چاپگر خاص
* `-a` : نمایش کارهای تمام چاپگرها.

```
[root@earth ~]# lpq -PHP-LaserJet-p2055d
HP-LaserJet-p2055d is not ready
Rank    Owner   Job     File(s)                         Total Size
1st     payam    15      Untitled Document 1             13312 bytes
2nd     payam    16      MOP template.docx               16384 bytes
```

> بین `-P` و نام چاپگر نباید فاصله باشد! به صورت `-Pprintername` بنویسید.

**lpr**: ساده‌ترین راه برای چاپ هر فایلی استفاده از دستور `lpr` و ارائه نام فایل است. باز هم از `-P` برای تعیین چاپگر استفاده کنید:

```
[root@earth ~]#  lpr -PHP-LaserJet-p2055d minicom.log 
[root@earth ~]# lpq
HP-LaserJet-p2055d is not ready
Rank    Owner   Job     File(s)                         Total Size
1st     payam   15      Untitled Document 1             13312 bytes
2nd     payam   16      MOP template.docx               16384 bytes
3rd     root    17      minicom.log                     2048 bytes

```

**lprm**: این دستور کار(ها) را از صف چاپگر حذف می‌کند. ما باید `Job ID` را برای این دستور مشخص کنیم؛ اگر هیچ شناسه کاری مشخص نشود، قدیمی‌ترین کار حذف می‌شود.

```
[root@earth ~]# lpq
HP-LaserJet-p2055d is not ready
Rank    Owner   Job     File(s)                         Total Size
1st     payam   15      Untitled Document 1             13312 bytes
2nd     payam   16      MOP template.docx               16384 bytes
3rd     root    17      minicom.log                     2048 bytes
[root@earth ~]# lprm 15
[root@earth ~]# lpq
HP-LaserJet-p2055d is not ready
Rank    Owner   Job     File(s)                         Total Size
1st     payam   16      MOP template.docx               16384 bytes
2nd     root    17      minicom.log                     2048 bytes
[root@earth ~]# lprm
[root@earth ~]# lpq
HP-LaserJet-p2055d is not ready
Rank    Owner   Job     File(s)                         Total Size
1st     root    17      minicom.log                     2048 bytes
[root@earth ~]# 
```

> مجدداً می‌توان از `-P` برای تعیین چاپگر استفاده کرد. همچنین می‌توانیم از `lprm -Pprintername -` برای حذف تمام کارهای یک چاپگر خاص استفاده کنیم. و `lprm -` تمام کارها را از چاپگر پیش‌فرض حذف می‌کند.
>
> هر کاربر می‌تواند کارهای خودش را حذف کند، اما root می‌تواند هر کاری انجام دهد!

**lpc**: ما می‌توانیم از دستور `lpc status` برای بررسی سلامت چاپگر و عیب‌یابی استفاده کنیم (برای نتایج بهتر، این کار را به عنوان root انجام دهید!).

```
[root@earth ~]# lpc status
HP-LaserJet-p2055d:
    printer is on device 'smb' speed -1
    queuing is enabled
    printing is disabled
    1 entries
    daemon present
```

که در آن: 

* **queuing is enabled**: به این معنی است که چاپگر کارهای جدید را می‌پذیرد. اگر غیرفعال باشد، هیچ کار جدیدی را نمی‌پذیرد حتی اگر چاپگر سالم باشد.
* **printing is disabled**: به این معنی است که چاپگر در حال حاضر نمی‌تواند روی کاغذ چاپ کند. این اتفاق زمانی می‌افتد که جوهر یا کاغذ تمام شده باشد یا کاغذ در دستگاه گیر کرده باشد (jam).

در مورد من، کاغذ چاپگر تمام شده است. اما دستورات دیگری از CUPS وجود دارند که ممکن است به خصوص هنگام بروز مشکل مفید باشند: 

| دستور | شرح |
| --------------- | ------------------------------------------------ |
| **cupsaccept**  | به صف چاپگر می‌گوید که کارهای جدید را بپذیرد       |
| **cupsreject**  | به چاپگر می‌گوید که کارهای جدید را رد کند          |
| **cupsenable**  | چاپ واقعی/فیزیکی کارها را فعال می‌کند |
| **cupsdisable** | چاپ فیزیکی کارها را غیرفعال می‌کند       |

 در تمام این دستورات می‌توانیم نام چاپگر را بدون سوئیچ `-P` مشخص کنیم!

```
[root@earth ~]# lpc status
HP-LaserJet-p2055d:
    printer is on device 'smb' speed -1
    queuing is enabled
    printing is disabled
    1 entries
    daemon present
```

افزودن مقداری کاغذ:

```
[root@earth ~]# cupsenable HP-LaserJet-p2055d

[root@earth ~]# lpc status
HP-LaserJet-p2055d:
    printer is on device 'smb' speed -1
    queuing is enabled
    printing is enabled
    1 entries
    daemon present
```

و اگر بخواهیم یک چاپگر را عمداً غیرفعال کنیم، می‌توانیم دلیل آن را با سوئیچ `-r` ذکر کنیم:

```
[root@earth ~]# cupsreject HP-LaserJet-p2055d -r "Adding more paper"
[root@earth ~]# lpc status
HP-LaserJet-p2055d:
    printer is on device 'smb' speed -1
    queuing is disabled
    printing is enabled
    1 entries
    daemon present
```

تمام!

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-108-4/](https://developer.ibm.com/tutorials/l-lpic1-108-4/)

[https://wiki.archlinux.org/index.php/CUPS](https://wiki.archlinux.org/index.php/CUPS)

[https://www.linux.com/tutorials/linux-101-printing/](https://www.linux.com/tutorials/linux-101-printing/)

[https://jadi.gitbooks.io/lpic1/content/1084\_manage_printers_and_printing.html](https://jadi.gitbooks.io/lpic1/content/1084\_manage_printers_and_printing.html)

[https://www.cups.org/](https://www.cups.org/faq.html)

.
