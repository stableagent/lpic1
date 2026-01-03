# 109.1 مبانی پروتکل‌های اینترنت

## **109.1 مبانی پروتکل‌های اینترنت**

**وزن:** ۴

**توضیحات:** داوطلبان باید درک درستی از مبانی شبکه TCP/IP داشته باشند.

**حوزه‌های کلیدی دانش:**

* نشان دادن درک درستی از ماسک‌های شبکه (network masks) و نمادگذاری CIDR
* دانش در مورد تفاوت‌های بین آدرس‌های IP عمومی (public) و خصوصی (private)
* دانش در مورد پورت‌ها و سرویس‌های رایج TCP و UDP (20, 21, 22, 23, 25, 53, 80, 110, 123, 139, 143, 161, 162, 389, 443, 465, 514, 636, 993, 995)
* دانش در مورد تفاوت‌ها و ویژگی‌های اصلی UDP، TCP و ICMP
* دانش در مورد تفاوت‌های اصلی بین IPv4 و IPv6
* دانش در مورد ویژگی‌های پایه IPv6

**اصطلاحات و ابزارهای کاربردی:**

* /etc/services
* IPv4, IPv6
* Subnetting
* TCP, UDP, ICMP

#### IP 

پروتکل IP (پروتکل اینترنت) پروتکل بنیادی برای ارتباطات در اینترنت است. این پروتکل نحوه بسته‌بندی اطلاعات، آدرس‌دهی، انتقال، مسیریابی و دریافت توسط دستگاه‌های تحت شبکه را مشخص می‌کند.

#### آدرس IP

 یک **آدرس IP** شماره‌ای است که یک کامپیوتر یا دستگاه دیگر را در اینترنت شناسایی می‌کند. این آدرس مشابه آدرس پستی است که مشخص می‌کند نامه از کجا می‌آید و به کجا باید تحویل داده شود. آدرس‌های IP به طور منحصر‌به‌فرد مبدأ و مقصد داده‌های ارسال شده با پروتکل اینترنت را شناسایی می‌کنند.

### IPv4

آدرس‌های IPv4 دارای طول ۳۲ بیت (چهار بایت) هستند. یک نمونه از آدرس IPv4 مقدار 216.58.216.164 است که آدرس صفحه اصلی Google.com می‌باشد.

{% hint style="info" %}
 **تجزیه و تحلیل آدرس IPv4**

آدرس از ۳۲ بیت باینری (صفر و یک) تشکیل شده است.

![](.gitbook/assets/fundamentalip-ipv4oct.jpg)

۳۲ بیت باینری به چهار اکتت (octet) تقسیم می‌شوند (هر اکتت = ۸ بیت). هر اکتت به عدد دهدهی (decimal) تبدیل شده و با یک نقطه (dot) جدا می‌شود. به همین دلیل، گفته می‌شود که آدرس IP در قالب دهدهی نقطه‌دار (dotted decimal) بیان شده است.

**نحوه تبدیل اکتت‌های باینری به دهدهی:** راست‌ترین بیت یا کم‌ارزش‌ترین بیتِ یک اکتت، مقدار 2^0 را دارد. بیت سمت چپ آن مقدار 2^1 را دارد و این روند تا چپ‌ترین بیت یا باارزش‌ترین بیت که مقدار 2^7 را دارد ادامه می‌یابد. بنابراین اگر تمام بیت‌های باینری یک باشند، معادل دهدهی آن‌ها ۲۵۵ خواهد بود، همانطور که در اینجا نشان داده شده است:

> ```
>     1  1  1  1 1 1 1 1
>   128 64 32 16 8 4 2 1 (128+64+32+16+8+4+2+1=255)
> ```

در اینجا یک نمونه تبدیل اکتت زمانی که تمام بیت‌ها ۱ نیستند آورده شده است:

> ```
>   0  1 0 0 0 0 0 1
>   0 64 0 0 0 0 0 1 (0+64+0+0+0+0+0+1=65)
> ```

و این نمونه یک آدرس IP را نشان می‌دهد که هم به صورت باینری و هم دهدهی نمایش داده شده است:

> ```
>         10.       1.      23.      19 (decimal)
>   00001010.00000001.00010111.00010011 (binary)
> ```
{% endhint %}

حداکثر مقدار یک عدد ۳۲ بیتی، 2^32 یا ۴،۲۹۴،۹۶۷،۲۹۶ است. بنابراین حداکثر تعداد آدرس‌های IPv4 که فضای آدرس (address space) نامیده می‌شود، حدود ۴.۳ میلیارد است. در دهه ۱۹۸۰، این مقدار برای آدرس‌دهی به هر دستگاه تحت شبکه کافی بود، اما دانشمندان می‌دانستند که این فضا به سرعت تمام خواهد شد. 

> فناوری‌هایی مانند NAT با اجازه دادن به چندین دستگاه برای استفاده از یک آدرس IP واحد، این مشکل را به تاخیر انداخته‌اند، اما برای خدمت‌رسانی به اینترنت مدرن، فضای آدرس بزرگتری مورد نیاز است.

### IPv6

مزیت اصلی IPv6 این است که از ۱۲۸ بیت داده برای ذخیره آدرس استفاده می‌کند که اجازه 2^128 آدرس منحصر‌به‌فرد را می‌دهد. اندازه فضای آدرس IPv6 بسیار بسیار بزرگتر از IPv4 است.

![](.gitbook/assets/fundamentalip-ipv6oct.jpg)

#### کلاس‌های آدرس IP

در آدرس‌دهی IPv4، پنج کلاس برای محدوده‌های آدرس IP در دسترس است: کلاس A، B، C، D و E، در حالی که فقط کلاس‌های A، B و C به طور معمول استفاده می‌شوند.

#### ماسک زیرشبکه (subnetmask)

در یک پروتکل اینترنت یا شبکه IP، هر هاست متصل شده باید هم یک آدرس IP و هم یک ماسک زیرشبکه (subnet mask) داشته باشد تا به درستی کار کند. هر دستگاهی که از پروتکل IP استفاده می‌کند می‌تواند با آدرس IP ۱۲۷.۰.۰.۱ و ماسک زیرشبکه ۲۵۵.۰.۰.۰ به خودش اشاره کند، اما برای برقراری ارتباط با سایر دستگاه‌ها در شبکه، هر دستگاه باید یک آدرس IP (عمومی یا خصوصی) و یک ماسک زیرشبکه داشته باشد.

 Netmask یک مقدار باینری ۳۲ بیتی است که آن کلاس IP را محدود می‌کند تا تعداد مشخصی شبکه و تعداد مشخصی هاست در هر شبکه داشته باشد.

![](.gitbook/assets/fundamentalip-ipv4format.jpg)

* **Netid**: بخشی از آدرس IP که شبکه را شناسایی می‌کند. 
* **Hostid**: بخشی از آدرس IP که یک هاست را در یک شبکه شناسایی می‌کند.

طول netid و hostid بسته به کلاس آدرس، متغیر است.

 هر کلاس IP مجهز به ماسک زیرشبکه (netmask) پیش‌فرض خود است و اجازه محدوده‌ای از آدرس‌های IP معتبر را می‌دهد که در جدول زیر نشان داده شده است:

| کلاس       | محدوده آدرس                | subnetmask    | پشتیبانی می‌کند از                                                       |
| ----------- | ---------------------------- | ------------- | -------------------------------------------------------------- |
| **Class A** | 1.0.0.1 to 126.255.255.254   | 255.0.0.0     | ۱۶ میلیون هاست در هر یک از ۱۲۷ شبکه |
| **Class B** | 128.1.0.1 to 191.255.255.254 | 255.255.0.0   | ۶۵,۰۰۰ هاست در هر یک از ۱۶,۰۰۰ شبکه |
| **Class C** | 192.0.1.1 to 223.255.254.254 | 255.255.255.0 | ۲۵۴ هاست در هر یک از ۲ میلیون شبکه |
| **Class D** | 224.0.0.0 to 239.255.255.255 | N/A           | رزرو شده برای گروه‌های چندپخشی (multicast) |
| **Class E** | 240.0.0.0 to 254.255.255.254 | N/A           | رزرو شده برای استفاده در آینده یا اهداف تحقیق و توسعه |

>  محدوده‌های `127.x.x.x` برای loopback یا localhost رزرو شده‌اند؛ برای مثال، `127.0.0.1` آدرس loopback است. محدوده `255.255.255.255` برای پخش همگانی (broadcast) به تمام هاست‌ها در شبکه محلی استفاده می‌شود.

{% hint style="success" %}
**IPهای خصوصی (Private IPs)**

سازمان IANA چندین محدوده آدرس را برای استفاده در شبکه‌های خصوصی اختصاص داده است.

محدوده‌های آدرس برای استفاده در شبکه‌های خصوصی عبارتند از:

* کلاس A: از `10.0.0.0` تا `10.255.255.255`
* کلاس B: از `172.16.0.0` تا `172.31.255.255`
* کلاس C: از `192.168.0.0` تا `192.168.255.255`

هر شبکه خصوصی که نیاز به استفاده داخلی از آدرس‌های IP دارد، می‌تواند از هر آدرسی در این محدوده‌ها بدون هیچ هماهنگی استفاده کند. آدرس‌های موجود در این فضای آدرس خصوصی، فقط در یک شبکه خصوصی مشخص منحصر‌به‌فرد هستند.
{% endhint %}

آدرس‌دهی کلاس‌بندی شده (Classful) هیچ انعطاف‌پذیری برای داشتن تعداد هاست کمتر در هر شبکه یا شبکه‌های بیشتر در هر کلاس IP فراهم نمی‌کند؛ اینجا جایی است که زیرشبکه‌بندی (subnetting) وارد عمل می‌شود.

### زیرشبکه‌بندی (subnetting)

فرآیند تقسیم یک کلاس IP به بلوک‌های کوچکتر یا گروه‌هایی از IPها، به عنوان زیرشبکه‌بندی (subnetting) شناخته می‌شود. 

زیرشبکه‌بندی می‌تواند امنیت را بهبود بخشد و به تعادل ترافیک کلی شبکه کمک کند. 

{% hint style="info" %}
**CIDR**

**CIDR** مخفف **Classless Inter Domain Routing** است و بر اساس مفهوم زیرشبکه‌بندی بنا شده است. CIDR و زیرشبکه‌بندی در واقع یک چیز هستند. اصطلاح زیرشبکه‌بندی (Subnetting) معمولاً زمانی استفاده می‌شود که در سطح سازمانی از آن استفاده کنید. CIDR معمولاً در سطح ISP یا بالاتر استفاده می‌شود.
{% endhint %}

**زیرشبکه‌بندی چگونه کار می‌کند؟** زیرشبکه‌بندی یک عملیات بیتی (bitwise) روی یک شبکه از آدرس‌های IP است که با استفاده از netmask انجام می‌شود.

 این کار انعطاف‌پذیری لازم برای قرض گرفتن بیت‌ها از بخش Host آدرس IP و استفاده از آن‌ها به عنوان شبکه در شبکه (که زیرشبکه یا Subnet نامیده می‌شود) را فراهم می‌کند. با استفاده از زیرشبکه‌بندی، یک آدرس IP کلاس A می‌تواند برای داشتن چندین زیرشبکه کوچکتر استفاده شود که قابلیت‌های مدیریت شبکه بهتری را فراهم می‌کند.

*   **Class A Subnets**

    در کلاس A، فقط اکتت اول به عنوان شناسه شبکه استفاده می‌شود و سه اکتت باقی‌مانده برای اختصاص دادن به هاست‌ها استفاده می‌شوند. برای ساختن زیرشبکه‌های بیشتر در کلاس A، بیت‌هایی از بخش Host قرض گرفته می‌شوند و ماسک زیرشبکه متناسب با آن تغییر می‌کند.

![](.gitbook/assets/fundamentalip-classasub.jpg)

*   **Class B Subnets**

    به طور پیش‌فرض در آدرس‌دهی کلاس‌بندی شده، ۱۴ بیت برای شبکه استفاده می‌شود که ۱۶۳۸۴ شبکه و ۶۵۵۳۴ هاست را فراهم می‌کند. آدرس‌های کلاس B را نیز می‌توان به همان روش کلاس A با قرض گرفتن بیت‌ها از بخش Host زیرشبکه‌بندی کرد. در زیر تمام ترکیبات ممکن برای زیرشبکه‌بندی کلاس B آورده شده است.

![](.gitbook/assets/fundamentalip-classbsub.jpg)

*   **Class C Subnets**

    آدرس‌های کلاس C معمولاً به شبکه‌های بسیار کوچک اختصاص می‌یابند زیرا فقط ۲۵۴ هاست در هر شبکه می‌توانند داشته باشند. در زیر لیستی از تمام ترکیبات ممکن برای زیرشبکه‌بندی کلاس C آورده شده است.

![](.gitbook/assets/fundamentalip-classcsub.jpg)

## پروتکل‌های ارتباطی

یک پروتکل شبکه، قوانین و رویه‌هایی را تعریف می‌کند که بر اساس آن‌ها ارتباط داده‌ای بین دستگاه‌ها در یک شبکه رخ می‌دهد. بدون قوانین یا رویه‌های از پیش تعریف شده، پیام‌هایی که در یک شبکه در حال عبور هستند فاقد هرگونه فرمت خاصی خواهند بود و ممکن است برای دستگاه دریافت‌کننده معنایی نداشته باشند.

{% hint style="info" %}
**مدل OSI:** مدل OSI (اتصال متقابل سیستم‌های باز) توسط سازمان بین‌المللی استانداردسازی (ISO) ایجاد شد. این مدل به عنوان یک مدل مرجع برای توصیف عملکردهای یک سیستم ارتباطی طراحی شده است. مدل OSI دارای هفت لایه است.

![](.gitbook/assets/fundamentalip-osi.jpg)

**مدل TCP/IP:** مدل TCP/IP در دهه ۱۹۷۰ توسط آژانس پروژه‌های تحقیقاتی پیشرفته دفاعی (DARPA) به عنوان یک مدل شبکه عمومی، مستقل از فروشنده و باز ایجاد شد. درست مانند مدل OSI، این مدل دستورالعمل‌های کلی را برای طراحی و پیاده‌سازی پروتکل‌های کامپیوتری توصیف می‌کند. این مدل شامل چهار لایه است.

![](.gitbook/assets/fundamentalip-tcp.jpg)

مقایسه بین مدل TCP/IP و مدل OSI:

![](.gitbook/assets/fundamentalip-ositcp.jpg)
{% endhint %}

بیایید در مورد برخی از پروتکل‌های محبوب (TCP/UDP/ICMP) و شماره پورت‌های مربوط به آن‌ها بحث کنیم:

### TCP

پروتکل کنترل انتقال (TCP) یک پروتکل **اتصال‌گرا (connection-oriented)** است که در لایه انتقال (Transport Layer) هر دو مدل مرجع (OSI) و پشته پروتکل (TCP/IP) عمل می‌کند. این پروتکل برای فراهم کردن انتقال قابل اعتماد داده‌ها در شبکه طراحی شده است. TCP با راه‌اندازی یک دست‌دادن ۳ مرحله‌ای (3-way handshake) قبل از تبادل داده بین فرستنده و گیرنده، اطمینان از انتقال را فراهم می‌کند.

![](.gitbook/assets/fundamentalip-howtcp.gif)

### UDP

پروتکل دیتاگرام کاربر (UDP)، یک پروتکل **بدون اتصال (connectionless)** است. این پروتکل نیز در لایه انتقال هر دو مدل مرجع (OSI) و پشته پروتکل (TCP/IP) عمل می‌کند. با این حال، برخلاف TCP، پروتکل UDP هیچ تضمینی برای تحویل داده‌ها در شبکه ارائه نمی‌دهد. همه پروتکل‌های لایه اپلیکیشن از TCP استفاده نمی‌کنند؛ پروتکل‌های زیادی در لایه ۷ وجود دارند که از پروتکل UDP استفاده می‌کنند.

![](.gitbook/assets/fundamentalip-howudp.gif)

#### مقایسه TCP و UDP

| TCP                                                              | UDP                                                                       |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------- |
| قابل اعتماد                                                         | بسیار سریع در تحویل داده‌ها                                             |
| استفاده از تاییدیه (Acknowledgment) برای تایید دریافت داده                  | سربار (overhead) بسیار کم در شبکه                                          |
| ارسال مجدد داده در صورت گم شدن پکت‌ها | نیازی به پکت‌های تاییدیه ندارد                               |
| داده‌ها را به ترتیب تحویل داده و بازسازی (reassembly) را مدیریت می‌کند     | در صورت گم شدن پکت‌ها، داده‌های گم شده را مجدداً ارسال نمی‌کند |
| کاربردها: HTTP, FTP, SMTP, Telnet.                           | کاربردها: DHCP, DNS, SNMP, TFTP, VoIP, IPTV.                          |

### ICMP

در یک شبکه، چه شبکه محلی (LAN) باشد و چه شبکه گسترده (WAN)، دستگاه‌های هاست برای تبادل داده و اطلاعات با یکدیگر ارتباط برقرار می‌کنند و گاهی اوقات ممکن است خطایی رخ دهد.

 پروتکل پیام کنترل اینترنت (ICMP) معمولاً برای ارائه گزارش خطا در شبکه استفاده می‌شود. انواع مختلفی از پیام‌های ICMP وجود دارند که عملکردهای متفاوتی را ارائه داده و در صورت بروز خطا، بازخوردی درباره مشکل موجود می‌دهند. یک نمونه خوب از استفاده از پروتکل ICMP، دستور `ping` است:

```
root@ubuntu16-1:~# ping google.com -c 3
PING google.com (172.217.18.142) 56(84) bytes of data.
64 bytes from arn02s05-in-f142.1e100.net (172.217.18.142): icmp_seq=1 ttl=128 time=141 ms
64 bytes from arn02s05-in-f142.1e100.net (172.217.18.142): icmp_seq=2 ttl=128 time=95.8 ms
64 bytes from arn02s05-in-f142.1e100.net (172.217.18.142): icmp_seq=3 ttl=128 time=95.7 ms

--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 6251ms
rtt min/avg/max/mdev = 95.746/110.869/141.042/21.337 ms
```

#### پورت‌ها (Ports)

همانطور که گفتیم، در یک شبکه TCP/IP هر دستگاه باید یک آدرس IP داشته باشد. آدرس IP دستگاه (مثلا کامپیوتر) را شناسایی می‌کند. با این حال، آدرس IP به تنهایی برای اجرای اپلیکیشن‌های تحت شبکه کافی نیست، زیرا یک کامپیوتر می‌تواند چندین اپلیکیشن و/یا سرویس را به طور همزمان اجرا کند.

درست همانطور که آدرس IP کامپیوتر را شناسایی می‌کند، پورت شبکه نیز اپلیکیشن یا سرویس در حال اجرا روی کامپیوتر را شناسایی می‌کند. استفاده از پورت‌ها به کامپیوترها/دستگاه‌ها اجازه می‌دهد تا چندین سرویس/اپلیکیشن را اجرا کنند.

نمودار زیر اتصال کامپیوتر به کامپیوتر را نشان داده و آدرس‌های IP و پورت‌ها را شناسایی می‌کند:

![](.gitbook/assets/fundamentalip-tcpipsock.jpg)

پورت‌های پیش‌فرض برخی پروتکل‌ها به شرح زیر است. این‌ها بسیار مهم هستند و اکثر مدیران شبکه آن‌ها را می‌دانند:

| پورت    | کاربرد                       |
| ------- | --------------------------- |
| 20,21   | FTP (یکی برای داده، یکی برای کنترل) |
| 22      | SSH                         |
| 23      | Telnet                      |
| 25      | SMTP                        |
| 53      | DNS                         |
| 80      | HTTP                        |
| 110     | POP3                        |
| 123     | NTP                         |
| 139     | NetBIOS                     |
| 143     | IMAP                        |
| 161,162 | SNMP                        |
| 389     | LDAP                        |
| 443     | HTTPS                       |
| 465     | SMTPS                       |
| 636     | LDAPS                       |
| 993     | IMAPS                       |
| 995     | POP3S                       |

> تمام پورت‌های بالای ۴۰۰ به حرف S ختم می‌شوند که مخفف Secure (امن) است.

### /etc/services

فایل `/etc/services` حاوی اطلاعاتی در مورد سرویس‌های شناخته شده موجود در اینترنت است. برای هر سرویس، یک خط شامل اطلاعات زیر وجود دارد: 

`نام رسمی سرویس - شماره پورت/نام پروتکل - نام‌های مستعار`

```
root@ubuntu16-1:~# cat  /etc/services 
# Network services, Internet style
#
# Note that it is presently the policy of IANA to assign a single well-known
# port number for both TCP and UDP; hence, officially ports have two entries
# even if the protocol doesn't support UDP operations.
#
# Updated from http://www.iana.org/assignments/port-numbers and other
# sources like http://www.freebsd.org/cgi/cvsweb.cgi/src/etc/services .
# New ports will be added on request if they have been officially assigned
# by IANA and used in the real-world or are needed by a debian package.
# If you need a huge list of used numbers please install the nmap package.

tcpmux        1/tcp                # TCP port service multiplexer
echo        7/tcp
echo        7/udp
discard        9/tcp        sink null
discard        9/udp        sink null
systat        11/tcp        users
daytime        13/tcp
daytime        13/udp
netstat        15/tcp
qotd        17/tcp        quote
msp        18/tcp                # message send protocol
msp        18/udp
chargen        19/tcp        ttytst source
chargen        19/udp        ttytst source
ftp-data    20/tcp
ftp        21/tcp
fsp        21/udp        fspd
ssh        22/tcp                # SSH Remote Login Protocol
ssh        22/udp
telnet        23/tcp
smtp        25/tcp        mail
...
```

و به همین ترتیب ... . 

.

.

.

[https://www.computerhope.com/jargon/i/ip.htm](https://www.computerhope.com/jargon/i/ip.htm)

[https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html](https://www.cisco.com/c/en/us/support/docs/ip/routing-information-protocol-rip/13788-3.html)

[https://itstillworks.com/calculate-host-id-7542379.html](https://itstillworks.com/calculate-host-id-7542379.html)

[https://www.computerhope.com/jargon/n/netmask.htm](https://www.computerhope.com/jargon/n/netmask.htm)

[http://compunetworx.blogspot.com/2013/01/difference-between-hostid-and-netid-in.html](http://compunetworx.blogspot.com/2013/01/difference-between-hostid-and-netid-in.html)

[https://www.computerhope.com/jargon/s/subnetma.htm](https://www.computerhope.com/jargon/s/subnetma.htm)

[https://www.ibm.com/support/knowledgecenter/en/SSSHRK\_4.2.0/disco/concept/dsc\_private\_addr\_ranges.html](https://www.ibm.com/support/knowledgecenter/en/SSSHRK\_4.2.0/disco/concept/dsc\_private\_addr\_ranges.html)

[https://en.wikipedia.org/wiki/IPv6\_address#/media/File:Ipv6\_address\_leading\_zeros.svg](https://en.wikipedia.org/wiki/IPv6\_address#/media/File:Ipv6\_address\_leading\_zeros.svg)

[https://www.tutorialspoint.com/ipv4/ipv4\_subnetting.htm](https://www.tutorialspoint.com/ipv4/ipv4\_subnetting.htm)

[http://www.itgeared.com/articles/1347-cidr-and-subnetting-tutorial/](http://www.itgeared.com/articles/1347-cidr-and-subnetting-tutorial/)

[http://www.steves-internet-guide.com/tcpip-ports-sockets/](http://www.steves-internet-guide.com/tcpip-ports-sockets/)

[https://hub.packtpub.com/understanding-network-port-numbers-tcp-udp-and-icmp-on-an-operating-system/](https://hub.packtpub.com/understanding-network-port-numbers-tcp-udp-and-icmp-on-an-operating-system/)

[https://study-ccna.com/osi-tcp-ip-models/](https://study-ccna.com/osi-tcp-ip-models/)

[https://www.inetdaemon.com/tutorials/internet/tcp/3-way\_handshake.shtml](https://www.inetdaemon.com/tutorials/internet/tcp/3-way\_handshake.shtml)

[https://nordvpn.com/blog/tcp-or-udp-which-is-better/](https://nordvpn.com/blog/tcp-or-udp-which-is-better/)

[http://www.qnx.com/developers/docs/6.5.0/index.jsp?topic=%2Fcom.qnx.doc.neutrino\_utilities%2Fs%2Fservices.html](http://www.qnx.com/developers/docs/6.5.0/index.jsp?topic=%2Fcom.qnx.doc.neutrino\_utilities%2Fs%2Fservices.html)

Cisco got you here? [https://www.pcwdld.com/cisco-commands-cheat-sheet](https://www.pcwdld.com/cisco-commands-cheat-sheet)

.
