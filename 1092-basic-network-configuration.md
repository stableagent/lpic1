# 109.2 پیکربندی پایه شبکه

**وزن:** ۴

**توضیحات:** داوطلبان باید قادر به مشاهده، تغییر و تأیید تنظیمات پیکربندی روی هاست‌های کلاینت باشند.

**حوزه‌های کلیدی دانش:**

* پیکربندی دستی و خودکار رابط‌های (interfaces) شبکه
* پیکربندی پایه هاست TCP/IP
* تنظیم یک مسیر پیش‌فرض (default route)

**اصطلاحات و ابزارهای کاربردی:**

* /etc/hostname
* /etc/hosts
* /etc/nsswitch.conf
* ifconfig
* ifup
* ifdown
* ip
* route
* ping

در این آموزش، یاد می‌گیریم که چگونه تنظیمات شبکه را در لینوکس دائمی کنیم.

### ifconfig

 ابزار **ifconfig** (مخفف interface configuration) برای پیکربندی یا مشاهده تنظیمات یک رابط شبکه از طریق رابط خط فرمان استفاده می‌شود (CentOS6).

```
ifconfig [...OPTIONS] [INTERFACE]
```

 دستور "ifconfig" بدون هیچ گزینه‌ای، اطلاعات پیکربندی فعلی شبکه را نمایش می‌دهد:

```
[root@centos6-1 ~]# 
[root@centos6-1 ~]# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:0C:29:6D:D2:C5  
          inet addr:172.16.43.137  Bcast:172.16.43.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe6d:d2c5/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:235444 errors:0 dropped:0 overruns:0 frame:0
          TX packets:81562 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:309940884 (295.5 MiB)  TX bytes:5027974 (4.7 MiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:2964 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2964 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:386271 (377.2 KiB)  TX bytes:386271 (377.2 KiB)
```

> `lo` رابط loopback است با آدرس IP 127.0.0.1 که توسط سیستم‌عامل برای ارتباطات داخلی خودش استفاده می‌شود.

| گزینه ifconfig | توضیحات                                                  |
| --------------- | ------------------------------------------------------------ |
| -a              | نمایش تمام رابط‌های موجود، حتی اگر غیرفعال (down) باشند. |
| -s              | نمایش یک لیست کوتاه |
| -v              | نمایش جزئیات بیشتر برای برخی شرایط خطا                    |

ما می‌توانیم از ifconfig برای تغییر تنظیمات شبکه استفاده کنیم، اما این کار به دسترسی root نیاز دارد:

| دستور ifconfig                                    | توضیحات          |
| --------------------------------------------------- | -------------------- |
| ifconfig eth0 172.16.43.155                         | اختصاص یک آدرس IP  |
| ifconfig eth0 netmask 255.255.255.224               | اختصاص یک Netmask     |
| ifconfig eth0 172.16.43.155 netmask 255.255.255.224 | اختصاص همزمان IP و Netmask |

ifconfig می‌تواند برای فعال یا غیرفعال کردن (up/down) یک رابط استفاده شود که مجدداً به دسترسی root نیاز دارد:

* `ifconfig interface up`: برای فعال کردن درایور رابط مورد نظر استفاده می‌شود.
* `ifconfig interface down`: برای غیرفعال کردن درایور رابط مورد نظر استفاده می‌شود.

{% hint style="danger" %}
اگر هنوز از ifconfig استفاده می‌کنید، در گذشته زندگی می‌کنید! دستور ifconfig منسوخ شده و دستور ip جایگزین آن شده است.
{% endhint %}

### ifup , ifdown

مشابه دستورات قبلی، `ifup` و `ifdown` برای فعال و غیرفعال کردن یک رابط استفاده می‌شوند.

```
[root@centos6-1 ~]# ifdown eth0
Device state: 3 (disconnected)
[root@centos6-1 ~]# ifup eth0
Active connection state: activating
Active connection path: /org/freedesktop/NetworkManager/ActiveConnection/10
state: activated
Connection activated
```

### درگاه (gateway)

یکی از مزایای تقسیم آدرس‌های IP به کلاس‌ها و زیرشبکه‌ها، کنترل پخش‌های همگانی (broadcasts) است که با استفاده از subnetmask انجام می‌شود. اما اگر دو کامپیوتر از دو شبکه متفاوت بخواهند با یکدیگر ارتباط برقرار کنند چه؟

یک **درگاه (gateway)** یک نود یا یک روتر است که به عنوان یک نقطه دسترسی برای انتقال داده‌های شبکه از شبکه‌های محلی به شبکه‌های راه دور عمل می‌کند.

آدرسی که به عنوان درگاه برای رسیدن به سایر شبکه‌های خارج از شبکه محلی ما استفاده می‌شود، **درگاه پیش‌فرض (default gateway)** نامیده می‌شود. راه‌های مختلفی برای تنظیم درگاه وجود دارد.

### فایل‌های پیکربندی شبکه

خبر بد این است که تمام تنظیماتی که با استفاده از دستور ifconfig انجام داده‌ایم دائمی نیستند و با راه‌اندازی مجدد سیستم یا فعال و غیرفعال شدن رابط، از بین می‌روند. بنابراین باید راهی پیدا کنیم تا تنظیمات خود را دائمی کنیم و تنها راه دستیابی به آن استفاده از فایل‌های پیکربندی شبکه است.

متأسفانه فایل‌های پیکربندی شبکه در لینوکس در مکان‌های مختلفی قرار دارند و این موضوع به توزیعی که در مورد آن صحبت می‌کنیم بستگی دارد.

#### سیستم‌های مبتنی بر RedHat 

در Redhat، CentOS و Fedora فایل‌ها در مسیر `/etc/sysconfig/network-scripts/` قرار دارند. 

```
[root@server1 ~]# ls /etc/sysconfig/network-scripts/
ifcfg-eth0   ifdown-ippp    ifdown-sit     ifup-ib     ifup-post      init.ipv6-global
ifcfg-lo     ifdown-ipv6    ifdown-tunnel  ifup-ippp   ifup-ppp       net.hotplug
ifdown       ifdown-isdn    ifup           ifup-ipv6   ifup-routes    network-functions
ifdown-bnep  ifdown-post    ifup-aliases   ifup-isdn   ifup-sit       network-functions-ipv6
ifdown-eth   ifdown-ppp     ifup-bnep      ifup-plip   ifup-tunnel
ifdown-ib    ifdown-routes  ifup-eth       ifup-plusb  ifup-wireless


```

و درگاه پیش‌فرض (default gateway) از طریق فایل `/etc/sysconfig/network` پیکربندی می‌شود:

```
[root@server1 Desktop]# cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=server1
GATEWAY=172.16.43.2
```

بیایید نگاهی به فایل پیکربندی eth0 بیندازیم:

```
[root@server1 ~]# cat  /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE="eth0"
BOOTPROTO=none
IPV6INIT="yes"
NM_CONTROLLED="yes"
ONBOOT="yes"
TYPE="Ethernet"
UUID="8f5774e8-607e-4629-8d54-a9cc4d12e851"
IPADDR=172.16.43.127
PREFIX=24
GATEWAY=172.16.43.2
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
NAME="System eth0"
HWADDR=00:0C:29:6D:D2:C5
DNS1=8.8.8.8
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
LAST_CONNECT=1582381005
```

#### سیستم‌های مبتنی بر Debian

در سیستم‌های مبتنی بر دبیان مانند اوبونتو، مکان اصلی فایل پیکربندی شبکه در `/etc/network/interfaces` است و فایل جداگانه‌ای برای تنظیمات درگاه وجود ندارد:

```
auto lo
iface lo inte loopback

auto eth0
#ifconfig eth0 inet dhcp
iface eth0 inet static 
address 172.16.43.135
netmask 255.255.255.0
gateway 172.16.43.2
dns-nameservers 8.8.8.8
```

همچنین ممکن است دایرکتوری `/etc/network/interfaces.d` برای فایل‌های پیکربندی وجود داشته باشد.

> دستورات `ifdown` و `ifup` از این فایل پیکربندی استفاده می‌کنند.

#### فایل پیکربندی DNS

### /etc/resolv.conf

همانطور که متوجه شدید، تنظیمات DNS در همان فایلی قرار دارد که تنظیمات رابط در آن است، اما مکان دیگری نیز در لینوکس وجود دارد که حاوی اطلاعات DNS است: `/etc/resolv.conf` :

```
[root@server1 Desktop]# cat /etc/resolv.conf 
# Generated by NetworkManager
nameserver 8.8.8.8
```

مجدداً، تنظیمات در این فایل دائمی نیستند و توصیه نمی‌شود که این فایل را به صورت دستی تغییر دهید، مگر برای تست‌های موقت.

### نام میزبان (hostname)

 **Hostname** برنامه‌ای است که برای تنظیم یا نمایش نام فعلی میزبان، دامنه یا نودِ سیستم استفاده می‌شود (در اینجا از CentOS استفاده می‌کنیم).

```
[root@server1 Desktop]# hostname
server1
[root@server1 Desktop]# hostname centos6-1
[root@server1 Desktop]# hostname
centos6-1
```

نام میزبان جدید با باز کردن یک ترمینال جدید ظاهر می‌شود اما با راه‌اندازی مجدد سیستم از بین می‌رود. برای پیکربندی دائمی نام میزبان، چند مکان دیگر وجود دارد که باید تغییر داده شوند:

1. `/etc/hostname` (در اوبونتو) یا `/etc/sysconfig/network` (در CentOS)
2. `/etc/hosts` (هم در اوبونتو و هم در CentOS)

### /etc/hostname

فایل `/etc/hostname` شامل نام ماشین است و یکی از فایل‌های پیکربندی است که باید برای دائمی کردن نام میزبان جدید در سیستم‌های مبتنی بر دبیان اصلاح شود (اوبونتو ۱۶ در اینجا).

```
root@ubuntu16-1:~# cat /etc/hostname 
ubuntu16-1
```

### /etc/sysconfig/network

مکان دیگری که حاوی نام میزبان است و باید در سیستم‌های مبتنی بر RedHat برای داشتن نام میزبان دائمی تغییر کند (CentOS6).

```
[root@server1 Desktop]# cat /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=server1   ###<---- آن را به centos6-1 در مثال ما تغییر دهید
GATEWAY=172.16.43.2
```

### /etc/hosts

 فایل **/etc/hosts** فایلی در سیستم‌عامل است که نام‌های میزبان یا دامنه‌ها را به آدرس‌های IP ترجمه می‌کند؛ این کار همان چیزی است که DNS انجام می‌دهد. ما می‌توانیم از آن برای اهداف تست یا زمانی که سرور DNS در دسترس نیست استفاده کنیم. فراموش نکنید که اولویت این فایل از DNS بالاتر است (یعنی سیستم‌عامل ابتدا داخل فایل `/etc/hosts` را برای یافتن آدرس IP یک میزبان جستجو می‌کند و اگر موفقیت‌آمیز نبود، از سرور DNS پرس‌وجو می‌کند).

```
[root@server1 Desktop]# cat /etc/hosts
127.0.0.1    centos6-1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1    server1    localhost localhost.localdomain localhost6 localhost6.localdomain6

```

برای دائمی کردن نام میزبان جدید، این فایل دیگری است که باید اصلاح شود.

### route

تمام دستگاه‌های شبکه، چه هاست باشند، چه روتر یا انواع دیگر نودهای شبکه مانند چاپگرهای متصل به شبکه، نیاز دارند تا در مورد مسیر هدایت پکت‌های داده TCP/IP تصمیم بگیرند. جدول مسیریابی (routing table) اطلاعات پیکربندی لازم برای اتخاذ این تصمیمات را فراهم می‌کند. دستور **route** برای مشاهده و ایجاد تغییرات در جدول مسیریابی هسته (kernel) استفاده می‌شود. 

{% hint style="danger" %}
دستور route تنظیمات موقتی ایجاد می‌کند، به جای آن از فایل‌های پیکربندی استفاده کنید!
{% endhint %}

 اجرای دستور **route** بدون هیچ گزینه‌ای، ورودی‌های جدول مسیریابی را نمایش می‌دهد:

```
[root@server1 Desktop]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.43.0     *               255.255.255.0   U     1      0        0 eth0
default         172.16.43.2     0.0.0.0         UG    0      0        0 eth0
```

> این نشان می‌دهد که سیستم در حال حاضر چگونه پیکربندی شده است. اگر پکتی وارد سیستم شود و مقصدی در محدوده **172.16.43.0** تا **172.16.43.255** داشته باشد، به درگاه **\*** ارسال می‌شود که همان **0.0.0.0** است - یک آدرس خاص که نشان‌دهنده یک مقصد نامعتبر یا ناموجود است. بنابراین در این حالت، سیستم ما این پکت‌ها را مسیریابی نخواهد کرد.
>
> اگر مقصد در این محدوده آدرس IP نباشد، به درگاه پیش‌فرض (در این مثال، **172.16.43.2**) فرستاده می‌شود و آن سیستم تعیین می‌کند که چگونه ترافیک را به مرحله بعدی به سمت مقصدش هدایت کند.

به طور پیش‌فرض دستور route نام میزبان را در خروجی خود نمایش می‌دهد. ما می‌توانیم با استفاده از گزینه `-n` از آن بخواهیم آدرس‌های IP عددی را نمایش دهد:

```
[root@server1 Desktop]# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.43.0     0.0.0.0         255.255.255.0   U     1      0        0 eth0
0.0.0.0         172.16.43.2     0.0.0.0         UG    0      0        0 eth0
```

 دستور `route add` زیر، درگاه پیش‌فرض را روی 172.16.43.2 تنظیم می‌کند:

```
[root@server1 ~]# route add default gw 172.16.43.1
[root@server1 ~]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.43.0     *               255.255.255.0   U     0      0        0 eth0
link-local      *               255.255.0.0     U     1002   0        0 eth0
default         172.16.43.1     0.0.0.0         UG    0      0        0 eth0
default         172.16.43.2     0.0.0.0         UG    0      0        0 eth0
```

از `route del` برای حذف استفاده کنید:

```
[root@server1 ~]# route del  default gw 172.16.43.1
[root@server1 ~]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.43.0     *               255.255.255.0   U     0      0        0 eth0
link-local      *               255.255.0.0     U     1002   0        0 eth0
default         172.16.43.2     0.0.0.0         UG    0      0        0 eth0
```

> هسته (Kernel) اطلاعات کش مسیریابی را برای مسیریابی سریع‌تر پکت‌ها نگه می‌دارد. ما می‌توانیم اطلاعات کش مسیریابی هسته را با استفاده از فلگ -C لیست کنیم.

> دستور `netstat -rn` نیز جدول مسیریابی را نشان می‌دهد.

### ip

این دستور جایگزین دستور قدیمی و اکنون منسوخ شده ifconfig شده است، با این حال، دستور ifconfig هنوز در اکثر توزیع‌های لینوکس کار می‌کند و در دسترس است.

این دستور می‌تواند برای اختصاص دادن و حذف آدرس‌ها، فعال یا غیرفعال کردن رابط‌ها، دستکاری مسیریابی و بسیاری کارهای دیگر استفاده شود.

```
ip [ OPTIONS ] OBJECT { COMMAND | help }
```

| مثال دستور ip                                     | توضیحات                                             |
| ------------------------------------------------------ | ------------------------------------------------------- |
| ip address show                                        | نمایش تمام آدرس‌های IP مرتبط با تمام دستگاه‌های شبکه |
| ip address show eth0                                   | مشاهده اطلاعات یک رابط خاص        |
| ip addr add 192.168.50.5/24 dev eth0                   | اختصاص آدرس IP به یک رابط خاص               |
| ip addr del 192.168.50.5/24 dev eth0                   | حذف یک آدرس IP                                    |
| ip link show                                           | نمایش رابط(های) شبکه                            |
| ip link set eth0 up                                    | فعال کردن رابط شبکه                                |
| ip link set eth0 down                                  | غیرفعال کردن رابط شبکه                               |
| ip route show                                          | نمایش اطلاعات جدول مسیریابی                          |
| ip route add 10.10.20.0/24 via 192.168.50.100 dev eth0 | افزودن مسیر استاتیک                                        |
| ip route del 10.10.20.0/24                             | حذف مسیر استاتیک                                     |

> تمام تنظیمات بالا پس از راه‌اندازی مجدد سیستم از بین خواهند رفت. از فایل‌های پیکربندی استفاده کنید.

### ping 

دستور `ping` یکی از پرکاربردترین ابزارها برای عیب‌یابی، تست و تشخیص مشکلات اتصال شبکه است.

Ping با ارسال یک یا چند پکت (Internet Control Message Protocol) Echo Request به یک IP مقصد مشخص در شبکه کار می‌کند و منتظر پاسخ می‌ماند. هنگامی که مقصد پکت را دریافت می‌کند، با یک ICMP echo reply پاسخ می‌دهد.

```
[root@server1 ~]# ping 8.8.8.8 -c3
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=128 time=45.0 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=128 time=47.0 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=128 time=40.2 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2043ms
rtt min/avg/max/mdev = 40.262/44.107/47.040/2.851 ms
```

با دستور `ping` می‌توانیم تعیین کنیم که آیا یک IP مقصد دور فعال است یا خیر. همچنین می‌توانید تاخیر رفت و برگشت (round-trip delay) در ارتباط با مقصد را پیدا کرده و بررسی کنید که آیا گم شدن پکت (packet loss) وجود دارد یا خیر.

| سوئیچ دستور ping | توضیحات                                        |
| ------------------- | -------------------------------------------------- |
|  **-n**             | فقط خروجی عددی؛ سعی در ترجمه نام میزبان نمی‌کند |
|  **-i interval**    | انتظار به میزان interval ثانیه بین ارسال هر پکت  |
|  **-I interface**   | تنظیم آدرس مبدأ به آدرس رابط مشخص شده  |
| **-a**              | پینگ صوتی                                       |

> برای محیط‌های IPv6 از دستور `ping6` استفاده کنید.

### /etc/nsswitch.conf

این فایل تعیین می‌کند که سیستم مواردی مانند نام میزبان، رمزهای عبور و شماره پروتکل‌ها را از کجا پیدا کند:

در اینجا تکه‌ای از یک فایل نمونه /etc/nsswitch.conf آمده است:

```
passwd:   files nis
group:    files nis

hosts:    files dns myhostname
```

در این مثال، اطلاعات کاربر (سرویس‌های passwd و group) ابتدا از "files" (مانند /etc/passwd یا /etc/group) می‌آیند و اگر هیچ ورودی در آنجا یافت نشد، از یک سرور NIS (که در جای دیگری پیکربندی شده) پرس‌وجو می‌شود. 

اطلاعات میزبان (Host) ابتدا از /etc/hosts (files)، سپس از یک سرور DNS (dns) می‌آید و اگر هیچ‌کدام کار نکرد، حداقل از "myhostname" به عنوان آخرین راهکار استفاده می‌شود تا ماشین محلی یک نام داشته باشد.

> سادگی در قانون "اگر کار نکرد، بعدی" نهفته است. وقتی چندین سرویس لیست شده‌اند، آن‌ها به ترتیب امتحان می‌شوند و یک سرویس یا موفق می‌شود یا شکست می‌خورد. اگر شکست خورد، سرویس بعدی امتحان می‌شود و به همین ترتیب.

تمام!

.

.

.

{% hint style="info" %}
### نام‌گذاری منسجم دستگاه‌های شبکه

 سیستم‌عامل Red Hat Enterprise Linux روش‌هایی برای نام‌گذاری منسجم و پیش‌بینی‌پذیر دستگاه‌های شبکه برای رابط‌های شبکه ارائه می‌دهد. این ویژگی‌ها نام رابط‌های شبکه را در سیستم تغییر می‌دهند تا مکان‌یابی و تمایز رابط‌ها آسان‌تر شود.

به طور سنتی، رابط‌های شبکه در لینوکس به صورت `eth[0123...]` شماره‌گذاری می‌شدند، اما این نام‌ها لزوماً با برچسب‌های واقعی روی بدنه دستگاه مطابقت نداشتند. پلتفرم‌های سرور مدرن با چندین آداپتور شبکه می‌توانند با نام‌گذاری غیرقطعی و غیرمنتظره این رابط‌ها مواجه شوند. این موضوع هم بر آداپتورهای شبکه تعبیه شده روی مادربرد (LOM) و هم بر آداپتورهای الحاقی (تک‌پورت و چند‌پورت) تأثیر می‌گذارد.

در Red Hat Enterprise Linux، ابزار **udev** از چندین طرح نام‌گذاری مختلف پشتیبانی می‌کند. حالت پیش‌فرض این است که نام‌های ثابتی بر اساس اطلاعات فریم‌ور، توپولوژی و مکان اختصاص داده شود. این طرح نام‌گذاری این مزیت را دارد که نام‌ها کاملاً خودکار و قابل پیش‌بینی هستند و حتی در صورت اضافه یا حذف شدن سخت‌افزار ثابت می‌مانند، و سخت‌افزار خراب را می‌توان بدون مشکل جایگزین کرد. عیب آن این است که گاهی اوقات خواندن آن‌ها نسبت به نام‌های eth0 یا wlan0 که به طور سنتی استفاده می‌شد، سخت‌تر است؛ به عنوان مثال: enp5s0.

* برای غیرفعال کردن آن (هرچند توصیه نمی‌شود)، هر دو مقدار `net.ifnames=0` و `biosdevname=0` را به عنوان پارامترهای هسته به متغیر `GRUB_CMDLINE_LINUX` اضافه کنید.
{% endhint %}



.

.

[https://developer.ibm.com/tutorials/l-lpic1-109-2/](https://developer.ibm.com/tutorials/l-lpic1-109-2/)

[https://www.computerhope.com/unix/uifconfi.htm](https://www.computerhope.com/unix/uifconfi.htm)

[https://www.tecmint.com/ifconfig-command-examples/](https://www.tecmint.com/ifconfig-command-examples/)

[https://www.geeksforgeeks.org/ifconfig-command-in-linux-with-examples/](https://www.geeksforgeeks.org/ifconfig-command-in-linux-with-examples/)

[https://www.unixmen.com/how-to-find-default-gateway-in-linux/](https://www.unixmen.com/how-to-find-default-gateway-in-linux/)

[https://jadi.gitbooks.io/lpic1/content/1092\_basic_network_configuration.html](https://jadi.gitbooks.io/lpic1/content/1092\_basic_network_configuration.html)

[https://www.tecmint.com/setup-local-dns-using-etc-hosts-file-in-linux/](https://www.tecmint.com/setup-local-dns-using-etc-hosts-file-in-linux/)

[https://opensource.com/business/16/8/introduction-linux-network-routing](https://opensource.com/business/16/8/introduction-linux-network-routing)

[https://www.computerhope.com/unix/route.htm](https://www.computerhope.com/unix/route.htm)

[https://www.thegeekstuff.com/2012/04/route-examples/](https://www.thegeekstuff.com/2012/04/route-examples/)

[https://linuxize.com/post/linux-ip-command/](https://linuxize.com/post/linux-ip-command/)

[https://www.geeksforgeeks.org/ip-command-in-linux-with-examples/](https://www.geeksforgeeks.org/ip-command-in-linux-with-examples/)

[https://www.tecmint.com/ip-command-examples/](https://www.tecmint.com/ip-command-examples/)

[https://linuxize.com/post/linux-ping-command/](https://linuxize.com/post/linux-ping-command/)

[https://linux.die.net/man/8/ping](https://linux.die.net/man/8/ping)

[https://developers.redhat.com/blog/2018/11/26/etc-nsswitch-conf-non-complexity/](https://developers.redhat.com/blog/2018/11/26/etc-nsswitch-conf-non-complexity/)

.

[https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/ch-consistent_network_device_naming](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/ch-consistent_network_device_naming)

.
