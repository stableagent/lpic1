# 108.3 مبانی Mail Transfer Agent (MTA)

**وزن:** ۳

**توضیحات:** داوطلبان باید از برنامه‌های رایج MTA آگاه باشند و بتوانند پیکربندی‌های اولیه forward و alias را روی یک هاست کلاینت انجام دهند. سایر فایل‌های پیکربندی پوشش داده نمی‌شوند.

**حوزه‌های کلیدی دانش:**

* ایجاد نام‌های مستعار (aliases) ایمیل
* پیکربندی ارسال مجدد (forwarding) ایمیل
* دانش در مورد برنامه‌های رایج MTA (مانند postfix، sendmail، qmail، exim) (بدون نیاز به پیکربندی)

**اصطلاحات و ابزارهای کاربردی:**

* \~/.forward
* sendmail emulation layer commands
* newaliases
* mail
* mailq
* postfix
* sendmail
* exim
* qmail

### MTAها

عوامل انتقال ایمیل (Mail Transfer Agents) ایمیل‌ها را بین کاربران و بین سیستم‌ها تحویل می‌دهند. بیشتر ایمیل‌های اینترنتی از پروتکل ساده انتقال ایمیل (SMTP) استفاده می‌کنند، اما ایمیل‌های محلی ممکن است از طریق روش‌های دیگر منتقل شوند. انواع مختلفی از MTAها در دسترس هستند و هر توزیع ممکن است یکی از آن‌ها را به صورت پیش‌فرض داشته باشد. به عنوان مدیر سیستم، ما می‌توانیم MTA مناسب را بر اساس نیازهای خود انتخاب کنیم.

### sendmail

Sendmail قدیمی‌ترین MTA لینوکس است. این برنامه در اصل از برنامه delivermail که در سال ۱۹۷۹ در ARPANET استفاده می‌شد، مشتق شده است. sendmail حجیم است و پیکربندی آن آسان نیست. در طول سال‌ها، آسیب‌پذیری‌های زیادی در sendmail پیدا شده است، اگرچه اکنون کمی بهبود یافته است.

#### سایر عوامل انتقال ایمیل (MTAs)

در پاسخ به مشکلات امنیتی sendmail، چندین عامل انتقال ایمیل دیگر در طول دهه ۱۹۹۰ توسعه یافتند. Postfix احتمالاً محبوب‌ترین آن‌هاست، اما qmail و exim نیز به طور گسترده استفاده می‌شوند.

### qmail

Qmail یکی از امن‌ترین نرم‌افزارهای سرور ایمیل لینوکس موجود در بازار است. اگرچه پشتیبانی نمی‌شود و در حال حاضر در حال توسعه نیست (Qmail از سال ۱۹۹۷ به‌روزرسانی نشده است)، اما طرفداران زیادی دارد. 

Qmail همچنین سریع‌تر است و در بارهای بالای ایمیل، بهتر از Sendmail مقیاس‌پذیر می‌شود. با این حال، پیکربندی یا گسترش Qmail آسان نیست.

> Qmail تحت لیسانس GPL نیست، بلکه در مالکیت عمومی (public domain) قرار دارد.

### exim

Exim از سال ۱۹۹۵ عرضه شده و از آن زمان تاکنون محبوبیت آن رو به افزایش بوده است. بزرگترین نقطه قوت Exim سطح شخصی‌سازی تقریباً بی‌نهایت آن است. Exim به مدیر سرور این امکان را می‌دهد که مجموعه‌ای از قوانین سفارشی ایجاد کند تا ایمیل‌های ورودی و خروجی را به هر روش خاصی مدیریت کند (احراز هویت، لیست کنترل دسترسی).

اگرچه Exim برای عملکرد بالا طراحی نشده بود، اما می‌توان آن را طوری پیکربندی کرد که به عنوان یک سرور ایمیل با کارایی بالا عمل کند. اگر نیاز به ایجاد یک پیکربندی ایمیل پیچیده یا سفارشی دارید، Exim یک MTA عالی است. 

### postfix

Postfix احتمالاً سریع‌ترین MTA در حال رشد در بازار امروز است. Postfix به دلیل عملکرد بالا و سابقه امنیتی گذشته‌اش بسیار محبوب است. نفوذ به کاربر root در سروری که Postfix را اجرا می‌کند، بسیار دشوارتر (یا تقریباً غیرممکن) از سرورهایی است که مثلاً Sendmail یا Exim را اجرا می‌کنند. 

Postfix همچنین با منابع سیستمی کمتر، سریع‌تر از اکثر MTAهای دیگر (حداقل با تنظیمات استاندارد) اجرا می‌شود. ایجاد تنظیمات استاندارد آسان است، اما اگر به تنظیمات خاص و منحصربه‌فرد نیاز داشته باشید، کار با Postfix می‌تواند دشوار باشد. 

### لایه شبیه‌ساز sendmail

 از آنجایی که sendmail برای مدت طولانی وجود داشته است، صرف‌نظر از اینکه از چه سرور ایمیلی استفاده می‌کنیم، معمولاً با یک لایه شبیه‌ساز sendmail ارائه می‌شود. به عبارت دیگر، سایر MTAها شامل ابزارهایی هستند که با نسخه‌های قبلی سازگار (backward compatible) هستند. بنابراین اگر دستورات `sendmail` یا `mailq` را تایپ کنیم، آن‌ها طوری پاسخ می‌دهند و عمل می‌کنند که گویی sendmail نصب شده است.

### نام‌های مستعار (Mail aliases) <a href="mail-aliases" id="mail-aliases"></a>

گاهی اوقات ممکن است بخواهید تمام ایمیل‌های یک کاربر به جای دیگری برود. به عنوان مثال، ممکن است مجموعه‌ای از سرورها داشته باشید و بخواهید تمام ایمیل‌های root به یک مدیر سیستم مرکزی ارسال شود. یا ممکن است بخواهید یک لیست پستی ایجاد کنید که در آن ایمیل به چندین نفر ارسال شود. برای انجام این کار، ما از aliases استفاده می‌کنیم که به ما اجازه می‌دهد یک یا چند مقصد را برای یک نام کاربری مشخص تعریف کنیم. 

نام‌های مستعار ایمیل در فایل `/etc/aliases` قرار دارند. قبل از اصلاح این فایل باید کاربر root شوید: (در CentOS7، خروجی خلاصه شده است)

```
[root@centos7-1 ~]# cat /etc/aliases
#
#  Aliases in this file will NOT be expanded in the header from
#  Mail, but WILL be visible over networks or from /bin/mail.
#
#    >>>>>>>>>>    The program "newaliases" must be run after
#    >> NOTE >>    this file is updated for any changes to
#    >>>>>>>>>>    show through to sendmail.
#


# Basic system aliases -- these MUST be present.
mailer-daemon:    postmaster
postmaster:    root

# General redirections for pseudo accounts.
bin:        root
daemon:        root
adm:        root
lp:        root
sync:        root
shutdown:    root
halt:        root
mail:        root
ftp:        root
nobody:        root
amandabackup:        root
xfs:        root
gdm:        root


lpic1:  root     ###<---------


# trap decode to catch security attacks
decode:        root

# Person who should get root's mail
#root:        marc
root:    payam   ###<-----------
```

بنابراین اگر پیامی برای `"lpic1"` وجود داشته باشد، به کاربر `root` ارسال خواهد شد. 

خط آخر می‌گوید که `payam` در حال خواندن ایمیل‌های `root` است. این به `payam` اجازه می‌دهد تا ایمیل‌های `root` را بدون لاگین با root بخواند.

### newaliases

 تغییرات در فایل `/etc/aliases` تا زمانی که دستور `newaliases` برای ساخت فایل `/etc/aliases.db` اجرا نشود، کامل نمی‌گردد.

```
[root@centos7-1 ~]# newaliases
[root@centos7-1 ~]# 
```

### دستور mail

راه‌های زیادی برای ارسال ایمیل هنگام استفاده از رابط گرافیکی (GUI)، استفاده از مرورگر یا یک کلاینت ایمیل وجود دارد. اما گزینه‌ها در رابط خط فرمان (CLI) محدودتر هستند.

   دستور `mail` یک ابزار قدیمی و قابل اعتماد است که می‌تواند برای اسکریپت‌نویسی جهت ارسال ایمیل و همچنین دریافت و مدیریت ایمیل‌های ورودی شما استفاده شود. (در CentOS پکیج `mailx` را نصب کنید)

| مثال دستور mail                                | کاربرد                     |
| --------------------------------------------------- | ------------------------- |
| mail -s “subject” user1@domain.com                  | ارسال یک ایمیل          |
| mail -s “subject” user1@domain.com < /root/test.txt | ارسال یک ایمیل از یک فایل |
| mail -s “subject” user1@domain.com -a /path/to/file | افزودن یک پیوست (attachment)         |

> همچنین می‌تواند از داخل اسکریپت‌ها ایمیل ارسال کند، مانند: 'echo -e "email content" | mail -s "email subject" "example@example.com"'

می‌توانیم از `mail` به صورت تعاملی برای ارسال پیام با دادن لیستی از آدرس‌ها استفاده کنیم، یا بدون هیچ آرگومانی از آن برای مشاهده ایمیل‌های ورودی خود استفاده کنیم.

```
[user1@centos7-1 ~]$ mail lpic1@localhost
Subject: test lpic1
Hi this is my first email.
I am sending it to lpic1 email address.
EOT
```

به دلیل تغییراتی که در aliases ایجاد کردیم، هر ایمیلی که به آدرس ایمیل `lpic1` ارسال شود به `root` فوروارد می‌شود و از آنجایی که `payam` را به عنوان کسی که باید ایمیل‌های `root` را دریافت کند تعریف کرده‌ایم، ایمیل به او می‌رسد:

```
[payam@centos7-1 ~]$ mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/payam": 1 message 1 new
>N  1 user1@centos7-1.loca  Wed Feb 19 05:10  19/683   "test lpic1"
& 1
Message  1:
From user1@centos7-1.localdomain  Wed Feb 19 05:10:35 2020
Return-Path: <user1@centos7-1.localdomain>
X-Original-To: lpic1@localhost
Delivered-To: lpic1@localhost.localdomain
Date: Wed, 19 Feb 2020 05:10:35 -0500
To: lpic1@localhost.localdomain
Subject: test lpic1
User-Agent: Heirloom mailx 12.5 7/5/10
Content-Type: text/plain; charset=us-ascii
From: user1@centos7-1.localdomain
Status: R

Hi this is my first email.
I am sending it to lpic1 email address.

& d
& q
```

> هر زمان که ایمیلی ارسال می‌شود، ابتدا دستور mail باینری mail را فراخوانی می‌کند که به نوبه خود به MTA محلی متصل می‌شود تا ایمیل را به مقصدش بفرستد. MTA محلی یک سرور smtp است که روی لوکال اجرا می‌شود و ایمیل‌ها را روی پورت ۲۵ می‌پذیرد.

### \~/.forward

فایل aliases باید توسط مدیر سیستم مدیریت شود. کاربران عادی می‌توانند با استفاده از یک فایل `.forward` در دایرکتوری خانگی خود، ارسال مجدد (forwarding) ایمیل‌هایشان را فعال کنند. شما می‌توانید هر چیزی را که در سمت راست فایل aliases مجاز است، در فایل `.forward` خود قرار دهید. این فایل شامل متن ساده است و نیازی به کامپایل شدن ندارد. زمانی که ایمیلی برای شما ارسال می‌شود، sendmail وجود فایل `.forward` را در دایرکتوری خانگی شما بررسی کرده و ورودی‌های آن را به همان روشی که aliases را پردازش می‌کند، پردازش می‌نماید.

```
[user1@centos7-1 ~]$ pwd
/home/user1
[user1@centos7-1 ~]$ ls -la | grep -i forward
[user1@centos7-1 ~]$ vi .forward
[user1@centos7-1 ~]$ cat .forward 
user2
```

حالا بیایید یک ایمیل به user1 بفرستیم و صندوق پستی آن را بررسی کنیم:

```
[payam@centos7-1 ~]$ mail user1@localhost
Subject: mail to user1
Hello!!!
EOT
```

```
[user1@centos7-1 ~]$ mail
No mail for user1
```

```
[user2@centos7-1 ~]$ mail
Heirloom Mail version 12.5 7/5/10.  Type ? for help.
"/var/spool/mail/user2": 1 message 1 new
>N  1 payam@centos7-1.loca  Wed Feb 19 06:33  21/771   "mail to user1"
& 1
Message  1:
From payam@centos7-1.localdomain  Wed Feb 19 06:33:28 2020
Return-Path: <payam@centos7-1.localdomain>
X-Original-To: user1@localhost
Delivered-To: user2@centos7-1.localdomain
Delivered-To: user1@localhost.localdomain
Date: Wed, 19 Feb 2020 06:33:27 -0500
To: user1@localhost.localdomain
Subject: mail to user1
User-Agent: Heirloom mailx 12.5 7/5/10
Content-Type: text/plain; charset=us-ascii
From: payam@centos7-1.localdomain
Status: R

Hello!!!

& d
& q
[user2@centos7-1 ~]$ 
```

### mailq

 مدیریت ایمیل در لینوکس از مدل ذخیره و ارسال (store-and-forward) استفاده می‌کند. قبلاً دیدید که ایمیل‌های ورودی شما تا زمانی که آن‌ها را بخوانید در فایلی در `/var/mail` ذخیره می‌شوند. ایمیل‌های خروجی نیز تا زمانی که اتصال به سرور گیرنده در دسترس باشد، ذخیره می‌گردند. برای مشاهده ایمیل‌هایی که در صف (queue) هستند، از دستور `mailq` استفاده می‌کنید.

```
[root@centos7-1 ~]# mailq
Mail queue is empty
[root@centos7-1 ~]# mailq
-Queue ID- --Size-- ----Arrival Time---- -Sender/Recipient-------
E085461E470C      473 Wed Feb 19 06:39:07  payam@centos7-1.localdomain
(Host or domain name not found. Name service error for name=gmail.com type=MX: Host not found, try again)
                                         test@gmail.com

-- 0 Kbytes in 1 Request.
```

بدیهی است که در یک سیستم سالم، `mailq` همیشه باید خالی باشد.

.

.

.

[https://en.wikipedia.org/wiki/Message_transfer_agent](https://en.wikipedia.org/wiki/Message_transfer_agent)

[https://developer.ibm.com/tutorials/l-lpic1-108-3/](https://developer.ibm.com/tutorials/l-lpic1-108-3/)

[https://jadi.gitbooks.io/lpic1/content/1083\_mail_transfer_agent_mta_basics.html](https://jadi.gitbooks.io/lpic1/content/1083\_mail_transfer_agent_mta_basics.html)

[http://linuxconsultant.info/tutorials/linux-mail-server-software.html](http://linuxconsultant.info/tutorials/linux-mail-server-software.html)

[https://hoststud.com/resources/comparison-between-mtas-postfix-vs-exim-vs-sendmail.158/](https://hoststud.com/resources/comparison-between-mtas-postfix-vs-exim-vs-sendmail.158/)

با تشکر ویژه از Shawn Powers.
