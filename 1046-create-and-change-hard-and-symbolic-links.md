# 104.6. ایجاد و تغییر hard links و symbolic links

**وزن:** ۲

**توضیحات:** داوطلبان باید قادر به ایجاد و مدیریت لینک‌های سخت (hard links) و نمادین (symbolic links) به یک فایل باشند.

**حوزه‌های کلیدی دانش:**

* ایجاد لینک‌ها
* شناسایی لینک‌های سخت و/یا نرم (soft links)
* کپی کردن در مقابل لینک کردن فایل‌ها
* استفاده از لینک‌ها برای پشتیبانی از وظایف مدیریت سیستم

**اصطلاحات و ابزارهای کاربردی:**

* ln
* ls

### معرفی لینک‌ها (Introducing links) <a href="introducing-links" id="introducing-links"></a>

روی یک storage device، یک فایل یا دایرکتوری در مجموعه‌ای از blockها ذخیره می‌شود. اطلاعات مربوط به یک فایل در یک _inode_ نگهداری می‌شود؛ inode اطلاعاتی مثل مالک فایل، آخرین زمان دسترسی، اندازه فایل، اینکه آیا فایل یک دایرکتوری هست یا نه، و اینکه چه کسی می‌تواند از آن بخواند یا روی آن بنویسد را ثبت می‌کند.

یک _directory entry_ شامل نام یک فایل یا دایرکتوری و یک اشاره‌گر (pointer) به inodeای است که اطلاعات آن فایل یا دایرکتوری را نگهداری می‌کند.

![](.gitbook/assets/links-inodes.jpg)

> شماره inode در داخل یک filesystem مشخص یکتا است.

```
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
```

> گزینه `-i` شماره inode هر فایل را نمایش می‌دهد.

**لینک چیست؟** لینک (link) در ساده‌ترین حالت، یک directory entry اضافی برای یک فایل یا دایرکتوری است که باعث می‌شود یک چیز، دو یا چند نام داشته باشد.

#### ایجاد لینک‌ها

دو نوع لینک داریم: **Hard Link** و **Soft Link**.

یک **hard link** یک directory entry است که به یک inode اشاره می‌کند؛ در حالی که یک **soft link** یا _symbolic link_ یک directory entry است که به inodeای اشاره می‌کند که نامِ یک directory entry دیگر را ارائه می‌دهد. به symbolic linkها همچنین _symlink_ هم گفته می‌شود.

![](.gitbook/assets/link-links.jpg)

> hard linkها به inode اشاره می‌کنند و inodeها فقط داخل یک filesystem مشخص یکتا هستند؛ بنابراین hard linkها نمی‌توانند بین file systemهای مختلف (پارتیشن‌ها یا دیسک‌های متفاوت) عبور کنند.

> hard link را فقط برای فایل‌ها می‌توان ساخت و نه برای دایرکتوری‌ها. تنها استثنا، ورودی‌های ویژه داخل یک دایرکتوری برای خود آن دایرکتوری و والد آن است (`.` و `..`).

### لینک‌های سخت در مقابل لینک‌های نرم (Hard Links vs Soft Links)

| hard link | soft link |
| --- | --- |
| شماره inode یکسان دارند. | شماره inode متفاوت دارند. |
| نمی‌توانند از مرزهای filesystem عبور کنند. | می‌توانند از مرزهای filesystem عبور کنند. |
| نمی‌توانند دایرکتوری‌ها را لینک کنند. | امکان لینک کردن بین دایرکتوری‌ها را می‌دهند. |
| به محتوای واقعی فایل اشاره می‌کنند. | مسیر فایل اصلی را نگه می‌دارند و نه محتوا را. |
| اگر فایل اصلی حذف شود، لینک همچنان محتوا را نشان می‌دهد. | حذف soft link مشکلی ایجاد نمی‌کند؛ اما اگر فایل اصلی حذف شود، لینک به یک لینک «آویزان» (dangling) تبدیل می‌شود که به فایلِ موجود-نیست (nonexistent) اشاره می‌کند. |
| اگر مجوزهای (permissions) فایل اصلی را تغییر دهیم، روی لینک هم اعمال می‌شود. | معمولاً به طور خودکار با تغییر مجوزهای فایل اصلی به‌روزرسانی نمی‌شود. |

#### ایجاد لینک‌ها

### ln

می‌توانیم از دستور `ln` برای ایجاد هم hard link و هم soft link استفاده کنیم:

```
### For  Hard Link 
ln  [original filename] [link name] 

### For  Soft link 
ln  -s [original filename] [link name] 
```

### لینک‌های سخت (Hard Links)

با دستور `ln` می‌توان hard linkهای بیشتری برای یک فایل موجود ساخت (اما نه برای دایرکتوری، هرچند سیستم برای `.` و `..` به شکل hard link عمل می‌کند).

```
root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 1 root root    0 Jan 29 08:14 file1

### see inodes
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1

### creating Hard Link
root@ubuntu16-1:~/sandbox# ln file1 HardLink

### comparing inodes
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
2228291 file2
2228289 HardLink
```

دستور `ls -l` همه لینک‌ها را نشان می‌دهد و ستون «تعداد لینک‌ها» (link count) تعداد لینک‌ها را نمایش می‌دهد:

```
root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 2 root root    0 Jan 29 08:14 file1
-rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink
```

به عدد "2" قبل از file1 توجه کنید؛ قبل از ساخت HardLink مقدار آن "1" بود.

### لینک‌های نرم (Soft Links)

دستور `ln` با گزینه `-s` لینک‌های نرم ایجاد می‌کند. soft linkها از نام فایل یا دایرکتوری استفاده می‌کنند که می‌تواند relative یا absolute باشد. اگر از نام‌های relative استفاده می‌کنید، معمولاً بهتر است current working directory همان دایرکتوری‌ای باشد که در آن لینک را می‌سازید؛ در غیر این صورت لینکی که ایجاد می‌کنید نسبت به نقطه دیگری در filesystem خواهد بود.

```
root@ubuntu16-1:~/sandbox# touch file2

root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 2 root root    0 Jan 29 08:14 file1
-rw-r--r-- 1 root root    0 Jan 29 09:05 file2
-rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink

### see inodes
root@ubuntu16-1:~/sandbox# ls -li
total 4
2228290 drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
2228289 -rw-r--r-- 2 root root    0 Jan 29 08:14 file1
2228291 -rw-r--r-- 1 root root    0 Jan 29 09:05 file2
2228289 -rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink

### creating soft link using relative path
root@ubuntu16-1:~/sandbox# ln -s  file2 SoftLink

### see inodes
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
2228291 file2
2228289 HardLink
2228292 SoftLink

### creating soft link using absolute path,we have to be in current dir)
root@ubuntu16-1:~/sandbox# ln -s dir/dir2/myconfig SoftLink2myconf

root@ubuntu16-1:~/sandbox# ls -l | grep myconfig
lrwxrwxrwx 1 root root   17 Feb  1 23:59 SoftLink2myconf -> dir/dir2/myconfig


###creating soft link to a directory
root@ubuntu16-1:~/sandbox# ln -s dir/ soft2dir

root@ubuntu16-1:~/sandbox# ls -l | grep soft2dir
lrwxrwxrwx 1 root root    4 Feb  2 00:04 soft2dir -> dir/
```

`ls -l` همه لینک‌ها را نشان می‌دهد؛ برای soft linkها ستون دوم مقدار 1 دارد و خود لینک به فایل/مسیر اصلی اشاره می‌کند:

```
drwxr-xr-x 3 root root 4096 Feb  1 23:59 dir
-rw-r--r-- 2 root root    0 Jan 29 08:14 file1
-rw-r--r-- 1 root root    0 Jan 29 09:05 file2
-rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink
lrwxrwxrwx 1 root root    4 Feb  2 00:04 soft2dir -> dir/
lrwxrwxrwx 1 root root    5 Jan 29 09:06 SoftLink -> file2
lrwxrwxrwx 1 root root   17 Feb  1 23:59 SoftLink2myconf -> dir/dir2/myconfig
```

#### لینک‌های نمادین شکسته (Broken symlinks) <a href="broken-symlinks" id="broken-symlinks"></a>

از آنجا که hard linkها همیشه به inodeای اشاره می‌کنند که نماینده یک فایل است، معمولاً همیشه معتبر هستند. اما symlinkها می‌توانند به دلایل مختلف «بشکنند» (broken شوند)، از جمله:

* هنگام ساخت لینک، یا فایل اصلی وجود نداشته یا مقصد لینک موجود نبوده است.
* مقصد لینک حذف شده یا تغییر نام داده است.
* یکی از بخش‌های مسیر مقصد حذف شده یا تغییر نام داده است.

{% hint style="info" %}
**شناسایی لینک‌ها با دستور find**

برای اینکه ببینید کدام فایل‌ها hard link یک inode مشخص هستند، از دستور `find` و گزینه `-samefile` همراه با نام فایل، یا گزینه `-inum` همراه با شماره inode استفاده کنید:

```
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
2228291 file2
2228289 HardLink
2228292 SoftLink

root@ubuntu16-1:~/sandbox# find . -samefile file1
./HardLink
./file1

root@ubuntu16-1:~/sandbox# find . -inum 2228289
./HardLink
./file1
```

همچنین می‌توانیم با دستور `find` و عبارت `-type l` به دنبال symbolic linkها بگردیم:

```
root@ubuntu16-1:~/sandbox# find . -type l 
./SoftLink
```
{% endhint %}

### کپی کردن در مقابل لینک کردن (Copying versus linking) <a href="copying-versus-linking" id="copying-versus-linking"></a>

بسته به اینکه چه چیزی می‌خواهیم انجام دهیم، گاهی از لینک‌ها استفاده می‌کنیم و گاهی بهتر است از یک فایل کپی بگیریم.

* تفاوت اصلی این است که لینک‌ها چند نام برای یک فایل واحد فراهم می‌کنند، اما کپی کردن، دو مجموعه داده یکسان را با دو نام متفاوت ایجاد می‌کند.
* معمولاً برای backup و همچنین برای تست (مثلاً وقتی می‌خواهید یک برنامه جدید را امتحان کنید بدون اینکه داده عملیاتی‌تان در خطر بیفتد) از کپی استفاده می‌کنید.
* زمانی از لینک استفاده می‌کنید که به یک alias برای یک فایل (یا دایرکتوری) نیاز دارید؛ شاید برای داشتن مسیر راحت‌تر یا کوتاه‌تر.
* وقتی یک فایل را به‌روزرسانی می‌کنید، همه لینک‌های آن به‌روزرسانی را می‌بینند؛ اما اگر فایل را کپی کرده باشید اینطور نیست.

{% hint style="danger" %}
لینک‌های نمادین می‌توانند شکسته شوند اما عملیات نوشتن بعدی ممکن است فایل جدیدی ایجاد کند. از لینک‌ها با احتیاط استفاده کنید.
{% endhint %}

#### لینک‌ها و مدیریت سیستم (Links and system administration)

لینک‌ها، مخصوصاً symbolic linkها، در مدیریت سیستم Linux زیاد استفاده می‌شوند.

1- **ساختن alias از دستورات برای یک نسخه مشخص**

دستورها اغلب alias می‌شوند؛ طوری که کاربر مجبور نباشد شماره نسخه دستور فعلی را بداند، اما اگر لازم بود بتواند با نام‌های طولانی‌تر به نسخه‌های دیگر هم دسترسی داشته باشد.

```
root@ubuntu16-1:~/sandbox# which python
/usr/bin/python
root@ubuntu16-1:~/sandbox# ls -l /usr/bin/python
lrwxrwxrwx 1 root root 9 Nov 23  2017 /usr/bin/python -> python2.7
```

2- **نمونه‌هایی از alias برای دستورها**

گاهی چندین نام دستور از یک کد زیرین یکسان استفاده می‌کنند؛ مثلاً دستورات مختلف برای متوقف کردن (stopping) و راه‌اندازی مجدد (restarting) یک سیستم. گاهی هم نام دستور جدیدی مثل `genisoimage` جایگزین نام قدیمی‌تر می‌شود، اما نام قدیمی (mkisofs) به عنوان یک لینک به دستور جدید نگه داشته می‌شود.

```
root@ubuntu16-1:~/sandbox# which halt
/sbin/halt
root@ubuntu16-1:~/sandbox# ls -l /sbin/halt
lrwxrwxrwx 1 root root 14 Nov 27  2018 /sbin/halt -> /bin/systemctl

root@ubuntu16-1:~/sandbox# which mkisofs 
/usr/bin/mkisofs
root@ubuntu16-1:~/sandbox# ls -l /usr/bin/mkisofs
lrwxrwxrwx 1 root root 11 Nov 26  2017 /usr/bin/mkisofs -> genisoimage
```

3- **لینک‌های کتابخانه (Library links)**

نام کتابخانه‌ها هم به شکل گسترده با symlinkها مدیریت می‌شوند؛ یا برای اینکه برنامه‌ها به یک نام عمومی لینک شوند اما نسخه فعلی را دریافت کنند، یا برای مدیریت سیستم‌هایی مثل سیستم‌های ۶۴ بیتی که قادر به اجرای برنامه‌های ۳۲ بیتی هستند.

```
root@ubuntu16-1:~/sandbox# ls -l /usr/lib/
...
lrwxrwxrwx   1 root root     30 Jul 31  2018 libblas.so.3 -> /etc/alternatives/libblas.so.3
lrwxrwxrwx   1 root root     18 Jul  9  2011 libfsplib.so.0 -> libfsplib.so.0.0.0
-rw-r--r--   1 root root  22808 Jul  9  2011 libfsplib.so.0.0.0
lrwxrwxrwx   1 root root     19 Dec 21  2015 libgdiplus.so -> libgdiplus.so.0.0.0
lrwxrwxrwx   1 root root     19 Dec 21  2015 libgdiplus.so.0 -> libgdiplus.so.0.0.0
-rw-r--r--   1 root root 423928 Dec 21  2015 libgdiplus.so.0.0.0
drwxr-xr-x   2 root root   4096 Dec  1  2018 libmbim
-rw-r--r--   1 root root 241488 Apr 12  2016 libMonoPosixHelper.so
-rw-r--r--   1 root root 133880 Apr 12  2016 libMonoSupportW.so
lrwxrwxrwx   1 root root     17 Nov 26  2017 libnetpbm.so.10 -> libnetpbm.so.10.0
...
```

.

.

.

{% hint style="danger" %}
**حذف لینک‌های نمادین (remove symbolic links)**

می‌توانیم symbolic linkها را با استفاده از دستورهای `rm`، `unlink` و `find` حذف کنیم.

_برای حذف یک symlink، باید روی دایرکتوری‌ای که symlink داخل آن قرار دارد مجوز نوشتن (write permission) داشته باشید؛ در غیر این صورت با خطای “Operation not permitted” مواجه می‌شوید._

**حذف لینک‌های نمادین با `rm`**

برای حذف یک symlink، دستور `rm` را با نام symlink به عنوان آرگومان اجرا کنید: `rm symlink_name`

با `rm` می‌توانید چند symlink را همزمان حذف کنید. برای این کار، نام symlinkها را به صورت آرگومان و با فاصله از هم بدهید: `rm symlink1 symlink2`.
برای گرفتن تأیید از `-i` استفاده کنید.

اگر نام آرگومان به `/` ختم شود، `rm` فرض می‌کند فایل یک دایرکتوری است. در این حالت خطا رخ می‌دهد چون `rm` بدون گزینه‌های `-d` یا `-r` نمی‌تواند دایرکتوری را حذف کند.

#### حذف لینک‌های نمادین با `unlink`

دستور unlink یک فایل مشخص را حذف می‌کند. برخلاف `rm`، دستور unlink فقط یک آرگومان می‌پذیرد.

برای حذف یک symbolic link، دستور unlink را با نام symlink به عنوان آرگومان اجرا کنید: `unlink symlink_name`

در انتهای نام symlink از `/` استفاده نکنید، چون unlink نمی‌تواند دایرکتوری‌ها را حذف کند.

#### یافتن و حذف لینک‌های نمادین شکسته

برای پیدا کردن همه symlinkهای شکسته در زیر یک دایرکتوری مشخص، از دستور زیر استفاده کنید: `find /path/to/directory -xtype l`

بعد از پیدا کردن broken symlinkها، می‌توانید یا آن‌ها را به صورت دستی با `rm` یا `unlink` حذف کنید یا از گزینه `-delete` در دستور `find` استفاده کنید:
`find /path/to/directory -xtype l -delete`

#### نتیجه‌گیری (Conclusion)

برای حذف یک symbolic link، می‌توانید از `rm` یا `unlink` به همراه نام symlink به عنوان آرگومان استفاده کنید. هنگام حذف یک symlink که به یک دایرکتوری اشاره می‌کند، `/` را به انتهای نام symlink اضافه نکنید.
{% endhint %}

.

[https://developer.ibm.com/tutorials/l-lpic1-104-6/](https://developer.ibm.com/tutorials/l-lpic1-104-6/)

[http://bambamdeo-linux.blogspot.com/2014/11/soft-link-and-hard-link.html](http://bambamdeo-linux.blogspot.com/2014/11/soft-link-and-hard-link.html)

[https://www.ostechnix.com/explaining-soft-link-and-hard-link-in-linux-with-examples/](https://www.ostechnix.com/explaining-soft-link-and-hard-link-in-linux-with-examples/)

[https://linoxide.com/linux-how-to/difference-soft-link-hard-link/](https://linoxide.com/linux-how-to/difference-soft-link-hard-link/)

[https://www.geeksforgeeks.org/soft-hard-links-unixlinux/](https://www.geeksforgeeks.org/soft-hard-links-unixlinux/)

``[`https://linuxize.com/post/how-to-remove-symbolic-links-in-linux/`](https://linuxize.com/post/how-to-remove-symbolic-links-in-linux/)``

.
