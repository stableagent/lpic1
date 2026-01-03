# 109.3 عیب‌یابی پایه شبکه

**وزن:** ۴

**توضیحات:** داوطلبان باید قادر به عیب‌یابی مشکلات شبکه در هاست‌های کلاینت باشند.

**حوزه‌های کلیدی دانش:**

* پیکربندی دستی و خودکار رابط‌های شبکه و جداول مسیریابی، شامل افزودن، شروع، توقف، راه‌اندازی مجدد، حذف یا پیکربندی مجدد رابط‌های شبکه
* تغییر، مشاهده یا پیکربندی جدول مسیریابی و اصلاح دستی درگاه پیش‌فرض (default route) تنظیم شده به صورت نادرست
* عیب‌یابی (Debug) مشکلات مربوط به پیکربندی شبکه

**اصطلاحات و ابزارهای کاربردی:**

* ifconfig
* ip
* ifup
* ifdown
* route
* host
* hostname
* dig
* netstat
* ping
* ping6
* traceroute
* traceroute6
* tracepath
* tracepath6
* netcat

تا به اینجا با مبانی پروتکل‌های اینترنت آشنا شده‌ایم و با برخی از فایل‌های پیکربندی و ابزارهای شبکه آشنایی پیدا کرده‌ایم. واقعیت این است که گاهی اوقات چیزها آنطور که انتظار داریم کار نمی‌کنند و نیاز به عیب‌یابی دارند. در این بخش سعی می‌کنیم مراحلی را برای حل مشکل نشان دهیم و علاوه بر آن، چند دستور جدید معرفی خواهد شد.

### ifconfig و ip (مشکلات رابط یا آدرس IP)

اولین دستوری که یاد گرفتیم `ifconfig` است. گاهی اوقات ممکن است یک رابط غیرفعال وجود داشته باشد که در نتایج ظاهر نشود:

```
root@ubuntu16-1:~# ifconfig
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:6 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:318 (318.0 B)  TX bytes:318 (318.0 B)
```

در عوض از گزینه `-a` با دستور `ifconfig` یا `ip` استفاده کنید:

```
root@ubuntu16-1:~# ifconfig -a
ens33     Link encap:Ethernet  HWaddr 00:0c:29:e2:1b:3e  
          BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:44 errors:0 dropped:0 overruns:0 frame:0
          TX packets:113 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:5169 (5.1 KB)  TX bytes:12521 (12.5 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:6 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:318 (318.0 B)  TX bytes:318 (318.0 B)

root@ubuntu16-1:~# ip a s
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast state DOWN group default qlen 1000
    link/ether 00:0c:29:e2:1b:3e brd ff:ff:ff:ff:ff:ff

```

رابط را با `ifup ens33` یا `ifconfig ens33 up` بالا بیاورید و سپس آدرس IP خود را بررسی کنید.  

```
root@ubuntu16-1:~# ifup ens33
```

> می‌توانید آدرس IP خود را از طریق رابط گرافیکی یا فایل‌های پیکربندی بررسی کنید. اگر از اختصاص خودکار IP استفاده می‌کنید، از `dhclient -r` و `dhclient` برای رها کردن و تمدید آدرس IP خود استفاده کنید.

### ping (تشخیص مشکل)

Ping بهترین دوست ما هنگام عیب‌یابی مشکلات شبکه است.

بررسی کنید که آیا می‌توانید کامپیوتر دیگری را در همان شبکه پینگ کنید؟ 

```
root@ubuntu16-1:~# ping 172.16.43.127 -c 2
PING 172.16.43.127 (172.16.43.127) 56(84) bytes of data.
64 bytes from 172.16.43.127: icmp_seq=1 ttl=64 time=0.748 ms
64 bytes from 172.16.43.127: icmp_seq=2 ttl=64 time=0.755 ms

--- 172.16.43.127 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.748/0.751/0.755/0.027 ms
```

از یک دستور ساده پینگ می‌توانیم تعیین کنیم که آیا هدف فعال و در حال اجرا است یا خیر. همچنین ممکن است یک فایروال در شبکه شما وجود داشته باشد که پکت‌های ICMP را فیلتر می‌کند؛ ابتدا فایروال هاست و سپس فایروال سخت‌افزاری (اگر وجود دارد) را بررسی کنید. 

```
root@ubuntu16-1:~# ping 172.16.43.126 -c 2
PING 172.16.43.126 (172.16.43.126) 56(84) bytes of data.
From 172.16.43.135 icmp_seq=1 Destination Host Unreachable
From 172.16.43.135 icmp_seq=2 Destination Host Unreachable

--- 172.16.43.126 ping statistics ---
2 packets transmitted, 0 received, +2 errors, 100% packet loss, time 1007ms
pipe 2
```

گاهی اوقات ممکن است یک آدرس IP اشتباه را پینگ کنید یا سرور ممکن است دو رابط یا دو آدرس IP داشته باشد.

### ping6

دستور پینگ معمولی فقط با آدرس‌های IPv4 کار می‌کند. از دستور `ping6` برای ارسال پکت‌های ICMPv6 ECHO_REQUEST به هاست‌های شبکه استفاده کنید.

### route (مشکلات درگاه و مسیریابی)

اگر نمی‌توانید به هیچ شبکه‌ای به جز کامپیوترهایی که در همان زیرشبکه شما هستند دسترسی پیدا کنید، باید به درگاه (gateway) خود شک کنید.

```
root@ubuntu16-1:~# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         172.16.43.2     0.0.0.0         UG    0      0        0 ens33
link-local      *               255.255.0.0     U     1000   0        0 ens33
172.16.43.0     *               255.255.255.0   U     0      0        0 ens33
```

همچنین می‌توانیم از دستور `netstat -rn` برای مشاهده درگاه فعلی استفاده کنیم:

```
root@ubuntu16-1:~# netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         172.16.43.2     0.0.0.0         UG        0 0          0 ens33
169.254.0.0     0.0.0.0         255.255.0.0     U         0 0          0 ens33
172.16.43.0     0.0.0.0         255.255.255.0   U         0 0          0 ens33
```

اگر درگاه پیش‌فرضی وجود نداشت، باید از `route add default gw x.x.x.x` برای افزودن آن استفاده کنید. سپس درگاه را بررسی کنید و مطمئن شوید که پکت‌ها از رابط صحیح خارج می‌شوند:

```
root@ubuntu16-1:~# ping -c 3 172.16.43.2
PING 172.16.43.2 (172.16.43.2) 56(84) bytes of data.
64 bytes from 172.16.43.2: icmp_seq=1 ttl=128 time=0.434 ms
64 bytes from 172.16.43.2: icmp_seq=2 ttl=128 time=0.379 ms
64 bytes from 172.16.43.2: icmp_seq=3 ttl=128 time=0.166 ms

--- 172.16.43.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2025ms
rtt min/avg/max/mdev = 0.166/0.326/0.434/0.116 ms
```

همه چیز درست به نظر می‌رسد، اما نمی‌توانید به یک آدرس IP خاص در ساختمان دیگری دسترسی پیدا کنید. ممکنه مشکل مسیریابی در روترهای فیزیکی وجود داشته باشد! چطور آن را بررسی کنیم؟

### traceroute

دستور `traceroute` مسیری را که یک پکت اطلاعاتی از مبدأ تا مقصد طی می‌کند، نقشه‌برداری می‌کند. این ابزار از پیام‌های **ICMP** استفاده می‌کند، اما برخلاف پینگ، هر روتر را در مسیر شناسایی می‌کند. `traceroute` هنگام عیب‌یابی مشکلات شبکه مفید است زیرا می‌تواند به شما در مکان‌یابی مشکلات در اتصال شبکه کمک کند (ممکن است نیاز به نصب آن داشته باشید: `apt install traceroute`):

```
root@ubuntu16-1:~# traceroute google.com
traceroute to google.com (172.217.18.142), 30 hops max, 60 byte packets
 1  172.16.43.2 (172.16.43.2)  0.272 ms  0.329 ms  0.172 ms
 2  172.16.130.1 (172.16.130.1)  0.969 ms  1.183 ms  1.128 ms
 3  192.168.1.66 (192.168.1.66)  0.448 ms  0.574 ms  0.655 ms
 4  192.168.66.41 (192.168.66.41)  5.123 ms  4.924 ms  7.027 ms
 5  * * *
 6  * * *
 7  10.10.53.93 (10.10.53.93)  14.334 ms  14.304 ms  14.256 ms
 8  10.201.147.214 (10.201.147.214)  19.629 ms  19.580 ms  21.650 ms
 9  10.21.21.22 (10.21.21.22)  19.445 ms  16.203 ms  16.131 ms
10  10.21.21.22 (10.21.21.22)  13.911 ms  13.793 ms  13.723 ms
11  213.202.4.172 (213.202.4.172)  48.527 ms  48.476 ms  48.416 ms
12  134.0.220.62 (134.0.220.62)  48.361 ms  48.285 ms  47.198 ms
13  108.170.246.113 (108.170.246.113)  49.444 ms  49.409 ms 108.170.240.49 (108.170.240.49)  49.677 ms
14  172.253.51.137 (172.253.51.137)  52.925 ms  46.305 ms  45.917 ms
15  arn02s05-in-f142.1e100.net (172.217.18.142)  45.783 ms  45.708 ms  4
```

> از گزینه `-i` برای مشخص کردن رابطی که traceroute باید پکت‌ها را از طریق آن ارسال کند، استفاده کنید.

### traceroute6

traceroute با گزینه `-6` از IPv6 پشتیبانی می‌کند، به جای آن می‌توانیم از دستور `traceroute6` استفاده کنیم.

{% hint style="info" %}
**MTU چیست؟** MTU مخفف Maximum Transmission Unit، اندازه بزرگترین واحد داده پروتکل است که می‌تواند در یک تراکنش لایه شبکه منتقل شود.
{% endhint %}

### tracepath

Tracepath مسیری را تا یک آدرس شبکه مشخص دنبال کرده و "زمان حیات" (TTL) و واحدهای حداکثر انتقال (MTU) را در طول مسیر گزارش می‌دهد. این دستور می‌تواند توسط هر کاربری که به خط فرمان دسترسی دارد اجرا شود.

```
root@ubuntu16-1:~# tracepath google.com
 1?: [LOCALHOST]                                         pmtu 1500
 1:  172.16.43.2                                           0.148ms 
 1:  172.16.43.2                                           0.139ms 
 2:  172.16.130.1                                          4.944ms asymm  1 
 3:  192.168.1.66                                          1.224ms asymm  1 
 4:  192.168.66.41                                         8.198ms asymm  1 
 5:  192.168.198.169                                       5.567ms asymm  1 
 6:  192.168.0.254                                         5.923ms asymm  1 
 7:  10.10.53.93                                          10.018ms asymm  1 
 8:  10.201.147.214                                       11.534ms asymm  1 
 9:  10.21.21.22                                          15.286ms asymm  1 
10:  10.21.21.22                                          11.588ms asymm  1 
11:  213.202.4.172                                        43.501ms asymm  1 
12:  no reply
...
30:  no reply
     Too many hops: pmtu 1500
     Resume: pmtu 1500 

```

> Traceroute اساساً مشابه Tracepath است، با این تفاوت که به طور پیش‌فرض، فقط مقدار TTL را می‌دهد.

### tracepath6

 دستور `tracepath6` جایگزین خوبی برای `traceroute6` است.

### dig

 **Dig** مخفف (Domain Information Groper) یک ابزار خط فرمان مدیریت شبکه برای پرس‌وجو از سرورهای نام (DNS) است. این ابزار برای تأیید و عیب‌یابی مشکلات DNS و همچنین انجام جستجوهای DNS و نمایش پاسخ‌هایی که از سرور نام بازگردانده شده‌اند، مفید است.

> به طور پیش‌فرض، `dig` پرس‌وجوی DNS را به سرورهای نام لیست شده در بخش resolver (فایل `/etc/resolv.conf`) ارسال می‌کند، مگر اینکه از آن خواسته شود از یک سرور نام خاص پرس‌وجو کند.

```
root@ubuntu16-1:~# dig aol.com

; <<>> DiG 9.10.3-P4-Ubuntu <<>> aol.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 51870
;; flags: qr rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;aol.com.            IN    A

;; ANSWER SECTION:
aol.com.        587    IN    A    188.125.72.165
aol.com.        587    IN    A    66.218.87.12
aol.com.        587    IN    A    67.195.231.10
aol.com.        587    IN    A    124.108.115.87
aol.com.        587    IN    A    106.10.218.150

;; Query time: 46 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Mon Feb 24 16:12:46 +0330 2020
;; MSG SIZE  rcvd: 116
```

خروجی دستور `dig` دارای چندین بخش است؛ برای داشتن فقط بخش پاسخ (Answer)، از سوئیچ `+short` استفاده کنید:

```
root@ubuntu16-1:~# dig aol.com +short
124.108.115.87
66.218.87.12
188.125.72.165
106.10.218.150
67.195.231.10
```

برای پرس‌وجو از یک سرور نام خاص از `@NameServerIP` استفاده کنید:

```
root@ubuntu16-1:~# dig aol.com  @64.6.65.6 +short
106.10.218.150
188.125.72.165
66.218.87.12
124.108.115.87
67.195.231.10
```

### netstat

  **netstat** (آمار شبکه) یک ابزار خط فرمان است که اتصالات شبکه (ورودی و خروجی)، جداول مسیریابی، تعداد رابط‌های شبکه و حتی آمار پروتکل‌های شبکه را نمایش می‌دهد.

> به طور پیش‌فرض، `netstat` لیستی از سوکت‌های باز را نمایش می‌دهد. (یک سوکت، یک نقطه انتهایی از یک لینک ارتباطی دوطرفه بین دو برنامه در حال اجرا در شبکه است.) به عنوان مثال X11:

```
root@ubuntu16-1:~# netstat  | grep X11 | head -n3
unix  3      [ ]         STREAM     CONNECTED     34942    @/tmp/.X11-unix/X0
unix  3      [ ]         STREAM     CONNECTED     33525    @/tmp/.X11-unix/X0
unix  3      [ ]         STREAM     CONNECTED     32753    @/tmp/.X11-unix/X0

```

بنابراین ما معمولاً از ترکیبی از سوئیچ‌ها با `netstat` استفاده می‌کنیم:

| مثال دستور netstat | کاربرد                                                      |
| ------------------------ | ---------------------------------------------------------- |
| netstat -a               | لیست کردن تمام پورت‌های در حال گوش دادن (LISTENING) برای اتصالات TCP و UDP |
| netstat -na              | تمام پورت‌های LISTENING، اما آدرس‌ها را به صورت عددی نشان می‌دهد        |
| netstat -at              | لیست کردن اتصالات پورت‌های TCP                              |
| netstat -au              | لیست کردن اتصالات پورت‌های UDP                              |
| netstat -l               | لیست کردن تمام اتصالات در حال گوش دادن (TCP و UDP)                |
| netstat -s               | نمایش آمار بر اساس پروتکل (TCP، UDP و غیره)               |
| netstat -tp              | نمایش نام سرویس به همراه PID                           |
| netstat -rn              | نمایش مسیریابی IP هسته                               |

> از `netstat` در ترکیب با `grep` برای دریافت نتایج بهتر استفاده کنید.

### netcat

ابزار **nc** (یا netcat) برای تقریباً هر کاری که مربوط به TCP یا UDP باشد استفاده می‌شود. این ابزار می‌تواند اتصالات TCP باز کند، پکت‌های UDP ارسال کند، روی پورت‌های دلخواه TCP و UDP گوش دهد، اسکن پورت انجام دهد و با هر دو پروتکل IPv4 و IPv6 کار کند. برخلاف telnet، ابزار `nc` برای اسکریپت‌نویسی بسیار مناسب است و پیام‌های خطا را به جای خروجی استاندارد، به خطای استاندارد (standard error) می‌فرستد.

```
root@ubuntu16-1:~# netcat -l 8888
```

پارامتر `-l` به این معنی است که netcat در حالت گوش دادن (سرور) است و `8888` پورتی است که به آن گوش می‌دهد؛ netcat یک سرور سوکت ایجاد کرده و منتظر اتصالات روی پورت ۸۸۸۸ می‌ماند. ترمینال منتظر می‌ماند تا یک کلاینت به سرور باز شده با netcat متصل شود. ما می‌توانیم تأیید کنیم که یک سرویس هاست روی پورت ۸۸۸۸ گوش می‌دهد. باید یک ترمینال جدید باز کرده و دستور زیر را اجرا کنیم:

```
root@ubuntu16-1:~# netstat -na | grep 8888
tcp        0      0 0.0.0.0:8888            0.0.0.0:*               LISTEN 
```





.

.

.

[https://www.cyberciti.biz/faq/howto-test-ipv6-network-with-ping6-command/](https://www.cyberciti.biz/faq/howto-test-ipv6-network-with-ping6-command/)

[https://www.lifewire.com/traceroute-linux-command-4092586](https://www.lifewire.com/traceroute-linux-command-4092586)

[https://geek-university.com/linux/traceroute-command/](https://geek-university.com/linux/traceroute-command/)

[https://www.techwalla.com/articles/differences-between-traceroute-tracepath](https://www.techwalla.com/articles/differences-between-traceroute-tracepath)

[http://netstat.net/](http://netstat.net)

[http://journals.ecs.soton.ac.uk/java/tutorial/networking/sockets/definition.html](http://journals.ecs.soton.ac.uk/java/tutorial/networking/sockets/definition.html)

[https://www.geeksforgeeks.org/netstat-command-linux/](https://www.geeksforgeeks.org/netstat-command-linux/)

[https://www.tecmint.com/20-netstat-commands-for-linux-network-management/](https://www.tecmint.com/20-netstat-commands-for-linux-network-management/)

[https://linux.die.net/man/1/nc](https://linux.die.net/man/1/nc)

[https://www.mvps.net/docs/what-is-netcat-and-how-to-use-it/](https://www.mvps.net/docs/what-is-netcat-and-how-to-use-it/)

.
