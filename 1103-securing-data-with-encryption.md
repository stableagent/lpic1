# 110.3 امن‌سازی داده‌ها با رمزگذاری

**وزن:** ۳

**توضیحات:** داوطلب باید قادر به استفاده از تکنیک‌های کلید عمومی برای امن‌سازی داده‌ها و ارتباطات باشد.

**حوزه‌های کلیدی دانش:**

* انجام پیکربندی و استفاده پایه از کلاینت OpenSSH 2
* درک نقش کلیدهای هاست در سرور OpenSSH 2
* انجام پیکربندی، استفاده و ابطال (revocation) پایه GnuPG
* درک تونل‌های پورت SSH (شامل تونل‌های X11)

**اصطلاحات و ابزارهای کاربردی:**

* ssh
* ssh-keygen
* ssh-agent
* ssh-add
* \~/.ssh/id\_rsa and id\_rsa.pub
* \~/.ssh/id\_dsa and id\_dsa.pub
* /etc/ssh/ssh\_host\_rsa\_key and ssh\_host\_rsa\_key.pub
* /etc/ssh/ssh\_host\_dsa\_key and ssh\_host\_dsa\_key.pub
* \~/.ssh/authorized\_keys
* ssh\_known\_hosts
* gpg
* \~/.gnupg/

ابتدا بیایید با برخی مفاهیم شروع کنیم.

## رمزنگاری (Cryptography)

 رمزنگاری روشی برای استفاده از اصول ریاضی پیشرفته در ذخیره‌سازی و انتقال داده‌ها به شکلی خاص است، به طوری که فقط کسانی که هدف ارسال بوده‌اند بتوانند آن را بخوانند و پردازش کنند. رمزگذاری (Encryption) یک مفهوم کلیدی در رمزنگاری است.

* **رمزگذاری (Encryption)**: در رمزنگاری، رمزگذاری فرآیند کدگذاری یک پیام یا اطلاعات به گونه‌ای است که فقط طرف‌های مجاز بتوانند به آن دسترسی داشته باشند و کسانی که مجاز نیستند نتوانند.
* **رمزگشایی (Decryption)**: تبدیل داده‌های رمزگذاری شده به شکل اصلی خود رمزگشایی نامیده می‌شود. این به طور کلی فرآیند معکوس رمزگذاری است.

### رمزگذاری متقارن در مقابل نامتقارن

الگوریتم‌های رمزگذاری اغلب به دو دسته تقسیم می‌شوند: رمزگذاری متقارن (symmetric) و نامتقارن (asymmetric).

#### رمزگذاری متقارن (Symmetric Encryption)

![](.gitbook/assets/securedata-symetric.jpg)

رمزگذاری متقارن قدیمی‌ترین و شناخته‌شده‌ترین تکنیک است. یک کلید مخفی که می‌تواند یک عدد، یک کلمه یا صرفاً رشته‌ای از حروف تصادفی باشد، روی متن پیام اعمال می‌شود تا محتوا را به شکلی خاص تغییر دهد. این کار می‌تواند به سادگیِ جابجایی هر حرف به تعداد مشخصی در الفبا باشد. تا زمانی که فرستنده و گیرنده هر دو کلید مخفی را بدانند، می‌توانند تمام پیام‌هایی را که از این کلید استفاده می‌کنند، رمزگذاری و رمزگشایی کنند.

#### رمزگذاری نامتقارن (Asymmetric Encryption)

مشکل کلیدهای مخفی، تبادل آن‌ها در اینترنت یا یک شبکه بزرگ است، در حالی که باید از افتادن آن‌ها به دست افراد اشتباه جلوگیری کرد. هر کسی که کلید مخفی را بداند می‌تواند پیام را رمزگشایی کند. یک پاسخ برای این مشکل، رمزگذاری نامتقارن است که در آن دو کلید مرتبط وجود دارد - یک جفت کلید. یک کلید عمومی (public key) به صورت آزاد در اختیار هر کسی که بخواهد برای شما پیامی بفرستد قرار می‌گیرد. کلید دوم، کلید خصوصی (private key)، مخفی نگه داشته می‌شود تا فقط شما آن را بدانید.

![](.gitbook/assets/securedata-asymetric.jpg)

هر پیامی (متن، فایل‌های باینری یا اسناد) که با استفاده از کلید عمومی رمزگذاری شده باشد، فقط با استفاده از همان الگوریتم و کلید خصوصیِ متناظر قابل رمزگشایی است. همچنین هر پیامی که با استفاده از کلید خصوصی رمزگذاری شده باشد، فقط با استفاده از کلید عمومی متناظر قابل رمزگشایی است.

این بدان معناست که لازم نیست نگران انتقال کلیدهای عمومی در اینترنت باشید (قرار است این کلیدها عمومی باشند).

> با این حال، یک مشکل در رمزگذاری نامتقارن این است که از رمزگذاری متقارن کندتر است. این روش برای رمزگذاری و رمزگشایی محتوای پیام به توان پردازشی بسیار بیشتری نیاز دارد.

{% hint style="info" %}
**رمزگذاری در مقابل امضا (Signing)**

هنگام رمزگذاری، شما از **کلید عمومی آن‌ها** برای نوشتن پیام استفاده می‌کنید و آن‌ها از **کلید خصوصی خود** برای خواندن آن استفاده می‌کنند.

هنگام امضا کردن، شما از **کلید خصوصی خود** برای ایجاد امضای پیام استفاده می‌کنید و آن‌ها از **کلید عمومی شما** برای بررسی اینکه آیا پیام واقعاً از طرف شماست استفاده می‌کنند.
{% endhint %}

{% hint style="success" %}
#### سرور کلید (Key Server) چیست؟

سرور کلید یک سرور است که کلیدهای عمومی در آن ذخیره می‌شوند تا دیگران بتوانند از آن‌ها استفاده کنند.
{% endhint %}

با این مقدمه بیایید در مورد SSH صحبت کنیم.

## SSH چیست؟

پروتکل SSH (که به نام Secure Shell نیز شناخته می‌شود) روشی برای لاگین امن از راه دور از یک کامپیوتر به کامپیوتر دیگر است. این پروتکل چندین گزینه جایگزین برای احراز هویت قوی فراهم می‌کند و امنیت و یکپارچگی ارتباطات را با رمزگذاری قوی محافظت می‌کند. SSH یک جایگزین امن برای پروتکل‌های لاگین محافظت نشده (مانند telnet، rlogin) و روش‌های انتقال فایل ناامن (مانند FTP) است.

کاربردهای معمول پروتکل SSH عبارتند از:

* فراهم کردن دسترسی امن برای کاربران و فرآیندهای خودکار
* انتقال فایل به صورت تعاملی و خودکار
* صدور دستورات از راه دور
* مدیریت زیرساخت شبکه و سایر اجزای حیاتی سیستم.

### پروتکل SSH چگونه کار می‌کند؟

نحوه عملکرد SSH بر اساس استفاده از مدل کلاینت-سرور است تا امکان احراز هویت دو سیستم از راه دور و رمزگذاری داده‌هایی را که بین آن‌ها جابجا می‌شود فراهم کند.

SSH به طور پیش‌فرض روی پورت ۲۲ پروتکل TCP کار می‌کند (هرچند در صورت نیاز قابل تغییر است). هاست (سرور) برای اتصالات ورودی روی پورت ۲۲ (یا هر پورت اختصاص یافته دیگری برای SSH) گوش می‌دهد.

SSH چندین مکانیسم برای احراز هویت سرور و کلاینت فراهم می‌کند. دو مکانیسم رایج احراز هویت، احراز هویت مبتنی بر رمز عبور و احراز هویت مبتنی بر کلید هستند. اگرچه احراز هویت مبتنی بر رمز عبور نیز امن است، اما توصیه می‌شود در عوض از احراز هویت مبتنی بر کلید استفاده کنید.

![](.gitbook/assets/ssh-howitworks.jpg)

اتصال با اتصال کلاینت SSH به سرور SSH برقرار می‌شود. کلاینت SSH فرآیند راه‌اندازی اتصال را هدایت می‌کند و از رمزنگاری کلید عمومی برای تأیید هویت سرور SSH استفاده می‌کند. پس از مرحله راه‌اندازی، پروتکل SSH از رمزگذاری متقارن قوی و الگوریتم‌های هشینگ (hashing) برای اطمینان از محرمانگی و یکپارچگی داده‌های تبادل شده بین کلاینت و سرور استفاده می‌کند.

### OpenSSH چیست؟

OpenSSH یک پیاده‌سازی رایگان و متن‌باز از پروتکل‌های SSH است. OpenSSH به دلیل قابلیت چند‌سکویی (multi-platform) و ویژگی‌های بسیار مفیدش، در میان مدیران سیستم بسیار محبوب است.

![](.gitbook/assets/openssh-logo.png)

تمام ارتباطات و اعتبارنامه‌های کاربر با استفاده از OpenSSH رمزگذاری می‌شوند؛ آن‌ها همچنین در برابر حملات مرد میانی (man-in-the-middle) محافظت می‌شوند. اگر شخص ثالثی سعی کند اتصال ما را رهگیری کند، OpenSSH آن را تشخیص داده و به ما اطلاع می‌دهد.

{% hint style="success" %}
ما از Ubuntu16-1 به عنوان سرور SSH و از Ubuntu16-2 به عنوان کلاینت استفاده می‌کنیم.
{% endhint %}

#### /etc/ssh

OpenSSH دارای دو مجموعه متفاوت از فایل‌های پیکربندی است: یکی برای برنامه‌های کلاینت (ssh، scp و sftp) و دیگری برای دیمون سرور (sshd).

```
root@ubuntu16-1:~# ls -1 /etc/ssh
moduli
ssh_config
sshd_config
ssh_host_dsa_key
ssh_host_dsa_key.pub
ssh_host_ecdsa_key
ssh_host_ecdsa_key.pub
ssh_host_ed25519_key
ssh_host_ed25519_key.pub
ssh_host_rsa_key
ssh_host_rsa_key.pub
ssh_import_id
```

فایل `sshd_config` فایل پیکربندی دیمون SSH (یا فرآیند سرور SSH) است، در حالی که فایل `ssh_config` فایل پیکربندی کلاینت SSH است. فایل پیکربندی کلاینت فقط زمانی تأثیر دارد که شما از دستور `ssh` برای اتصال به هاست SSH دیگری استفاده می‌کنید. همانطور که می‌بینید، در اینجا کلیدهای عمومی و خصوصی با الگوریتم‌های مختلف وجود دارند که می‌توانند توسط SSH برای رمزگذاری نشست استفاده شوند.

## تنظیمات کلاینت SSH

تا به اینجا فهمیده‌ایم که SSH چگونه کار می‌کند. همانطور که اشاره کردیم، وقتی اتصال SSH شروع می‌شود، کلید عمومی سرور SSH به کلاینت منتقل می‌شود (و در `~/.ssh/known_hosts` ذخیره می‌گردد) و کلاینت از آن برای ادامه مذاکره با سرور استفاده می‌کند. سپس کاربر باید با ارسال نام کاربری و رمز عبور احراز هویت شود.

بیایید با اتصال به Ubuntu16-1 از Ubuntu16-2 شروع کنیم و کلیدها را ببینیم:

```
user1@ubuntu16-2:~$ ssh user1@192.168.52.146
The authenticity of host '192.168.52.146 (192.168.52.146)' can't be established.
ECDSA key fingerprint is SHA256:GV/PpX9YGvMZTAbuz6w3zBDreokesZHhVSM1zrXmHLw.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.52.146' (ECDSA) to the list of known hosts.
user1@192.168.52.146's password: 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 15:51:55 2020 from 192.168.52.133
user1@ubuntu16-1:~$ 
```

{% hint style="info" %}
**اثر انگشت (Fingerprint) چیست؟** اثر انگشتِ کلید عمومی، توالی کوتاهی از بایت‌هاست که برای شناسایی یک کلید عمومی طولانی‌تر استفاده می‌شود. اثر انگشت‌ها با اعمال یک تابع هش رمزنگاری روی کلید عمومی ایجاد می‌شوند. از آنجایی که اثر انگشت‌ها کوتاه‌تر از کلیدهایی هستند که به آن‌ها اشاره می‌کنند، می‌توانند برای ساده‌سازی برخی وظایف مدیریت کلید استفاده شوند.
{% endhint %}

حالا بیایید کلیدها را در سرور و کلاینت مقایسه کنیم:

```
### سرور
user1@ubuntu16-1:~$ cat /etc/ssh/ssh_host_ecdsa_key.pub 
ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIjnKq9Wr0C2faQCf4+gcqPN4bOMFyx1nywTjLS/mh/S30V0r/mvy9cvfWvA2LY/y7zqxg+/gMvELQznikQiaTo= root@server1
```

```
### کلاینت
user1@ubuntu16-2:~$ tree .ssh/
.ssh/
└── known_hosts

0 directories, 1 file
user1@ubuntu16-2:~$ cat .ssh/known_hosts 
|1|gD2R1tW6jKBSyL1A0XpynmG8Vok=|1X2nzVcwHLQGT5T8FOUxeCejtvQ= ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIjnKq9Wr0C2faQCf4+gcqPN4bOMFyx1nywTjLS/mh/S30V0r/mvy9cvfWvA2LY/y7zqxg+/gMvELQznikQiaTo=
```

### پیکربندی احراز هویت مبتنی بر کلید SSH

این امکان وجود دارد که وارد کردن نام کاربری و رمز عبور را حذف کرده و با استفاده از جفت کلید عمومی و خصوصیِ کلاینت به سرور SSH متصل شد.

![](.gitbook/assets/ssh-keybasedauth.jpg)

حالا بیایید جفت کلید عمومی و خصوصی را برای کلاینت ایجاد کرده و کلید عمومی کلاینت را به سرور کپی کنیم.

### ssh-keygen

ssh-keygen - یک جفت کلید برای احراز هویت کلید عمومی ایجاد می‌کند:

```
user1@ubuntu16-2:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user1/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user1/.ssh/id_rsa.
Your public key has been saved in /home/user1/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:rer76yQU+8Mmrg33xCA51RtnpTUVHT1cVMX7kB2+aUI user1@ubuntu16-2
The key's randomart image is:
+---[RSA 2048]----+
|            +.+*@|
|       .   + . ++|
|      o o +   .o+|
|     o o *   Eoo.|
|    + + S . .  .+|
|     + = .   . +.|
|    . + X     o  |
|     = O .       |
|    .o*+=.       |
+----[SHA256]-----+
user1@ubuntu16-2:~$ 
user1@ubuntu16-2:~$ tree .ssh
.ssh
├── id_rsa
├── id_rsa.pub
└── known_hosts

0 directories, 3 files
user1@ubuntu16-2:~$ cat .ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC818rxUXbwnwwxtFhUKvgtZV+Ygao1nUEHcaBvYEsXsBa4hQcV0+ITPEMHfk0zUag3sKyQZWckKmREK+lpiF+7Nrw83eKxjgHdwZC0ibxPTenklZNSBEMZMBDeq8H3bKoAfuyczX0IfVDA2Iyebsg2KYIIZQ/Otw7hAm2IH3perUqzeYliLhIYb0Gd3jyOpl4VMaPb2p+f5+fG87MnzjDplyorruhZyUcv8CMUc6XZ3dJjeiSsNNCRKLEb6Cm6msQxuCUq+Xz1n0+ay6fsaJbbhzFwNvbRH2YSzBg5BmtBXVt68U6XM3SzynWAqQaDS44Cuv3M1q88baTlOTjRkFRZ user1@ubuntu16-2
user1@ubuntu16-2:~$ 


```

ما در نمایش خود عبارت عبور (passphrase) را تنظیم نکردیم، اما اگر تنظیم می‌کردیم، هنگام کپی کردن آن به سرور، از ما خواسته می‌شد آن را وارد کنیم. 

### ssh-copy-id

ما از `ssh-copy-id` برای پیکربندی یک کلید عمومی به عنوان کلید مجاز (authorized) در یک سرور استفاده می‌کنیم.

```
user1@ubuntu16-2:~$ ssh-copy-id -i .ssh/id_rsa.pub user1@192.168.52.146
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ".ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
user1@192.168.52.146's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'user1@192.168.52.146'"
and check to make sure that only the key(s) you wanted were added.
```

حالا بیایید نگاهی به سمت سرور بیندازیم:

```
user1@ubuntu16-1:~$ cat .ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC818rxUXbwnwwxtFhUKvgtZV+Ygao1nUEHcaBvYEsXsBa4hQcV0+ITPEMHfk0zUag3sKyQZWckKmREK+lpiF+7Nrw83eKxjgHdwZC0ibxPTenklZNSBEMZMBDeq8H3bKoAfuyczX0IfVDA2Iyebsg2KYIIZQ/Otw7hAm2IH3perUqzeYliLhIYb0Gd3jyOpl4VMaPb2p+f5+fG87MnzjDplyorruhZyUcv8CMUc6XZ3dJjeiSsNNCRKLEb6Cm6msQxuCUq+Xz1n0+ay6fsaJbbhzFwNvbRH2YSzBg5BmtBXVt68U6XM3SzynWAqQaDS44Cuv3M1q88baTlOTjRkFRZ user1@ubuntu16-2
```

حالا بیایید نتیجه را از سمت کلاینت بررسی کنیم:

```
user1@ubuntu16-2:~$ ssh user1@192.168.52.146
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 15:57:29 2020 from 192.168.52.133
user1@ubuntu16-1:~$ 

```

و به نظر می‌رسد همه چیز درست است. اگر بخواهید می‌توانید کلیدها را برای کاربران دیگر کپی و جایگذاری کنید، اما فراموش نکنید که این کلیدها به کاربران قدرت می‌دهند تا بدون رمز عبور لاگین کنند.

#### چرا از عبارت عبور (passphrase) استفاده کنیم؟ کاربرد آن چیست؟

ما یک اتصال SSH بدون رمز عبور را با استفاده از احراز هویت مبتنی بر کلید پیکربندی کرده‌ایم. اما اگر سیستم ما در معرض خطر قرار گیرد چه اتفاقی می‌افتد؟ یک هکر خبیث می‌تواند بدون دانستن رمزهای عبور، با استفاده از احراز هویت مبتنی بر کلید به سرورهای دیگر متصل شود.

عبارت عبور (Passphrase) می‌تواند با درخواست یک رمز در ابتدای هر احراز هویت مبتنی بر کلید، به ما در جلوگیری از این نوع مشکلات امنیتی کمک کند. پس بیایید `authorized_keys` قبلی را پاک کرده و شروع کنیم:

```
user1@ubuntu16-1:~$ 
user1@ubuntu16-1:~$ vim .ssh/authorized_keys 
user1@ubuntu16-1:~$ cat .ssh/authorized_keys 
user1@ubuntu16-1:~$ exit
logout
Connection to 192.168.52.146 closed.
```

حالا یک جفت کلید جدید با عبارت عبور روی کلاینت ایجاد کنید (اجازه دهید روی کلیدهای خصوصی و عمومی فعلی بازنویسی شود):

```
user1@ubuntu16-2:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user1/.ssh/id_rsa): 
/home/user1/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/user1/.ssh/id_rsa.
Your public key has been saved in /home/user1/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:NYUt7TiSY2pBH7jtmZqGDdV545CRZxTFvOg3KJ3trxo user1@ubuntu16-2
The key's randomart image is:
+---[RSA 2048]----+
|       . ooOo    |
|      o + =.=    |
|     . = Oo= .   |
|      + %.*.o    |
|     . =S@ *     |
|    . o = * +    |
|     = o .Eo .   |
|    . =    ..    |
|     .    ...o.  |
+----[SHA256]-----+
user1@ubuntu16-2:~$ 
user1@ubuntu16-2:~$ tree .ssh/
.ssh/
├── id_rsa
├── id_rsa.pub
└── known_hosts

0 directories, 3 files
user1@ubuntu16-2:~$ cat .ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDjF4K2XDLQIysT9QvwQKFFvJ6LeBN3XsmEO9cTZfnRPhPjKOpcwvyCPaFJwXpiSXLE+RUjy2lwghZ6sOIXezGG9+oqkVegBOJ9RonQfvg5nUCr/Khx+dT/5ZV8JEjJVYqWnrgxKiKgzFHfzInE3qKG4kN2yuanomqIPFGQs0Mk/ShmYCPEEDIxyapRWkSJMa1OeS4/Elk1gGcna0TwgNVF8zmGkg5JO3ruwia2uSbdDWxA2vVtae2qA02lFh0Gb/LJO8vR24MAwlyMMHU2UdszA4eWqQBrZtpKmQ0A4plT8EUkh2cZHgaUeMVloWDmFRyiU2LIFgC5AacnwRIFTtTD user1@ubuntu16-2
user1@ubuntu16-2:~$ 
```

حالا بیایید کلید عمومی جدیدمان را به سرور منتقل کنیم:

```
user1@ubuntu16-2:~$ ssh-copy-id -i .ssh/id_rsa.pub user1@192.168.52.146
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ".ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
user1@192.168.52.146's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'user1@192.168.52.146'"
and check to make sure that only the key(s) you wanted were added.
```

بیایید کلیدی را که روی سرور کپی کرده‌ایم بررسی کنیم:

```
user1@ubuntu16-1:~$ cat .ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDjF4K2XDLQIysT9QvwQKFFvJ6LeBN3XsmEO9cTZfnRPhPjKOpcwvyCPaFJwXpiSXLE+RUjy2lwghZ6sOIXezGG9+oqkVegBOJ9RonQfvg5nUCr/Khx+dT/5ZV8JEjJVYqWnrgxKiKgzFHfzInE3qKG4kN2yuanomqIPFGQs0Mk/ShmYCPEEDIxyapRWkSJMa1OeS4/Elk1gGcna0TwgNVF8zmGkg5JO3ruwia2uSbdDWxA2vVtae2qA02lFh0Gb/LJO8vR24MAwlyMMHU2UdszA4eWqQBrZtpKmQ0A4plT8EUkh2cZHgaUeMVloWDmFRyiU2LIFgC5AacnwRIFTtTD user1@ubuntu16-2
```

اکنون وقتی از کلاینت (ubuntu16-2) به سرور راه دور (ubuntu16-1) متصل می‌شویم، به جای رمز عبور حساب کاربری راه دور، از ما خواسته می‌شود عبارت عبور کلید محلی خود را وارد کنیم:

```
user1@ubuntu16-2:~$ ssh user1@192.168.52.146
Enter passphrase for key '/home/user1/.ssh/id_rsa': 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 16:19:57 2020 from 192.168.52.133

```

بیایید خارج شویم و بارها و بارها متصل شویم:

```
user1@ubuntu16-1:~$ exit
logout
Connection to 192.168.52.146 closed.

user1@ubuntu16-2:~$ ssh user1@192.168.52.146
Enter passphrase for key '/home/user1/.ssh/id_rsa': 
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 16:30:00 2020 from 192.168.52.133
user1@ubuntu16-1:~$ exit
logout
Connection to 192.168.52.146 closed.

```

همانطور که می‌بینید، هر بار از ما خواسته می‌شود عبارت عبور را وارد کنیم و این دقیقاً همان چیزی بود که به دنبالش بودیم تا در صورت نفوذ به سیستم ما، جلوی هکر گرفته شود. راهی وجود دارد که عبارت عبور را به نشست (session) کاربر فعلی متصل کنیم و آن را برای اتصالات بعدی SSH نگه داریم تا از وارد کردن مکرر آن خودداری شود.

### ssh-agent

 **ssh-agent** یک برنامه کمکی است که کلیدهای هویتی کاربر و عبارت‌های عبور آن‌ها را ردیابی می‌کند. سپس اِجنت می‌تواند از این کلیدها برای لاگین به سرورهای دیگر استفاده کند بدون اینکه کاربر نیاز داشته باشد دوباره رمز عبور یا عبارت عبور را تایپ کند. این کار شکلی از ورود یکباره (SSO) را پیاده‌سازی می‌کند. اِجنت SSH برای احراز هویت کلید عمومی SSH استفاده می‌شود.

```
user1@ubuntu16-2:~$ ssh-agent
SSH_AUTH_SOCK=/tmp/ssh-7dlnU2k64PSA/agent.65150; export SSH_AUTH_SOCK;
SSH_AGENT_PID=65151; export SSH_AGENT_PID;
echo Agent pid 65151;
```

> اگر با خطا مواجه شدید، ابتدا `ssh-agent /bin/bash` را امتحان کنید.

### ssh-add

به طور پیش‌فرض، اِجنت از کلیدهای SSH ذخیره شده در دایرکتوری `.ssh` در دایرکتوری خانگی کاربر استفاده می‌کند. دستور **ssh-add** برای افزودن هویت‌ها به اِجنت استفاده می‌شود. در ساده‌ترین حالت، کافیست آن را بدون آرگومان اجرا کنید تا فایل‌های پیش‌فرض `~/.ssh/id_rsa` ، `.ssh/id_dsa` ، `~/.ssh/id_ecdsa` ، `~/.ssh/id_ed25519` و `~/.ssh/identity` اضافه شوند. در غیر این صورت، نام فایل کلید خصوصی را به عنوان آرگومان به آن بدهید.

```
user1@ubuntu16-2:~$ ssh-add 
Enter passphrase for /home/user1/.ssh/id_rsa: 
Identity added: /home/user1/.ssh/id_rsa (/home/user1/.ssh/id_rsa)

user1@ubuntu16-2:~$ ssh-add -l
2048 SHA256:NYUt7TiSY2pBH7jtmZqGDdV545CRZxTFvOg3KJ3trxo /home/user1/.ssh/id_rsa (RSA)
user1@ubuntu16-2:~$ 
```

گزینه `-l` کلیدهای خصوصی را که در حال حاضر برای اِجنت قابل دسترسی هستند، لیست می‌کند. 

گزینه `-D` تمام هویت‌ها را از اِجنت پاک می‌کند، اگر تمایل داشتید!

و سپس می‌توانیم بارها و بارها بدون نیاز به هیچ رمز عبور یا عبارت عبوری به سرور OpenSSH (ubuntu16-1) متصل شویم:

```
user1@ubuntu16-2:~$ ssh user1@192.168.52.146
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

445 packages can be updated.
365 updates are security updates.

New release '18.04.4 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Fri Mar 27 16:31:26 2020 from 192.168.52.133
user1@ubuntu16-1:~$ exit
logout

```

تا زمانی که از بشی (bash) که از کلید مرتبط استفاده می‌کند خارج نشویم؛ پس از آن دوباره نیاز به وارد کردن عبارت عبور خواهیم داشت.

#### تونل‌زنی SSH (SSH Tunneling) چیست؟

تونل‌زنی SSH روشی برای انتقال داده‌های دلخواه شبکه بر روی یک اتصال رمزگذاری شده SSH است. می‌توان از آن برای افزودن رمزگذاری به اپلیکیشن‌های قدیمی استفاده کرد. همچنین می‌توان از آن برای پیاده‌سازی VPNها و دسترسی به سرویس‌های اینترانت از پشت فایروال‌ها استفاده نمود.

SSH استانداردی برای لاگین از راه دور و انتقال فایل به صورت امن بر روی شبکه‌های غیرقابل اعتماد است. همچنین راهی برای امن‌سازی ترافیک داده‌های هر اپلیکیشن با استفاده از ارسال پورت (port forwarding) فراهم می‌کند که اساساً تونل‌زنی هر پورت TCP/IP بر روی SSH است. این بدان معناست که ترافیک داده‌های اپلیکیشن به گونه‌ای هدایت می‌شود که در داخل یک اتصال رمزگذاری شده SSH جریان یابد تا در حین انتقال، قابل شنود یا رهگیری نباشد. تونل‌زنی SSH امکان افزودن امنیت شبکه به اپلیکیشن‌های قدیمی را که به طور ذاتی از رمزگذاری پشتیبانی نمی‌کنند، فراهم می‌کند.

![](.gitbook/assets/ssh-tunneling.jpg)

#### ارسال پورت (port forwarding) چیست؟

 ارسال پورت SSH مکانیزمی در SSH برای تونل‌زنی پورت‌های اپلیکیشن از ماشین کلاینت به ماشین سرور یا برعکس است. برخی مدیران سیستم و متخصصان IT از آن برای باز کردن درهای پشتی (backdoors) به شبکه داخلی از کامپیوترهای خانگی خود استفاده می‌کنند. همچنین هکرها و بدافزارها می‌توانند از آن برای ایجاد دسترسی از اینترنت به شبکه داخلی سوءاستفاده کنند.

ارسال پورت SSH سه نوع است:

* **Local port forwarding** - اتصالات از یک کلاینت SSH، از طریق سرور SSH، به یک سرور مقصد فرستاده می‌شوند.

![](.gitbook/assets/ssh-portfwl1.jpg)

![](.gitbook/assets/ssh-portfwl2.jpg)

* **Remote port forwarding** - اتصالات از یک سرور SSH، از طریق کلاینت SSH، به یک سرور مقصد فرستاده می‌شوند.

![](.gitbook/assets/ssh-portfwlr1.jpg)

![](.gitbook/assets/ssh-portfwlr2.jpg)

* **Dynamic port forwarding** - اتصالات از برنامه‌های مختلف، از طریق کلاینت SSH به یک سرور SSH و در نهایت به چندین سرور مقصد فرستاده می‌شوند.

### ssh

مانند سایر دستورات، `ssh` نیز گزینه‌هایی دارد. بیایید نگاهی به کاربردی‌ترین سوئیچ‌ها بیندازیم:

| دستورات SSH                             | توضیحات                                                                                                       |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| ssh -V                                   | نمایش نسخه کلاینت SSH                                                                                          |
| ssh user1@server1.example.com            | اتصال به هاست راه دور؛ برای نمایش جزئیات گزینه "-v" را اضافه کنید                                                             |
| ssh user1@server1.example.com \<command> | اجرای دستور \<command> روی هاست راه دور از طریق SSH                                                                    |
| ssh -X user@server1.example.com          | فعال کردن Xforwarding در سمت کلاینت؛ دقت کنید که X11Forwarding باید در سمت سرور در فایل sshd\_config فعال باشد. |

## رمزگذاری داده‌ها

 رمزگذاری بسیار **مهم است** زیرا به شما اجازه می‌دهد تا از داده‌هایی که نمی‌خواهید هیچ‌کس دیگری به آن‌ها دسترسی داشته باشد، به طور ایمن محافظت کنید. 

### GPG

GnuPG (که بیشتر به نام GPG شناخته می‌شود) پیاده‌سازی استانداردی به نام PGP (Pretty Good Privacy) است. این برنامه از سیستمی از کلیدهای "عمومی" و "خصوصی" برای رمزگذاری و امضای پیام‌ها یا داده‌ها استفاده می‌کند.

برای نمایش، ما از دو کاربر در اوبونتو ۱۶ استفاده می‌کنیم: user1 و user2.

![](.gitbook/assets/securedata-gpgencrypt.jpg)

بسیار خب، بیایید با user1 وارد شویم و با استفاده از دستور `gpg --gen-key` شروع به ایجاد جفت کلید کنیم:

```
user1@ubuntu16-1:~$ gpg --gen-key 
gpg (GnuPG) 1.4.20; Copyright (C) 2015 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: keyring `/home/user1/.gnupg/secring.gpg' created
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 
Key does not expire at all
Is this correct? (y/N) y

You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name: RealUser1
Email address: user1@localhost
Comment: created by user1
You selected this USER-ID:
    "RealUser1 (created by user1) <user1@localhost>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
You need a Passphrase to protect your secret key.

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
.+++++
+++++
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

Not enough random bytes available.  Please do some other work to give
the OS a chance to collect more entropy! (Need 74 more bytes)
....+++++

gpg: key 6D187851 marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   2048R/6D187851 2020-03-28
      Key fingerprint = 8772 5C1E 3F2F 88DB DBB7  7F37 E06C 3317 6D18 7851
uid                  RealUser1 (created by user1) <user1@localhost>
sub   2048R/F00A0A74 2020-03-28
```

> این کار کلیدها را در دایرکتوری `~/.gnupg` ایجاد می‌کند.

بیایید جفت کلید ایجاد شده را با استفاده از دستور `gpg --list-key` ببینیم:

```
user1@ubuntu16-1:~$ gpg --list-key
/home/user1/.gnupg/pubring.gpg
------------------------------
pub   2048R/6D187851 2020-03-28
uid                  RealUser1 (created by user1) <user1@localhost>
sub   2048R/F00A0A74 2020-03-28
```

سپس باید کلید عمومی خود را با دیگران به اشتراک بگذاریم. برای استخراج (export) فایل کلید عمومی باید دستور زیر را اجرا کنیم:

```
user1@ubuntu16-1:~$ gpg --export RealUser1 > user1.pub
```

بیایید آن را در دایرکتوری /tmp برای user2 قرار دهیم:

```
user1@ubuntu16-1:~$ mv user1.pub /tmp/
```

حالا به عنوان user2 وارد شوید و کلید عمومی user1 را با استفاده از `gpg --import` وارد کنید:

```
user2@ubuntu16-1:~$ gpg --import /tmp/user1.pub 
gpg: directory `/home/user2/.gnupg' created
gpg: new configuration file `/home/user2/.gnupg/gpg.conf' created
gpg: WARNING: options in `/home/user2/.gnupg/gpg.conf' are not yet active during this run
gpg: keyring `/home/user2/.gnupg/secring.gpg' created
gpg: keyring `/home/user2/.gnupg/pubring.gpg' created
gpg: /home/user2/.gnupg/trustdb.gpg: trustdb created
gpg: key 6D187851: public key "RealUser1 (created by user1) <user1@localhost>" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
```

سپس یک فایل نمونه برای رمزگذاری ایجاد کنید:

```
user2@ubuntu16-1:~$ vim user2file 
user2@ubuntu16-1:~$ cat user2file 
water boils at 100 degrees celsius!
```

همه چیز برای رمزگذاری فایل user2 آماده است:

```
### لیست کلیدها
user2@ubuntu16-1:~$ gpg --list-key
/home/user2/.gnupg/pubring.gpg
------------------------------
pub   2048R/6D187851 2020-03-28
uid                  RealUser1 (created by user1) <user1@localhost>
sub   2048R/F00A0A74 2020-03-28
```

```
### رمزگذاری
user2@ubuntu16-1:~$ gpg --output user2secret --recipient Realuser1 --encrypt user2file
gpg: F00A0A74: There is no assurance this key belongs to the named user

pub  2048R/F00A0A74 2020-03-28 RealUser1 (created by user1) <user1@localhost>
 Primary key fingerprint: 8772 5C1E 3F2F 88DB DBB7  7F37 E06C 3317 6D18 7851
      Subkey fingerprint: 28DD 00C2 443C EA3B CD1A  9D5F 901E 5A02 F00A 0A74

It is NOT certain that the key belongs to the person named
in the user ID.  If you *really* know what you are doing,
you may answer the next question with yes.

Use this key anyway? (y/N) y
```

```
### نگاهی به داخل فایل رمزگذاری شده بیندازید!
user2@ubuntu16-1:~$ cat user2secret 

  Z

t}D?YKy
               ƸTB$+!Mz(s}jc_#,$uz]KKܮHgm˚o^;JIvI &(WY'YMm,*F)-$vjU򳶜mM(XrI+Hko%k^Lߍ5h7s[N.2 ofed Ż[Dӿ'^fⳐKn{AS&#,̊ښ}5!01e;Ēhj1JktVY6kH߳rt-nA
      ?gzȬ#j1)]
(
  L1EyCJĬcуkQP
                             I2y_8Guser2@ubuntu16-1:~$ 
```

زمان ارسال فایل محرمانه user2 به user1 فرا رسیده است:

```
user2@ubuntu16-1:~$ mv user2secret /tmp/
```

به عنوان user1 وارد شوید و سعی کنید فایل محرمانه user2 را رمزگشایی کنید:

```
user1@ubuntu16-1:~$ gpg --out from_user2 --decrypt /tmp/user2secret 

You need a passphrase to unlock the secret key for
user: "RealUser1 (created by user1) <user1@localhost>"
2048-bit RSA key, ID F00A0A74, created 2020-03-28 (main key ID 6D187851)

gpg: encrypted with 2048-bit RSA key, ID F00A0A74, created 2020-03-28
      "RealUser1 (created by user1) <user1@localhost>"
```

بیایید ببینیم اطلاعات محرمانه user2 چیست؟

```
user1@ubuntu16-1:~$ cat from_user2 
water boils at 100 degrees celsius!
```

### ایجاد یک گواهی ابطال (Revocation Certificate)

اگر کلید خصوصی شما برای دیگران فاش شود، باید کلیدهای قدیمی را از هویت خود جدا کنید تا بتوانید کلیدهای جدیدی ایجاد نمایید. برای این کار به یک گواهی ابطال نیاز دارید. ما اکنون این کار را انجام می‌دهیم و آن را در جایی امن ذخیره می‌کنیم.

```
user1@ubuntu16-1:~$ gpg --output revoke_User1.crt --gen-revoke user1@localhost

sec  2048R/6D187851 2020-03-28 RealUser1 (created by user1) <user1@localhost>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 0
Enter an optional description; end it with an empty line:
> 
Reason for revocation: No reason specified
(No description given)
Is this okay? (y/N) y

You need a passphrase to unlock the secret key for
user: "RealUser1 (created by user1) <user1@localhost>"
2048-bit RSA key, ID 6D187851, created 2020-03-28

ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!
```

بعد از گزینه `--output` باید نام فایل گواهی که می‌خواهید ایجاد کنید بیاید. گزینه `--gen-revoke` باعث می‌شود `gpg` یک گواهی ابطال ایجاد کند. شما باید آدرس ایمیلی را که هنگام ایجاد کلیدها استفاده کرده‌اید، ارائه دهید.

```
user1@ubuntu16-1:~$ cat revoke_User1.crt 
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1
Comment: A revocation certificate should follow

iQEfBCABAgAJBQJef1iqAh0AAAoJEOBsMxdtGHhRuU0H/RHrxodSkniRE+iD15PW
dfcWcPZVAv9m2SWzmPN0vU/ywgUE5G60pNsOC6bxWbtmxbxm/PutCA+ipC/KPTcZ
RwZvU8ge0PLulrQ5km9xea2295b2dOBEPCzOkgv6BTeAMADrBmi2shhLqUWeoRRr
7H6oTC6RFr76o1jPb9UUMVDh840ttMlpYPAlir6JqCvwSL2ZJE58vWSuC/rou1ds
4Z1Q0EFjpMQ5S1TmukVF4h4xxGsiZpgoubKJb++1CtYfqoPgCV/yiQFjaSGdiQbL
0uqjfEERjOUUc5FKAY9fmvfAjmRzUBhit1U9wWiQ7O+JBxzMUDda3eas5OzpweeK
0Lw=
=z2hv
-----END PGP PUBLIC KEY BLOCK-----
```

## امضا کردن (Signing) 

 با رمزگذاری یک سند با استفاده از کلید خصوصی خود، به همه اجازه می‌دهید سعی کنند آن را با استفاده از کلید عمومی شما باز کنند و اگر موفق شوند، مطمئن خواهند شد که شما آن را با کلید خصوصی خودتان امضا کرده‌اید! `gpg` دستور خاصی برای امضای اسناد دارد:

```
user1@ubuntu16-1:~$ vim notice
user1@ubuntu16-1:~$ cat notice 
Lets start learning LPIC-2!
user1@ubuntu16-1:~$ 
user1@ubuntu16-1:~$ gpg --clearsign notice

You need a passphrase to unlock the secret key for
user: "RealUser1 (created by user1) <user1@localhost>"
2048-bit RSA key, ID 6D187851, created 2020-03-28
```

در اینجا گزینه `--clearsign` به `gpg` می‌گوید که پیام متنی ساده را نیز در فایل خروجی قرار دهد. فایل خروجی با پسوند `.asc` ایجاد خواهد شد:

```
user1@ubuntu16-1:~$ cat notice.asc 
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA1

Lets start learning LPIC-2!
-----BEGIN PGP SIGNATURE-----
Version: GnuPG v1

iQEcBAEBAgAGBQJef2DyAAoJEOBsMxdtGHhReQoH/j33csnEJUlS6IqdlzeIDyw3
D6HVli8TaywfdZLTCXhSRHNM7NISCIsPdULHVs1J8vwYKLqshNOlx7F+HNAmkYtv
WqHlNL03x2TOEca+qr31iUFdtpRKrMsQ3mpai2aaI8MoILO1gRYGMZ717O31YGGh
yQ5Teft6q2QorWXpbACN0JlWcKs5OiFCgcCOOp2RHnjM4NtCJEmI+ZGBuqVJL98P
Aa/fP5AteexMuj4GwRfC542f9Tsaw03je4SlAcgq7cJ+iwpy5vHGwN0sPwbRoqbS
ABL1zaTA2TZmnS5Jx5ii8IyndObuVHtDUEGROaqxiVWZ/JrMAAfAjtv/mnL79HQ=
=W+EU
-----END PGP SIGNATURE-----
```

فایل `notice.asc` را به /tmp کپی کنید؛ سایر کاربران می‌توانند بررسی کنند که آیا سند به درستی امضا شده است یا خیر:

```
user2@ubuntu16-1:~$ gpg --verify /tmp/notice.asc 
gpg: Signature made Sat 28 Mar 2020 07:06:34 PM +0430 using RSA key ID 6D187851
gpg: Good signature from "RealUser1 (created by user1) <user1@localhost>"
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 8772 5C1E 3F2F 88DB DBB7  7F37 E06C 3317 6D18 7851
user2@ubuntu16-1:~$ 
```

و تمام!

## تبریک می‌گوییم، ما بخش LPIC-1 102 را تمام کردیم!!! فراموش نکنید که [ستاره](https://github.com/Borosan) بدهید و [حمایت مالی](http://linuxcert.ir/donation.html) کنید. :-)

## می‌توانید مطالعه کتاب LPIC-2 من را شروع کنید: [https://borosan.gitbook.io/lpic2-exam-guide/](https://borosan.gitbook.io/lpic2-exam-guide/)

.

.

.

[https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences](https://www.ssl2buy.com/wiki/symmetric-vs-asymmetric-encryption-what-are-differences)

[https://hackernoon.com/symmetric-and-asymmetric-encryption-5122f9ec65b1](https://hackernoon.com/symmetric-and-asymmetric-encryption-5122f9ec65b1)

[https://economictimes.indiatimes.com/definition/decryption](https://economictimes.indiatimes.com/definition/decryption)

[https://support.microsoft.com/en-us/help/246071/description-of-symmetric-and-asymmetric-encryption](https://support.microsoft.com/en-us/help/246071/description-of-symmetric-and-asymmetric-encryption)

[https://stackoverflow.com/questions/454048/what-is-the-difference-between-encrypting-and-signing-in-asymmetric-encryption](https://stackoverflow.com/questions/454048/what-is-the-difference-between-encrypting-and-signing-in-asymmetric-encryption)

[https://en.wikipedia.org/wiki/Key\_server](https://en.wikipedia.org/wiki/Key\_server)

[https://www.ssh.com/ssh/protocol](https://www.ssh.com/ssh/protocol)

[https://www.hivelocity.net/kb/what-is-openssh/](https://www.hivelocity.net/kb/what-is-openssh/)

[https://www.ssh.com/ssh/agent](https://www.ssh.com/ssh/agent)

[https://www.ssh.com/ssh/tunneling](https://www.ssh.com/ssh/tunneling)

[https://www.ssh.com/ssh/tunneling/example](https://www.ssh.com/ssh/tunneling/example)

[https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot](https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot)

[https://www.ssh.com/ssh/command](https://www.ssh.com/ssh/command)

[https://www.taoeffect.com/espionage/EspionageHelp/pages/faq-encryption.html](https://www.taoeffect.com/espionage/EspionageHelp/pages/faq-encryption.html)

[https://www.privex.io/articles/what-is-gpg/](https://www.privex.io/articles/what-is-gpg/)

[https://www.howtogeek.com/427982/how-to-encrypt-and-decrypt-files-with-gpg-on-linux/](https://www.howtogeek.com/427982/how-to-encrypt-and-decrypt-files-with-gpg-on-linux/)

[https://jadi.gitbooks.io/lpic1/content/1103\_securing\_data\_with\_encryption.html](https://jadi.gitbooks.io/lpic1/content/1103\_securing\_data\_with\_encryption.html)

.
