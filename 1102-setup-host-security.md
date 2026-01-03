# 110.2 راه‌اندازی امنیت هاست

**وزن:** ۳

**توضیحات:** داوطلبان باید بدانند چگونه سطح پایه‌ای از امنیت هاست را راه‌اندازی کنند.

**حوزه‌های کلیدی دانش:**

* آگاهی از رمزهای عبور shadow و نحوه عملکرد آن‌ها
* خاموش کردن سرویس‌های شبکه که مورد استفاده نیستند
* درک نقش TCP wrapperها

**اصطلاحات و ابزارهای کاربردی:**

* /etc/nologin
* /etc/passwd
* /etc/shadow
* /etc/xinetd.d/
* /etc/xinetd.conf
* /etc/inetd.d/
* /etc/inetd.conf
* /etc/inittab
* /etc/init.d/
* /etc/hosts.allow
* /etc/hosts.deny

ما قبلاً در مورد `/etc/passwd` ، `/etc/nologin` و `/etc/shadow` صحبت کرده‌ایم. پس بیایید سریع آن‌ها را مرور کنیم:

### /etc/passwd

 فایل **/etc/passwd** اطلاعات ضروری مورد نیاز در هنگام لاگین را ذخیره می‌کند. به عبارت دیگر، این فایل اطلاعات حساب کاربری را در خود دارد. این یک فایل متنی ساده است.

```
root@ubuntu16-1:~# tail /etc/passwd
pulse:x:117:124:PulseAudio daemon,,,:/var/run/pulse:/bin/false
rtkit:x:118:126:RealtimeKit,,,:/proc:/bin/false
saned:x:119:127::/var/lib/saned:/bin/false
usbmux:x:120:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
payam:x:1000:1000:ubuntu16.04.3-1,,,:/home/payam:/bin/bash
user1:x:1001:1001::/home/user1:/bin/bash
sshd:x:121:65534::/var/run/sshd:/usr/sbin/nologin
mysql:x:122:129:MySQL Server,,,:/nonexistent:/bin/false
user3:x:1003:1003::/home/user3:
user2:x:1004:1004::/home/user2:/bin/bash
```

 هر خط در این فایل نشان‌دهنده یک حساب کاربری مجزا است و شامل هفت فیلد زیر است که با دو نقطه (**:**) از هم جدا شده‌اند:

۱. نام کاربری (Username)
۲. رمز عبور رمزگذاری شده
۳. شناسه کاربر (UID)
۴. شناسه گروه (GID)
۵. توضیحات کاربر (Comment)
۶. دایرکتوری خانگی کاربر
۷. شل لاگین کاربر

فایل `/etc/passwd` باید دارای مجوز خواندن عمومی باشد زیرا بسیاری از ابزارها از آن برای نگاشت شناسه‌های کاربر به نام‌های کاربری استفاده می‌کنند. با این حال، دسترسی نوشتن به این فایل باید فقط محدود به کاربر ارشد (root) باشد.

```
root@ubuntu16-1:~# ls -l /etc/passwd
-rw-r--r-- 1 root root 2470 Mar 23 01:24 /etc/passwd
```

همانطور که گفتیم، در گذشته این فایل مکانی بود که تمام اطلاعات کاربران از جمله رمز عبور در آن قرار داشت و این موضوع باعث مشکلات امنیتی می‌شد. برای حل این مشکل، `/etc/shadow` ابداع شد. کاراکتر `x` نشان می‌دهد که رمز عبور رمزگذاری شده در فایل `/etc/shadow` ذخیره شده است.

### /etc/shadow

فایل `/etc/shadow` شامل رمزهای عبور رمزگذاری شده به همراه اطلاعات انقضای رمز عبور و حساب کاربری است.

```
root@ubuntu16-1:~# tail -5 /etc/shadow
user1:$6$c9PN.175$.t.CG0E0Gtr/trq4pqquSe1BemMjB6Zc3E0ExUOVufuTkPNe3BSRv3DyUuXFHPiAbEujzuSMCeMsCbpg8cV2j.:17749:0:99999:7:::
sshd:*:17749:0:99999:7:::
mysql:!:17867:0:99999:7:::
user3:$6$LPhxz61y$V0/HsC6SF6olabfoKuDKj3Gfm.wRVvbUI.GXWGJ2Icejs91ZQDgZHIAc153x4VYrJcg.oetNlwq.X7xxJIRDR0:18343:0:99999:7:::
user2:$6$sd2T1NDd$ihaP8mO7/rhSUFDccY3evqQqM/VwDVDYSQwZJstV6Dnjy3Me83faro6pk/Nb1GuvRqy8J66ZBHIGqIpIrzl5a0:18343:0:99999:7:::
```

 هر خط در این فایل نشان‌دهنده یک حساب کاربری مجزا است و شامل نه فیلد زیر است که با دو نقطه (**:**) از هم جدا شده‌اند:

۱. نام کاربری
۲. رمز عبور رمزگذاری شده
۳. تاریخ آخرین تغییر رمز عبور
۴. حداقل روزهای مورد نیاز بین تغییرات رمز عبور
۵. حداکثر روزهای مجاز بین تغییرات رمز عبور
۶. تعداد روزهای قبل از انقضا برای نمایش پیام هشدار
۷. تعداد روزهای پس از انقضا برای غیرفعال کردن حساب
۸. تاریخ انقضای حساب
۹. فیلد رزرو شده

 برخلاف فایل `/etc/passwd` ، فایل `/etc/shadow` برای همه قابل خواندن نیست. این فایل فقط توسط کاربر root یا کاربر ارشد قابل خواندن است. 

```
root@ubuntu16-1:~# ls -l /etc/shadow
-rw-r----- 1 root shadow 1704 Mar 23 01:23 /etc/shadow
```

### /etc/nologin

```
root@ubuntu16-1:~# cat /etc/nologin
cat: /etc/nologin: No such file or directory
```

 اگر فایل `/etc/nologin` وجود داشته باشد و قابل خواندن باشد، لاگین فقط برای root مجاز خواهد بود. به سایر کاربران محتویات این فایل نشان داده شده و ورود آن‌ها رد می‌شود. با حذف این فایل، کاربران دوباره قادر به لاگین خواهند بود.

> فایل `/etc/nologin` در حین ریبوت توسط اسکریپت شات‌داون حذف می‌شود.

### خاموش کردن سرویس‌های شبکه

 به عنوان مدیر سیستم، وظیفه ماست که سرویس‌های غیرضروری در حال اجرا را پیدا کرده و برای به حداقل رساندن خطرات امنیتی، آن‌ها را غیرفعال کنیم. قبلاً یاد گرفتیم که توزیع‌های مختلف لینوکس از راه‌کارهای راه‌اندازی متفاوتی در هنگام بوت استفاده می‌کنند؛ بنابراین از دستورات مناسب بر اساس مدیر سرویس خود برای غیرفعال کردن سرویس‌ها استفاده کنید:

| توزیع لینوکس                        | مدیر سرویس | دستور                                                                                                                                                |
| ----------------------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| سیستم‌های لینوکس قدیمی‌تر (قبل از ۲۰۰۶)      | SysV            | <p><strong>chkconfig </strong><em>ServiceName</em> <strong>off</strong><br><strong>sysv-rc-conf</strong> <em>ServiceName</em> <strong>off</strong></p> |
| Ubuntu(2006-2019),CentOS(2011-2020) | Upstart         | **update-rc.d** _ServiceName _**remove**                                                                                                               |
| ubuntu(2015-????),CentOS(2014-????) | systemd         | **systemctl disable** _ServiceName_                                                                                                                    |

 لطفاً توجه داشته باشید که این دستورات از شروع به کار سرویس در هنگام بوت سیستم جلوگیری می‌کنند. اما پکیج مربوطه هنوز روی ماشین نصب است و در صورت نیاز می‌توانیم آن را اجرا کنیم.

### سوپر سرورها (Super Servers)

در بیشتر سیستم‌های یونیکس، سرویس‌های شبکه به صورت دیمون (daemon) پیاده‌سازی می‌شوند. هر دیمون شبکه به درخواست‌ها روی یک پورت خاص پاسخ می‌دهد. به عنوان مثال، سرویس Telnet روی پورت ۲۳ کار می‌کند. برای اینکه سرویس‌های شبکه به درستی کار کنند، باید فرآیندی روی هر پورت مربوطه در حال گوش دادن باشد. دو راه برای ارائه سرویس‌های TCP/IP وجود دارد: 

* با اجرای اپلیکیشن‌های سرور به صورت مستقل (standalone) به عنوان یک دیمون 
* یا با استفاده از یک سوپر سرور اینترنت (Internet super server)

این سوپر سرور یک دیمون خاص است که به پورت‌های تمام سرویس‌های شبکه فعال گوش می‌دهد. هنگامی که درخواستی از یک پورت خاص می‌رسد، دیمون شبکه مربوطه شروع به کار کرده و درخواست برای سرویس‌دهی به آن منتقل می‌شود.

![](.gitbook/assets/securehost-superserver.jpg)

این طرح دو مزیت اصلی دارد: اول اینکه در هر لحظه فقط مجموعه حداقلی از دیمون‌های مورد نیاز فعال هستند و در نتیجه منابع سیستم هدر نمی‌روند. دوم اینکه یک مکانیزم متمرکز برای مدیریت و نظارت بر سرویس‌های شبکه وجود دارد.

عیب سوپر سرور این است که راه‌اندازی آن زمان‌بر است که باعث افزایش زمان پاسخگویی سرویس شبکه می‌شود.

{% hint style="info" %}
سوپر سرورها دیگر چندان مورد استفاده قرار نمی‌گیرند و اکثر توزیع‌ها از سرویس‌های مستقل (standalone) استفاده می‌کنند.
{% endhint %}

### inetd , xinetd

دو سوپر سرور اصلی اینترنت برای لینوکس وجود دارد: `inetd` و `xinetd`. اگرچه `inetd` سوپر سرور استاندارد در اکثر توزیع‌های لینوکس بود، اما به تدریج با `xinetd` که ویژگی‌های بیشتری دارد جایگزین شده است. اما چون `inetd` ویژگی‌های کمتری نسبت به `xinetd` دارد، حجم آن نیز کمتر است و ممکن است برای سیستم‌های لینوکس تعبیه شده (embedded) بهتر باشد.

#### فایل‌های پیکربندی inetd

### /etc/inetd.conf

 فایل **/etc/inetd.conf** فایل پیکربندی پیش‌فرض برای دیمون `inetd` است. این فایل به شما امکان می‌دهد دیمون‌هایی را که باید به صورت پیش‌فرض شروع شوند مشخص کنید و آرگومان‌های مربوط به نحوه عملکرد هر دیمون را ارائه دهید. بیایید نگاهی به یک خط نمونه از `inetd.conf` بیندازیم:

```
# File Transfer Protocol (FTP) server:
ftp     stream  tcp     nowait  root    /usr/sbin/tcpd  proftpd
```

### /etc/inetd.d/

دایرکتوری `/etc/inetd.d` شامل فایل‌های پیکربندی برای هر سرویس مدیریت شده توسط `inetd` است و نام فایل‌ها با نام سرویس‌ها مطابقت دارد.

#### فایل‌های پیکربندی xinetd

### /etc/xinetd.conf

 فایل `/etc/xinetd.conf` شامل تنظیمات پیکربندی عمومی است که بر هر سرویس تحت کنترل `xinetd` تأثیر می‌گذارد. این فایل یک بار هنگام شروع سرویس `xinetd` خوانده می‌شود، بنابراین برای اعمال تغییرات، مدیر سیستم باید سرویس `xinetd` را ری‌استارت کند. در زیر یک نمونه فایل `/etc/xinetd.conf` آورده شده است:

```
defaults
{
        instances               = 60
        log_type                = SYSLOG authpriv
        log_on_success          = HOST PID
        log_on_failure          = HOST
        cps                     = 25 30
}
includedir /etc/xinetd.d
```

### /etc/xinetd.d/

 دایرکتوری `/etc/xinetd.d/` حاوی فایل‌های پیکربندی برای هر سرویس مدیریت شده توسط `xinetd` است. مانند `xinetd.conf` ، این دایرکتوری فقط هنگام شروع سرویس `xinetd` خوانده می‌شود. برای درک ساختار این فایل‌ها، فایل `/etc/xinetd.d/telnet` را در نظر بگیرید:

```
service telnet
{
        flags           = REUSE
        socket_type     = stream
        wait            = no
        user            = root
        server          = /usr/sbin/in.telnetd
        log_on_failure  += USERID
        disable         = yes
}
```

 برای اعمال هرگونه تغییر، مدیر سیستم باید سرویس `xinetd` را ری‌استارت کند.

{% hint style="info" %}
 فایل `/etc/services` شامل لیست سرویس‌های شبکه و پورت‌های نگاشت شده به آن‌هاست. `inetd` یا `xinetd` به این جزئیات نگاه می‌کنند تا بتوانند برنامه خاصی را زمانی که پکتی به پورت مربوطه می‌رسد، فراخوانی کنند.
{% endhint %}

### TCP Wrapperها

 همانطور که در `/etc/inetd.conf` می‌بینید، اتصالات برای اکثر پروتکل‌ها از طریق **tcpd** انجام می‌شود، به جای اینکه مستقیماً به یک برنامه سرویس منتقل شود. برای مثال:

```
# File Transfer Protocol (FTP) server:
ftp     stream  tcp     nowait  root    /usr/sbin/tcpd  proftpd
```

 در این مثال، اتصالات FTP از طریق **tcpd** عبور می‌کنند. `tcpd` اتصال را از طریق syslog ثبت کرده و اجازه بررسی‌های اضافی را می‌دهد. یکی از پرکاربردترین ویژگی‌های `tcpd` ، کنترل دسترسی مبتنی بر هاست (host-based) است. یک TCP Wrapper در واقع یک سیستم لیست کنترل دسترسی (ACL) شبکه مبتنی بر هاست است و برای فیلتر کردن دسترسی شبکه استفاده می‌شود.

### /etc/hosts.allow , /etc/hosts.deny

   هنگامی که یک درخواست شبکه به سرور شما می‌رسد، TCP wrapperها از فایل‌های `hosts.allow` و `hosts.deny` (به همین ترتیب) استفاده می‌کنند تا تعیین کنند آیا کلاینت مجاز به استفاده از سرویس مورد نظر هست یا خیر.

به طور پیش‌فرض، این فایل‌ها خالی هستند، تمام خطوط آن‌ها کامنت شده است یا اصلاً وجود ندارند. بنابراین، همه چیز از لایه TCP wrapperها عبور می‌کند و سیستم شما برای محافظت کامل به فایروال متکی خواهد بود.

```
root@ubuntu16-1:~# cat /etc/hosts.allow 
# /etc/hosts.allow: list of hosts that are allowed to access the system.
#                   See the manual pages hosts_access(5) and hosts_options(5).
#
# Example:    ALL: LOCAL @some_netgroup
#             ALL: .foobar.edu EXCEPT terminalserver.foobar.edu
#
# If you're going to protect the portmapper use the name "rpcbind" for the
# daemon name. See rpcbind(8) and rpc.mountd(8) for further information.
#
```

```
root@ubuntu16-1:~# cat /etc/hosts.deny 
# /etc/hosts.deny: list of hosts that are _not_ allowed to access the system.
#                  See the manual pages hosts_access(5) and hosts_options(5).
#
# Example:    ALL: some.host.name, .some.domain
#             ALL EXCEPT in.fingerd: other.host.name, .other.domain
#
# If you're going to protect the portmapper use the name "rpcbind" for the
# daemon name. See rpcbind(8) and rpc.mountd(8) for further information.
#
# The PARANOID wildcard matches any host whose name does not match its
# address.
#
# You may wish to enable this to ensure any programs that don't
# validate looked up hostnames still leave understandable logs. In past
# versions of Debian this has been the default.
# ALL: PARANOID
```

هر دو فایل در هر خط دارای یک قانون به شکل زیر هستند: `service: hosts`

هاست‌ها می‌توانند با نام میزبان یا آدرس IP مشخص شوند. کلمه کلیدی `ALL` به تمام هاست‌ها یا تمام سرویس‌ها اشاره دارد.

به عنوان مثال، افزودن `telnet 192.168.` به `/etc/hosts.allow` باعث می‌شود فقط اتصالات telnet از محدوده IP ۱۹۲.۱۶۸.x.x پذیرفته شوند.

افزودن همین خط به `/etc/hosts.deny` باعث می‌شود اتصالات telnet از محدوده IP ۱۹۲.۱۶۸.x.x رد شوند، اما اتصالات از هر آدرس دیگری پذیرفته شوند.

> پس از تغییر این فایل‌ها، سرویس `xinetd` باید ری‌استارت شود.

تمام!

.

.

.

[https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/)

[https://www.computernetworkingnotes.com/rhce-study-guide/etc-passwd-file-in-linux-explained-with-examples.html](https://www.computernetworkingnotes.com/rhce-study-guide/etc-passwd-file-in-linux-explained-with-examples.html)

[https://www.cyberciti.biz/faq/understanding-etcshadow-file/](https://www.cyberciti.biz/faq/understanding-etcshadow-file/)

[https://www.computernetworkingnotes.com/rhce-study-guide/etc-shadow-file-in-linux-explained-with-examples.html](https://www.computernetworkingnotes.com/rhce-study-guide/etc-shadow-file-in-linux-explained-with-examples.html)

[https://linux.die.net/man/5/nologin](https://linux.die.net/man/5/nologin)

[https://jadi.gitbooks.io/lpic1/content/1102\_setup_host_security.html](https://jadi.gitbooks.io/lpic1/content/1102\_setup_host_security.html)

[http://etutorials.org/Linux+systems/embedded+linux+systems/Chapter+10.+Setting+Up+Networking+Services/10.1+The+Internet+Super-Server/](http://etutorials.org/Linux+systems/embedded+linux+systems/Chapter+10.+Setting+Up+Networking+Services/10.1+The+Internet+Super-Server/)

[https://en.wikipedia.org/wiki/Super-server](https://en.wikipedia.org/wiki/Super-server)

[https://thecustomizewindows.com/2011/11/super-server-what-is-it-how-it-works/](https://thecustomizewindows.com/2011/11/super-server-what-is-it-how-it-works/)

[https://www.ibm.com/support/knowledgecenter/ssw_aix\_72/filesreference/inetd.conf.html](https://www.ibm.com/support/knowledgecenter/ssw_aix\_72/filesreference/inetd.conf.html)

[https://book.huihoo.com/slackware-linux-basics/html/inetd.html](https://book.huihoo.com/slackware-linux-basics/html/inetd.html)

[https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/4/html/reference_guide/s1-tcpwrappers-xinetd-config](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/4/html/reference_guide/s1-tcpwrappers-xinetd-config)

[https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/4/html/reference_guide/s2-tcpwrappers-xinetd-config-files](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/4/html/reference_guide/s2-tcpwrappers-xinetd-config-files)

[https://kerneltalks.com/linux/understanding-etc-services-file-in-linux/](https://kerneltalks.com/linux/understanding-etc-services-file-in-linux/)

[https://www.tecmint.com/secure-linux-tcp-wrappers-hosts-allow-deny-restrict-access/](https://www.tecmint.com/secure-linux-tcp-wrappers-hosts-allow-deny-restrict-access/)

.
