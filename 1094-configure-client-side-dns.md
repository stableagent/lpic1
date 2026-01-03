# 109.4 پیکربندی DNS در سمت کلاینت

**وزن:** ۲

**توضیحات:** داوطلبان باید قادر به پیکربندی DNS در یک هاست کلاینت باشند.

**حوزه‌های کلیدی دانش:**

* پرس‌وجو از سرورهای DNS راه دور
* پیکربندی ترجمه نام محلی (local name resolution) و استفاده از سرورهای DNS راه دور
* تغییر ترتیب انجام ترجمه نام

**اصطلاحات و ابزارهای کاربردی:**

* /etc/hosts
* /etc/resolv.conf
* /etc/nsswitch.conf
* host
* dig
* getent

ما تمام این موضوعات را در درس‌های قبلی دیده‌ایم، بنابراین در این درس ابتدا نگاهی سریع به آن‌ها می‌اندازیم و سپس در مورد مراحل ترجمه نام (Name Resolution) در سمت کلاینت صحبت خواهیم کرد.

#### DNS

سیستم نام دامنه (DNS) نام سایت‌های اینترنتی را به آدرس‌های IP زیربنایی آن‌ها ترجمه (resolve) می‌کند.

![](.gitbook/assets/clientdns-howdns.jpg)

#### پرس‌وجو از سرورهای DNS راه دور

### dig

دستور **Dig** مخفف (Domain Information Groper) یک ابزار خط فرمان قدرتمند برای پرس‌وجو از سرورهای نام DNS است. این ابزار به دلیل انعطاف‌پذیری و سهولت در استفاده، پرکاربردترین ابزار در میان مدیران سیستم برای عیب‌یابی مشکلات DNS است.

در ساده‌ترین حالت، وقتی برای پرس‌وجوی یک هاست (دامنه) واحد بدون هیچ گزینه اضافی استفاده می‌شود، خروجی دستور `dig` بسیار پرجزئیات (verbose) است.

```
root@ubuntu16-1:~# dig lpi.org

; <<>> DiG 9.10.3-P4-Ubuntu <<>> lpi.org
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 23520
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;lpi.org.            IN    A

;; ANSWER SECTION:
lpi.org.        599    IN    A    65.39.134.165

;; Query time: 501 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Sat Feb 29 17:16:01 +0330 2020
;; MSG SIZE  rcvd: 52
```

> فراموش نکنید که به طور پیش‌فرض، `dig` پرس‌وجوی DNS را به سرورهای نام لیست شده در بخش resolver (فایل `/etc/resolv.conf`) ارسال می‌کند، با این حال، می‌توانیم با استفاده از علامت `@` از یک سرور DNS متفاوت پرس‌وجو کنیم.

### host

دستور `host` ابزاری برای جستجوی DNS و یافتن آدرس IP یک نام دامنه است. این دستور همچنین جستجوی معکوس (reverse lookup) را انجام می‌دهد تا نام دامنه مرتبط با یک آدرس IP را پیدا کند.

```
root@ubuntu16-1:~# host yahoo.com
yahoo.com has address 72.30.35.10
yahoo.com has address 98.137.246.7
yahoo.com has address 98.138.219.231
yahoo.com has address 98.138.219.232
yahoo.com has address 98.137.246.8
yahoo.com has address 72.30.35.9
yahoo.com has IPv6 address 2001:4998:c:1023::4
yahoo.com has IPv6 address 2001:4998:58:1836::10
yahoo.com has IPv6 address 2001:4998:44:41d::4
yahoo.com has IPv6 address 2001:4998:58:1836::11
yahoo.com has IPv6 address 2001:4998:44:41d::3
yahoo.com has IPv6 address 2001:4998:c:1023::5
yahoo.com mail is handled by 1 mta6.am0.yahoodns.net.
yahoo.com mail is handled by 1 mta7.am0.yahoodns.net.
yahoo.com mail is handled by 1 mta5.am0.yahoodns.net.
```

 و برعکس، برای پیدا کردن نام میزبانِ یک آدرس IP:

```
root@ubuntu16-1:~# host 72.30.35.10
10.35.30.72.in-addr.arpa domain name pointer media-router-fp2.prod1.media.vip.bf1.yahoo.com.
```

> اگر هیچ آرگومان یا گزینه‌ای داده نشود، `host` خلاصه کوتاهی از آرگومان‌ها و گزینه‌های خط فرمان خود را چاپ می‌کند:

#### ترجمه نام کلاینت (Client Name Resolution) 

وقتی کلاینت می‌خواهد به کامپیوترهای دیگر در شبکه دسترسی پیدا کند، ابتدا باید آدرس IP هدف را بداند. مکان‌های مختلفی در سیستم‌عامل وجود دارد که این اطلاعات را نگه می‌دارند، بیایید آن‌ها را با هم مرور کنیم:

### /etc/hosts

اگر نمی‌خواهیم از یک سرور DNS برای ترجمه نام استفاده کنیم، می‌توانیم از فایل **/etc/hosts** برای این منظور استفاده کنیم. این یک فایل متنی ساده است که شامل نگاشت‌های (mappings) آدرس‌های IP به نام‌های میزبان است. هر خط شامل یک آدرس IP و به دنبال آن یک یا چند نام میزبان است (ubuntu16):

```
root@ubuntu16-1:~# cat /etc/hosts
127.0.0.1    localhost
127.0.1.1    ybyntu16-1

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

محتوای پیش‌فرض معمولی فایل **hosts** را می‌بینید که شامل ورودی‌هایی برای آدرس‌های loopback است. برای تنظیم نگاشت‌های خود، ورودی‌ها را به صورت **`IP_ADDRESS HOSTNAME `**:

```
root@ubuntu16-1:~# ping thisismyexample.com
ping: unknown host thisismyexample.com

root@ubuntu16-1:~# vim /etc/hosts
root@ubuntu16-1:~# 
root@ubuntu16-1:~# cat /etc/hosts
127.0.0.1    localhost
127.0.1.1    ybyntu16-1

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

# this is my example:
172.217.164.238 thisismyexample
```

خط **`172.217.164.238 thisismyexample.com` ** آدرس IP `172.217.164.238` را به نام میزبان `thisismyexample.com` نگاشت می‌کند. اکنون می‌توانیم از نام میزبان `thisismyexample` برای ارتباط با ماشین راه دور استفاده کنیم:

```
root@ubuntu16-1:~# ping thisismyexample -c3
PING thisismyexample (172.217.164.238) 56(84) bytes of data.
64 bytes from thisismyexample (172.217.164.238): icmp_seq=1 ttl=63 time=281 ms
64 bytes from thisismyexample (172.217.164.238): icmp_seq=2 ttl=63 time=324 ms
64 bytes from thisismyexample (172.217.164.238): icmp_seq=3 ttl=63 time=262 ms

--- thisismyexample ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 262.673/289.474/324.462/25.881 ms

```

### /etc/resolv.conf

فایل `/etc/resolv.conf` حاوی اطلاعاتی در مورد سرور DNS فعلی سیستم است. اگرچه می‌توانیم آن را به صورت دستی تغییر دهیم، اما فراموش نکنید که تنظیمات ما دائمی نخواهند بود و تا ریبوت بعدی باقی می‌مانند. 

```
root@ubuntu16-1:~# cat /etc/resolv.conf 
# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
nameserver 8.8.8.8
nameserver 4.2.2.4
search mydomain.local
```

### /etc/nsswitch.conf

فایل `/etc/nsswitch.conf` ترتیب تماس با سرویس‌های مختلف نام را تعریف می‌کند. برای استفاده از اینترنت، مهم است که `dns` در خط مربوط به "hosts" ظاهر شود:

```
root@ubuntu16-1:~# cat /etc/nsswitch.conf | grep hosts
hosts:          files mdns4_minimal [NOTFOUND=return] dns
```

خط hosts مشخص می‌کند که سرویس‌های مختلف ترجمه نام با چه ترتیبی امتحان شوند. حالت پیش‌فرض به این صورت است:

* `files`: فایل `/etc/hosts` را می‌خواند.
* `mdns4_minimal`: آدرس‌های IPv4 را با multicast DNS ترجمه می‌کند **فقط** اگر نام میزبان درخواستی به `.local` ختم شود.
* `[NOTFOUND=return]`: اگر آن نام میزبان `.local` پیدا نشد، فرآیند ترجمه را متوقف می‌کند.
* `dns`: ترجمه DNS را انجام می‌دهد.

> شما می‌توانید ترتیب ترجمه نام را در اینجا تغییر دهید.

### getent

همانطور که گفتیم، **getent** یک دستور لینوکس است که به کاربر کمک می‌کند ورودی‌های تعدادی از فایل‌های متنی مهم که پایگاه داده نامیده می‌شوند را دریافت کند. این شامل پایگاه‌های داده `passwd` و `group` است که اطلاعات کاربران را ذخیره می‌کنند. واقعیت این است که دستور **getent** ورودی‌هایی را از پایگاه‌های داده‌ای که توسط کتابخانه‌های Name Service Switch پشتیبانی می‌شوند (و در `/etc/nsswitch.conf` پیکربندی شده‌اند) نمایش می‌دهد. 

```
root@ubuntu16-1:~# getent hosts thisismyexample
172.217.164.238 thisismyexample
```

.

.

.

[https://www.networkworld.com/article/3268449/what-is-dns-and-how-does-it-work.html](https://www.networkworld.com/article/3268449/what-is-dns-and-how-does-it-work.html)

[https://computer.howstuffworks.com/dns3.htm](https://computer.howstuffworks.com/dns3.htm)

[https://linuxize.com/post/how-to-use-dig-command-to-query-dns-in-linux/](https://linuxize.com/post/how-to-use-dig-command-to-query-dns-in-linux/)

****[https://www.computerhope.com/unix/host.htm](https://www.computerhope.com/unix/host.htm)

[https://www.geeksforgeeks.org/host-command-in-linux-with-examples/](https://www.geeksforgeeks.org/host-command-in-linux-with-examples/)

[https://geek-university.com/linux/etc-hosts-file/](https://geek-university.com/linux/etc-hosts-file/)

[https://www.shellhacks.com/setup-dns-resolution-resolvconf-example/](https://www.shellhacks.com/setup-dns-resolution-resolvconf-example/)[https://www.linuxtopia.org/online_books/introduction_to_linux/linux\_\_etc_nsswitch.conf.html](https://www.linuxtopia.org/online_books/introduction_to_linux/linux\_\_etc_nsswitch.conf.html)[https://www.reddit.com/r/linuxquestions/comments/co02ui/hosts_and_mdns_configuration_in_etcnsswitchconf/](https://www.reddit.com/r/linuxquestions/comments/co02ui/hosts_and_mdns_configuration_in_etcnsswitchconf/)

[https://ubuntuforums.org/showthread.php?t=971693](https://ubuntuforums.org/showthread.php?t=971693)

[https://www.geeksforgeeks.org/getent-command-in-linux-with-examples/](https://www.geeksforgeeks.org/getent-command-in-linux-with-examples/)

[https://linux.die.net/man/1/getent](https://linux.die.net/man/1/getent)

.
