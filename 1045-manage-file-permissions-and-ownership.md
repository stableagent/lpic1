# 104.5. مدیریت دسترسی‌ها (permissions) و مالکیت (ownership) فایل

**وزن:** ۳

**توضیحات:** داوطلبان باید قادر به کنترل دسترسی فایل از طریق استفاده صحیح از مجوزها (permissions) و مالکیت‌ها (ownerships) باشند.

**حوزه‌های کلیدی دانش:**

* مدیریت مجوزهای دسترسی روی فایل‌های معمولی و خاص و همچنین دایرکتوری‌ها
* استفاده از حالت‌های دسترسی مانند suid، sgid و sticky bit برای حفظ امنیت
* دانستن نحوه تغییر ماسک ایجاد فایل (file creation mask)
* استفاده از فیلد گروه برای اعطای دسترسی فایل به اعضای گروه

**اصطلاحات و ابزارهای کاربردی:**

* chmod
* umask
* chown
* chgrp

####  <a href="users-groups-and-file-ownership" id="users-groups-and-file-ownership"></a>

#### کاربران، گروه‌ها و مالکیت فایل (Users, groups and file ownership) <a href="users-groups-and-file-ownership" id="users-groups-and-file-ownership"></a>

تا اینجا می‌دانید که Linux یک سیستم چندکاربره (multiuser) است و هر کاربر به یک گروه اصلی (primary group) و احتمالاً چند گروه اضافی (additional groups) تعلق دارد. همچنین می‌توان با یک کاربر وارد سیستم شد و با استفاده از دستور `su` به کاربر دیگری تبدیل شد. مالکیت فایل‌ها در Linux و سطح دسترسی (access authority) ارتباط نزدیکی با user idها و groupها دارد.

### کاربر و گروه‌ها (User and groups) <a href="user-and-groups" id="user-and-groups"></a>

برای شروع، بیایید با چند دستور، بعضی اطلاعات پایه درباره user و group را مرور کنیم.

* whoami: نام کاربریِ کاربر جاری را نمایش می‌دهد (ubuntu16.04)

```
user1@ubuntu16-1:~/sandbox$ whoami
user1
user1@ubuntu16-1:~/sandbox$ su -
Password: 
root@ubuntu16-1:~# whoami
root
```

* groups: با استفاده از دستور `groups` می‌توانیم بفهمیم عضو چه گروه‌هایی هستیم.

```
root@ubuntu16-1:~# groups
root
root@ubuntu16-1:~# exit
logout
user1@ubuntu16-1:~/sandbox$ groups
user1
```

* id: با استفاده از دستور `id` می‌توانیم هم اطلاعات user و هم اطلاعات group را ببینیم.

```
user1@ubuntu16-1:~/sandbox$ id
uid=1001(user1) gid=1001(user1) groups=1001(user1)
user1@ubuntu16-1:~/sandbox$ su -
Password: 
root@ubuntu16-1:~# id
uid=0(root) gid=0(root) groups=0(root)
```

> این دستور می‌تواند شناسه‌های عددی (UID یا group ID) کاربر جاری یا هر کاربر دیگری روی سرور را نمایش دهد.

{% hint style="info" %}
اطلاعات کاربران و گروه‌ها (به همراه اطلاعات دیگر) در فایل‌های `/etc/passwd` و `/etc/group` ذخیره می‌شوند.

```
root@ubuntu16-1:~# cat /etc/passwd | grep user1
user1:x:1001:1001::/home/user1:
root@ubuntu16-1:~# cat /etc/group | grep user1
user1:x:1001:
```
{% endhint %}

#### مالکیت فایل و مجوزها (File ownership and permissions)

هر فایل روی یک سیستم Linux یک owner و یک group دارد.

برای نمایش owner و group می‌توانیم از `ls -l` استفاده کنیم.

```
user1@ubuntu16-1:~/sandbox$ ls -l
total 4
drwxrwxr-x 2 user1 user1 4096 Jan 27 21:49 dir1
-rw-rw-r-- 1 user1 user1    0 Jan 27 21:49 file1
```

> همانطور که می‌بینید، فایل file1 متعلق به user1 و گروهی به نام user1 است.

اولین کاراکتر در خروجیِ long listing نوع object را مشخص می‌کند. `-` برای فایل معمولی (regular file)، `d` برای دایرکتوری و `l` برای symbolic link (بعداً آن‌ها را می‌بینیم).

![](.gitbook/assets/permis-filepermis.jpg)

مجوزها (permissions) جداگانه برای owner فایل، اعضای group فایل و سایر کاربران (everyone else) تعریف می‌شوند.

مدل مجوزدهی Linux برای هر object در filesystem سه نوع مجوز دارد.

این سه مجوز عبارت‌اند از: read (r)، write (w) و execute (x). مجوز write شامل امکان تغییر دادن یا حذف کردن یک object هم می‌شود. علامت `-` یعنی مجوز متناظر داده نشده است. مثال:

```
root@ubuntu16-1:~# ls -l /sbin/fsck
-rwxr-xr-x 1 root root 44184 May 16  2018 /sbin/fsck
```

همانطور که می‌بینید، fsck توسط owner آن (root) و اعضای گروه root قابل خواندن/نوشتن/اجرا است، اما دیگران فقط می‌توانند آن را بخوانند و اجرا کنند (احتمالاً با نتایج محدود).

#### دایرکتوری‌ها (Directories) <a href="directories" id="directories"></a>

![](.gitbook/assets/permis-dirpermis.jpg)

دایرکتوری‌ها همان پرچم‌های مجوز (permission flags) را مثل فایل‌های معمولی دارند، اما تفسیرشان متفاوت است.

* مجوز read برای یک دایرکتوری اجازه می‌دهد کاربر، محتویات آن دایرکتوری را لیست کند.
* مجوز write یعنی کاربر می‌تواند داخل دایرکتوری فایل بسازد یا فایل‌ها را حذف کند.
* مجوز execute اجازه می‌دهد کاربر وارد دایرکتوری شود و به زیردایرکتوری‌ها دسترسی داشته باشد.

اگر روی یک دایرکتوری مجوز execute نداشته باشید، objectهای داخل آن دایرکتوری قابل دسترسی نیستند. اگر مجوز read نداشته باشید، objectهای داخل دایرکتوری در لیست کردن قابل مشاهده نیستند؛ اما اگر مسیر کامل را بدانید، ممکن است همچنان بتوان به آن‌ها دسترسی داشت. مثال:

```
root@ubuntu16-1:~# ls -l /home/
total 8
drwxr-xr-x 22 payam payam 4096 Oct 27  2018 payam
drwxr-xr-x 19 user1 user1 4096 Jan 27 21:49 user1
```

اولین کاراکتر نشان می‌دهد این یک دایرکتوری است. owner (یعنی user1) روی آن read/write/execute دارد؛ اما اعضای گروه user1 و سایر کاربران فقط read و execute دارند (همانطور که گفتیم، execute باعث می‌شود بتوانند داخل دایرکتوری را ببینند/به آن دسترسی داشته باشند).

#### تغییر مجوزها (Changing permissions)

### chmod

دستوری که برای تغییر مجوز فایل‌ها استفاده می‌کنیم `chmod` است که مخفف “change mode” است. دو روش اصلی برای بیان تغییرات به این دستور داریم:

* استفاده از نمادها/حروف (symbolic یا short codes)
* استفاده از اعداد اکتال (octal codes)

**۱- استفاده از نمادها (short codes):** معمولاً روش ساده‌تر است.

![](.gitbook/assets/permis-chmodshortcodes.jpg)

```
chmod [reference][operator][mode] file... 
```

reference می‌تواند یکی از موارد زیر باشد:

* `u` به معنی user (owner فایل)
* `g` به معنی group (کاربرانی که عضو گروه فایل هستند)
* `o` به معنی others (کاربرانی که owner فایل نیستند / عضو گروه فایل هم نیستند)
* `a` به معنی all (هر سه مورد بالا، همان ugo)

operator می‌تواند یکی از موارد زیر باشد:

* `+` افزودن مجوزهای مشخص‌شده به کلاس‌های مشخص‌شده
* `-` حذف مجوزهای مشخص‌شده از کلاس‌های مشخص‌شده
* `=` دقیقاً همان مجوزهای مشخص‌شده را برای کلاس‌های مشخص‌شده تنظیم می‌کند

و modeها هم معمولاً این‌ها هستند:

* `r`: اجازه خواندن فایل
* `w`: اجازه نوشتن (یا حذف کردن) فایل
* `x`: اجازه اجرای فایل؛ یا در مورد دایرکتوری، اجازه search/ورود

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rw-r-- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod u+x file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwxrw-r-- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod o-r file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwxrw---- 1 user1 user1    0 Jan 27 21:49 file1
```

> اگر بخواهیم برای user، group و other مجوزهای متفاوت تعیین کنیم، می‌توانیم چند expression را با کاما جدا کنیم؛ مثلاً `ug=rwx,o=rx`.

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwxrw---- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod u-x,g+x,o+r file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rwxr-- 1 user1 user1    0 Jan 27 21:49 file1
```

> استفاده از `a` به جای ugo همراه با عملگر `=` معمولاً تنظیم دقیق mode را راحت‌تر می‌کند.

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rwxr-- 1 user1 user1    0 Jan 27 21:49 file1
user1@ubuntu16-1:~/sandbox$ chmod a=rw file1
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rw-rw- 1 user1 user1    0 Jan 27 21:49 file1
```

**۲- استفاده از اکتال (octal codes):** تا اینجا از نمادها (ugoa و rwx) برای تعیین مجوزها استفاده کردیم. اما می‌توانیم به جای نمادها از اعداد اکتال استفاده کنیم.

![](.gitbook/assets/permis-chmodoctalcodes.jpg)

برای استفاده از octal codeها در chmod باید یک رشته اکتال بسازیم که چیزی نیست جز جمع ساده اعداد.

| نمادین | توضیح | اکتال |
| -------- | ----- | ----- |
| rwx      | 4+2+1 | 7     |
| rw-      | 4+2   | 6     |
| r-x      | 4+1   | 5     |
| r--      | 4     | 4     |
| -wx      | 2+1   | 3     |
| -w-      | 2     | 2     |
| --x      | 1     | 1     |
| ---      | 0     | 0     |

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rw-rw- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod 700 file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwx------ 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod 655 file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-r-xr-x 1 user1 user1    0 Jan 27 21:49 file1
```

برای تغییر مجوزها به صورت بازگشتی (recursive) روی دایرکتوری‌ها و فایل‌ها از گزینه `-R` استفاده کنید:

```
user1@ubuntu16-1:~/sandbox$ chmor -R o+r dir1
```

#### حالت‌های دسترسی ویژه (Access modes)

وقتی login می‌کنیم، فرآیند شل جدید با user ID و group ID ما اجرا می‌شود. معمولاً این یعنی نمی‌توانیم به فایل‌های دیگران دسترسی داشته باشیم و نمی‌توانیم روی فایل‌های سیستمی بنویسیم. از طرف دیگر، کاربران برای انجام عملیات‌ها کاملاً به برنامه‌های دیگر وابسته‌اند.

یک مثال مهم فایل `/etc/passwd` است که کاربران معمولی نمی‌توانند مستقیم آن را تغییر دهند، چون مجوز write فقط برای root فعال است. اما کاربران معمولی به شکلی باید بتوانند `/etc/passwd` را تغییر دهند (مثلاً برای تغییر رمز عبور):

```
root@ubuntu16-1:~# which passwd
/usr/bin/passwd
root@ubuntu16-1:~# ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 54256 May 16  2017 /usr/bin/passwd
```

پس اگر کاربر نمی‌تواند این فایل را مستقیماً تغییر دهد، چطور این کار انجام می‌شود؟ این حرف "s" چیست؟

### suid , guid

مدل مجوزدهی Linux دو حالت دسترسی ویژه به نام suid (set user id) و sgid (set group id) دارد. وقتی یک برنامه اجرایی (executable) حالت suid داشته باشد، طوری اجرا می‌شود که انگار توسط owner فایل اجرا شده است، نه کاربری که واقعاً آن را اجرا کرده است. به همین ترتیب، وقتی حالت sgid فعال باشد، برنامه طوری اجرا می‌شود که انگار کاربر اجراکننده عضو group فایل است، نه گروه خودش.

> #### دایرکتوری‌ها و sgid <a href="directories-and-sgid" id="directories-and-sgid"></a>
> 
> وقتی یک دایرکتوری حالت sgid داشته باشد، هر فایل یا دایرکتوری‌ای که داخل آن ساخته شود group ID آن دایرکتوری را به ارث می‌برد. این موضوع برای درخت‌دایرکتوری‌هایی که توسط یک گروه از افراد روی یک پروژه مشترک استفاده می‌شود بسیار مفید است.

### sticky bit

دیدیم که هر کسی که روی یک دایرکتوری مجوز write داشته باشد می‌تواند فایل‌های داخلش را حذف کند. این موضوع برای یک پروژه گروهی ممکن است قابل قبول باشد، اما برای فضای اشتراکی عمومی مثل دایرکتوری `/tmp` مطلوب نیست. خوشبختانه راه‌حل وجود دارد: _sticky bit_.

اگر sticky bit روی یک دایرکتوری تنظیم شود، فقط owner فایل یا superuser (root) اجازه دارد فایل را delete یا unlink کند.

خب، بیایید آنچه یاد گرفتیم را جمع‌بندی کنیم:

| access mode    | **روی فایل** | **روی دایرکتوری** |
| -------------- | --------------------------------------- | -------------------------------------------- |
| **SUID**       | با مجوزهای owner فایل اجرا می‌شود | هیچ |
| **GUID**       | با مجوزهای group اجرا می‌شود | فایل‌های جدید group دایرکتوری را می‌گیرند |
| **Sticky Bit** | هیچ | فقط owner می‌تواند فایل‌ها را حذف کند |

#### suid، guid و stickybit چگونه پیاده‌سازی می‌شوند؟

چون دیگر جایی برای تنظیم access modeها باقی نمانده است، از کاراکتر مربوط به execute استفاده می‌شود. حرف `s` برای هر دو suid و guid استفاده می‌شود و حرف `t` برای stickybit. باز هم برای افزودن و حذف مجوزها از `+/-` استفاده می‌کنیم.

![](.gitbook/assets/permis-accessmodes.jpg)

> احتمالاً متوجه شدید اگر فایل یا دایرکتوری از قبل executable باشد، بعد از تنظیم access modeها حروف **s** و **t** نمایش داده می‌شوند.
>
> اما اگر فایل یا دایرکتوری قبل از تنظیم access mode executable نبوده باشد، حروف **S** و **T** دیده می‌شوند.

به عنوان مثال برای suid، دستور `ping` را در نظر بگیرید. چون ping باید به کارت شبکه دسترسی داشته باشد، به مجوزهای root نیاز دارد، اما یک کاربر معمولی هم می‌تواند از آن استفاده کند:

```
user1@ubuntu16-1:~/sandbox$ which ping
/bin/ping
user1@ubuntu16-1:~/sandbox$ ls -l /bin/ping
-rwsr-xr-x 1 root root 44168 May  7  2014 /bin/ping
user1@ubuntu16-1:~/sandbox$ ping 8.8.8.8 -c2
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=128 time=39.9 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=128 time=39.7 ms

--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 39.793/39.862/39.932/0.211 ms
```

حالا تلاش می‌کنیم guid را روی یک دایرکتوری تنظیم کنیم و با یک کاربر دیگر داخل آن فایل بسازیم:

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir1
drwxrwxrwx 2 user1 user1 4096 Jan 27 21:49 dir1
user1@ubuntu16-1:~/sandbox$ chmod g+s dir1
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir1
drwxrwsrwx 2 user1 user1 4096 Jan 27 21:49 dir1
```

```
user2@ubuntu16-1:~$ whoami
user2
user2@ubuntu16-1:~$ cd /home/user1/sandbox/dir1/
user2@ubuntu16-1:/home/user1/sandbox/dir1$ touch NewFileUser2
user2@ubuntu16-1:/home/user1/sandbox/dir1$ ls -l
total 0
-rw-rw-r-- 1 user2 user1 0 Jan 29 02:02 NewFileUser2
```

و در نهایت بیایید ببینیم stickybit روی `/tmp` چطور کار می‌کند:

```
user1@ubuntu16-1:/$ cd /
user1@ubuntu16-1:/$ ls -l | grep tmp
drwxrwxrwt  22 root  root   4096 Jan 29 02:03 tmp
user1@ubuntu16-1:/$ cd tmp/
user1@ubuntu16-1:/tmp$ touch NewFileUser1
user1@ubuntu16-1:/tmp$ ls -l | grep -i newfile
-rw-rw-r-- 1 user1 user1    0 Jan 29 02:04 NewFileUser1
```

```
user2@ubuntu16-1:~$ whoami
user2
user2@ubuntu16-1:~$ cd /tmp/
user2@ubuntu16-1:/tmp$ rm NewFileUser1 
rm: remove write-protected regular empty file 'NewFileUser1'? y
rm: cannot remove 'NewFileUser1': Operation not permitted
```

#### تنظیم Access Modeها با استفاده از octal codeها

می‌توانیم از octal codeها برای تنظیم suid، guid و stickybit هم استفاده کنیم:

| Access Mode   | octal |
| ------------- | ----- |
| **SUID**      | 4000  |
| **GUID**      | 2000  |
| **StickyBit** | 1000  |

و دوباره می‌توانیم از جمع ارقام استفاده کنیم.

```
###SUID
user1@ubuntu16-1:~/sandbox$ touch file2
user1@ubuntu16-1:~/sandbox$ ls -l | grep file2
-rw-rw-r-- 1 user1 user1    0 Jan 29 02:32 file2
user1@ubuntu16-1:~/sandbox$ chmod 4470 file2
user1@ubuntu16-1:~/sandbox$ ls -l | grep file2
-r-Srwx--- 1 user1 user1    0 Jan 29 02:32 file2

###GUID
user1@ubuntu16-1:~/sandbox$ mkdir dir2
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir2
drwxrwxr-x 2 user1 user1 4096 Jan 29 02:33 dir2
user1@ubuntu16-1:~/sandbox$ chmod 2770 dir2
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir2
drwxrws--- 2 user1 user1 4096 Jan 29 02:33 dir2

###StickyBit
user1@ubuntu16-1:~/sandbox$ chmod 1770 dir2
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir2
drwxrws--T 2 user1 user1 4096 Jan 29 02:33 dir2
```

### umask

وقتی یک فایل یا دایرکتوری جدید ایجاد می‌شود، فرآیند ایجاد، مجوزهایی را که آن فایل/دایرکتوری باید داشته باشد مشخص می‌کند. این مجوزها از کجا می‌آیند؟ از umask.

می‌توانیم مقدار umask را با دستور `umask` ببینیم:

```
root@ubuntu16-1:~# umask
0022
```

#### umask چگونه کار می‌کند؟

![](.gitbook/assets/permis-umask.jpg)

وقتی یک فایل جدید ایجاد می‌شود، فرآیند ایجاد، مجوزهایی را که فایل باید داشته باشد مشخص می‌کند. اغلب mode درخواستی 0666 است که فایل را برای همه قابل خواندن و قابل نوشتن می‌کند (اما executable نیست). برای دایرکتوری‌ها معمولاً مقدار پیش‌فرض 0777 است. با این حال این حالتِ «باز» تحت تأثیر مقدار _umask_ قرار می‌گیرد؛ umask مشخص می‌کند کاربر چه مجوزهایی را **نمی‌خواهد** به صورت خودکار به فایل‌ها یا دایرکتوری‌های تازه‌ساخته‌شده بدهد. سیستم از مقدار umask برای کاهش مجوزهای درخواستی اولیه استفاده می‌کند.

```
user1@ubuntu16-1:~/sandbox$ umask
0002

user1@ubuntu16-1:~/sandbox$ touch newfile

user1@ubuntu16-1:~/sandbox$ ls -l | grep newfile
-rw-rw-r-- 1 user1 user1    0 Jan 28 05:44 newfile

user1@ubuntu16-1:~/sandbox$ mkdir newdir
user1@ubuntu16-1:~/sandbox$ ls -l | grep newdir
drwxrwxr-x 2 user1 user1 4096 Jan 28 05:45 newdir
```

معمولاً umask به صورت system-wide تنظیم می‌شود (هرچند می‌تواند per-user هم تنظیم شود) و بسته به توزیع Linux معمولاً پیکربندی آن را در یکی از این مسیرها پیدا می‌کنید:

> * /etc/profile (معمولاً)
> * /etc/bashrc (معمولاً)
> * /etc/logindefs (ubuntu)

چون اینجا از ubuntu استفاده می‌کنیم، نگاهی به `/etc/logindefs` می‌اندازیم:

```
# If USERGROUPS_ENAB is set to "yes", that will modify this UMASK default value
# for private user groups, i. e. the uid is the same as gid, and username is
# the same as the primary group name: for these, the user permissions will be
# used as group permissions, e. g. 022 will become 002.
#
# Prefix these values with "0" to get octal, "0x" to get hexadecimal.
#
ERASECHAR       0177
KILLCHAR        025
UMASK           022
```

در اینجا گفته می‌شود اگر `USERGROUPS_ENAB` تنظیم باشد، umask برابر 002 خواهد شد. بیایید بررسی کنیم:

```
root@ubuntu16-1:~# cat /etc/login.defs | grep -i USERGROUPS_ENAB
# If USERGROUPS_ENAB is set to "yes", that will modify this UMASK default value
USERGROUPS_ENAB yes
```

به همین دلیل umask در سیستم ما 002 است.

#### تنظیم owner و group فایل

همه فایل‌ها در Linux یک owner و یک group دارند. می‌توانیم owner را با دستور `chown` و group را با دستور `chgrp` تنظیم کنیم.

### chown

کاربر root می‌تواند با استفاده از دستور `chown` مالکیت یک فایل را تغییر دهد. می‌توانیم از user name یا user ID استفاده کنیم.

```
chown [OPTION]… [OWNER][:[GROUP]] FILE…
```

همزمان با owner، می‌توان group فایل را هم تغییر داد؛ کافی است بعد از user name یا ID یک colon و سپس group name یا ID را اضافه کنیم.

```
root@ubuntu16-1:~/sandbox# touch file1
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 root root 0 Jan 29 03:00 file1
root@ubuntu16-1:~/sandbox# chown user1 file1 
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 root 0 Jan 29 03:00 file1
```

اگر فقط colon بدهیم، از گروه پیش‌فرض (default group) کاربر استفاده می‌شود:

```
root@ubuntu16-1:~/sandbox# chown user1: file1
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 user1 0 Jan 29 03:00 file1
```

گزینه `-R` تغییر را به صورت recursive اعمال می‌کند و `-c` زمانی که تغییری روی فایل اعمال شود گزارش می‌دهد. همچنین می‌توان از مالکیت یک فایل دیگر به عنوان مرجع با گزینه `--reference` استفاده کرد.

### chgrp

دستور `chgrp` در Linux برای تغییر group ownership یک فایل یا دایرکتوری استفاده می‌شود.

```
chgrp [OPTION]… GROUP FILE…
```

**نکته ۱:** برای اضافه/حذف گروه‌ها باید مجوز administrator داشته باشیم.

```
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 user1 0 Jan 29 03:00 file1
root@ubuntu16-1:~/sandbox# chgrp user2 file1
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 user2 0 Jan 29 03:00 file1
root@ubuntu16-1:~/sandbox# chgrp root file1
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 root 0 Jan 29 03:00 file1
```

**نکته ۲:** owner فایل همیشه می‌تواند group فایل/دایرکتوری خودش را به گروه اصلی خودش یا یکی از گروه‌هایی که عضو آن است تغییر دهد.

```
root@ubuntu16-1:~# cd ~user1/

root@ubuntu16-1:/home/user1# cd sandbox/

root@ubuntu16-1:/home/user1/sandbox# ls -l
total 0
-rw-r--r-- 1 root root 0 Jan 29 05:21 file1

root@ubuntu16-1:/home/user1/sandbox# chown user1 file1 
```

```
user1@ubuntu16-1:~/sandbox$ whoami
user1

user1@ubuntu16-1:~/sandbox$ id
uid=1001(user1) gid=1001(user1) groups=1001(user1)

user1@ubuntu16-1:~/sandbox$ ls -l
total 0
-rw-r--r-- 1 user1 root 0 Jan 29 05:21 file1

user1@ubuntu16-1:~/sandbox$ chgrp user1 file1

user1@ubuntu16-1:~/sandbox$ ls -l
total 0
-rw-r--r-- 1 user1 user1 0 Jan 29 05:21 file1
```

مثل بسیاری از دستورهایی که در این آموزش پوشش داده شده‌اند، `chgrp` هم گزینه `-R` دارد تا تغییرات به صورت recursive روی همه فایل‌ها و زیردایرکتوری‌های انتخاب‌شده اعمال شود.

`--reference` از groupname یک فایل مرجع برای تغییر group فایل یا فولدر دیگر استفاده می‌کند.

.

.

.

{% hint style="success" %}
**Primary و secondary groupها**

در واقع دو نوع گروه داریم: **primary** و **secondary**.

**primary group** گروهی است که در فایل `/etc/passwd` ثبت می‌شود و هنگام ساخت اکانت تنظیم می‌گردد. وقتی یک کاربر یک فایل ایجاد می‌کند، group مرتبط با آن فایل همان primary group اوست.

**secondary group** گروه‌هایی هستند که کاربران بعد از ایجاد حساب می‌توانند عضو آن‌ها شوند. عضویت در secondary groupها در فایل `/etc/group` دیده می‌شود.

کاربر می‌تواند با دستور `newgrp`، **primary group** (یا **default group**) خود را تغییر دهد و بعد از آن همه فایل‌ها/دایرکتوری‌هایی که می‌سازد، آن group را خواهند داشت.
{% endhint %}

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-5/](https://developer.ibm.com/tutorials/l-lpic1-104-5/)

[https://jadi.gitbooks.io/lpic1/content/1045\_manage_file_permissions_and_ownership.html](https://jadi.gitbooks.io/lpic1/content/1045\_manage_file_permissions_and_ownership.html)

[https://www.geeksforgeeks.org/chmod-command-linux/](https://www.geeksforgeeks.org/chmod-command-linux/)

[https://www.geeksforgeeks.org/permissions-in-linux/](https://www.geeksforgeeks.org/permissions-in-linux/)

[https://www.geeksforgeeks.org/chown-command-in-linux-with-examples/](https://www.geeksforgeeks.org/chown-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/chgrp-command-in-linux-with-examples/](https://www.geeksforgeeks.org/chgrp-command-in-linux-with-examples/)

[https://www.networkworld.com/article/3409781/mastering-user-groups-on-linux.html](https://www.networkworld.com/article/3409781/mastering-user-groups-on-linux.html)

.

.

.
