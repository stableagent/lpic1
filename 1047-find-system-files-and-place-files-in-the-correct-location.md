# 104.7 یافتن فایل‌های سیستم و قرار دادن فایل‌ها در مکان صحیح

## **104.7. یافتن فایل‌های سیستم و قرار دادن فایل‌ها در مکان صحیح**

**وزن:** ۲

**توضیحات:** داوطلبان باید به طور کامل با استاندارد سلسله‌مراتب سیستم‌فایل (FHS)، از جمله مکان‌های معمول فایل‌ها و طبقه‌بندی دایرکتوری‌ها آشنا باشند.

**حوزه‌های کلیدی دانش:**

* درک مکان‌های صحیح فایل‌ها تحت FHS
* یافتن فایل‌ها و دستورات در یک سیستم لینوکس
* آگاهی از مکان و هدف فایل‌ها و دایرکتوری‌های مهم طبق تعریف FHS

**اصطلاحات و ابزارهای کاربردی:**

* find
* locate
* updatedb
* whereis
* which
* type
* /etc/updatedb.conf

بیش از ۲۰۰ توزیع لینوکس موجود است، که همه چیزهای زیادی مشترک دارند، باید یک روش استاندارد برای قرار دادن فایل‌ها در سیستم وجود داشته باشد، و اینجاست که FHS وارد عمل می‌شود.

### FHS

استاندارد سلسله‌مراتب سیستم‌فایل (Filesystem Hierarchy Standard - FHS) سندی است که طرح مشترکی از دایرکتوری‌ها را در یک سیستم لینوکس/یونیکس مشخص می‌کند.

| دایرکتوری | هدف |
| --------- | ------------------------------------------------------ |
| bin | فایل‌های باینری دستورات ضروری |
| boot | فایل‌های استاتیک boot loader |
| dev | فایل‌های دستگاه |
| etc | پیکربندی سیستم مخصوص هاست |
| lib | کتابخانه‌های اشتراکی ضروری و ماژول‌های کرنل |
| media | نقطه اتصال برای رسانه‌های قابل جابجایی |
| mnt | نقطه اتصال برای اتصال موقت یک سیستم‌فایل |
| opt | پکیج‌های نرم‌افزاری افزونه |
| sbin | فایل‌های باینری سیستم ضروری |
| srv | داده‌ها برای سرویس‌های ارائه شده توسط این سیستم |
| tmp | فایل‌های موقت |
| usr | سلسله‌مراتب ثانویه |
| var | داده‌های متغیر |
| home | دایرکتوری‌های خانگی کاربر (اختیاری) |
| lib | کتابخانه‌های اشتراکی ضروری به فرمت جایگزین (اختیاری) |
| root | دایرکتوری خانگی برای کاربر root (اختیاری) |

{% hint style="info" %}
سلسله‌مراتب `/usr` و `/var` به اندازه کافی پیچیده هستند که بخش‌های کاملی از FHS به آن‌ها اختصاص داده شده است.

`/usr` شامل فایل‌های باینری، کتابخانه‌ها، مستندات و کد منبع برای برنامه‌های سطح دوم است.

* **/usr/bin** شامل فایل‌های باینری برای برنامه‌های کاربر است.
* **/usr/sbin** شامل فایل‌های باینری برای مدیران سیستم است.
* **/usr/lib** شامل کتابخانه‌ها برای /usr/bin و /usr/sbin است.
* **/usr/local** شامل برنامه‌هایی است که شما از منبع نصب می‌کنید.
* **/usr/src** منابع کرنل لینوکس، فایل‌های هدر و مستندات را نگه می‌دارد.

سیستم‌فایل `/var` شامل فایل‌های داده متغیر است، از جمله دایرکتوری‌ها و فایل‌های spool، داده‌های مدیریتی و لاگینگ، و فایل‌های گذرا و موقت. برخی بخش‌های `/var` بین سیستم‌های مختلف قابل اشتراک نیستند، اما برخی دیگر مانند `/var/mail`، `/var/cache/man`، `/var/cache/fonts` و `/var/spool/news` ممکن است اشتراک گذاشته شوند.
{% endhint %}

### PATH

هنگامی که یک برنامه را در خط فرمان اجرا می‌کنید، bash (یا شل‌های دیگر) از طریق لیستی از دایرکتوری‌ها جستجو می‌کند تا برنامه‌ای که درخواست کرده‌اید را پیدا کند. لیست دایرکتوری‌ها در متغیر محیطی PATH شما مشخص شده است.

```
root@ubuntu16-1:~# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
```

```
user1@ubuntu16-1:~$ echo $PATH
/home/user1/bin:/home/user1/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

همانطور که می‌بینید، متغیر PATH فقط لیستی از نام‌های دایرکتوری است که با دو نقطه (:) جدا شده‌اند. تفاوت‌های بین مسیر کاربر و مسیر root را توجه کنید

همانطور که در بخش‌های قبلی گفتیم، می‌توانیم مسیر خود را با استفاده از `export PATH=$PATH:/new/path/dir` تغییر دهیم یا با اضافه کردن آن داخل **.bash_profile** یا **.bashrc**.

#### which, type, and whereis

### which

دستور `which` مسیر شما را جستجو می‌کند و مشخص می‌کند که کدام دستور اجرا خواهد شد (اگر وجود داشته باشد) هنگامی که یک دستور را تایپ می‌کنید.

```
root@ubuntu16-1:~# which ping
/bin/ping
```

> دستور `which` اولین وقوع یک دستور را در مسیر شما به شما نشان می‌دهد. اگر می‌خواهید بدانید چندین وقوع وجود دارد، گزینه `-a` را اضافه کنید

### type

برخی دستورات وجود دارند که دستور `which` آن‌ها را پیدا نمی‌کند، مانند shell builtins.

```
root@ubuntu16-1:~# which for
root@ubuntu16-1:~# type for
for is a shell keyword
root@ubuntu16-1:~# type type
type is a shell builtin
```

دستور `type` یک builtin است که کلیدواژه‌های bash را درک می‌کند و می‌تواند به ما بگوید که چگونه یک رشته دستور داده شده برای اجرا ارزیابی خواهد شد.

### whereis

اگر اطلاعات بیشتری از فقط مکان یک برنامه می‌خواهید، می‌توانید از دستور `whereis` استفاده کنید.

```
root@ubuntu16-1:~# whereis ping
ping: /bin/ping /usr/share/man/man8/ping.8.gz

root@ubuntu16-1:~# whereis mkfs
mkfs: /sbin/mkfs.ext4dev /sbin/mkfs /sbin/mkfs.minix /sbin/mkfs.ext2 /sbin/mkfs.ext4 /sbin/mkfs.ext3 /sbin/mkfs.fat /sbin/mkfs.bfs /sbin/mkfs.cramfs /sbin/mkfs.ntfs /sbin/mkfs.vfat /sbin/mkfs.msdos /usr/share/man/man8/mkfs.8.gz
```

دستور `whereis` همچنین می‌تواند صفحات man و کدهای منبع برنامه‌ها را در کنار مکان باینری آن‌ها جستجو کند.

### find

در یک درس قبلی از این سری "104-7"، یاد گرفتید که چگونه فایل‌ها را بر اساس نام (شامل wildcards)، مسیر، اندازه یا timestamp پیدا کنید. در یک درس قبلی دیگر از این سری، "104-6" یاد گرفتید که چگونه لینک‌ها را به یک فایل یا inode خاص پیدا کنید.

دستور `find` چاقوی سوئیسی (Swiss Army knife) ابزارهای جستجوی فایل در سیستم‌های لینوکس است. دو قابلیت دیگر که ممکن است مفید پیدا کنید، توانایی آن برای یافتن فایل‌ها بر اساس نام کاربر یا گروه و توانایی آن برای یافتن فایل‌ها بر اساس دسترسی‌ها است.

#### یافتن توسط مالک و دسترسی‌ها

ما همچنین می‌توانیم فایل‌ها را بر اساس مالک فایل یا مالک گروه (بحث شده در "104-5") جستجو کنیم. ما این کار را با استفاده از پارامترهای `-user` و `-group` به ترتیب انجام می‌دهیم.

```
### finding by user
root@ubuntu16-1:~# find /tmp -user user1 | head
/tmp/gnome-software-ZPJTE0
/tmp/gnome-software-ZPJTE0/debconf.socket
/tmp/gnome-software-YDX3E0
/tmp/gnome-software-YDX3E0/debconf.socket
/tmp/gnome-software-GD8NE0

### finding by group
root@ubuntu16-1:~/test-space# find /etc  -group shadow
/etc/shadow
/etc/gshadow
```

> از `-nouser` یا `-nogroup` استفاده کنید برای جستجوی یک فایل با بدون کاربر یا بدون شناسه گروه.

ما همچنین می‌توانیم فایل‌ها را با دسترسی‌های خاص جستجو کنیم. اگر می‌خواهیم یک مجموعه دقیق از دسترسی تطبیق دهیم: `find / -perm 644`

اگر می‌خواهیم هر چیزی با حداقل آن دسترسی‌ها مشخص کنیم: `find / -perm -644` .

#### فیلتر کردن توسط عمق

ما می‌توانیم عمق حداکثر جستجو را زیر دایرکتوری جستجوی سطح بالا مشخص کنیم:

```
find -maxdepth num -name query
```

```
find -mindepth num -name query
```

همچنین امکان ترکیب پارامترهای عمق min و max برای تمرکز بر یک محدوده باریک وجود دارد: `find -mindepth num -maxdepth num -name file`:

```

root@ubuntu16-1:~# find /tmp  -mindepth 1 -maxdepth 2 -user user1 | head
/tmp/gnome-software-ZPJTE0
/tmp/gnome-software-ZPJTE0/debconf.socket
/tmp/gnome-software-YDX3E0
/tmp/gnome-software-YDX3E0/debconf.socket
/tmp/gnome-software-GD8NE0
/tmp/gnome-software-GD8NE0/debconf.socket
/tmp/gnome-software-U5COF0
/tmp/gnome-software-U5COF0/debconf.socket
```

> مانند سایر تست‌ها، می‌توانید یک ! را دقیقا قبل از هر عبارت اضافه کنید تا آن را نفی کنید. بنابراین این فایل‌هایی را که متعلق به user1 نیستند پیدا می‌کند: `find . ! -user user1`

#### locate & updatedb

دستور `find` تمام دایرکتوری‌هایی که مشخص می‌کنید را هر بار که اجرا می‌شود جستجو می‌کند. برای سرعت بخشیدن به امور، می‌توانید از دستور دیگری استفاده کنید، `locate`، که از یک پایگاه داده اطلاعات مسیر ذخیره شده استفاده می‌کند به جای جستجوی سیستم‌فایل در هر بار.

### locate

دستور `locate` فایل‌های منطبق را در یک پایگاه داده جستجو می‌کند که معمولاً روزانه به‌روزرسانی می‌شود (توسط cron job).

```
root@ubuntu16-1:~# locate bin/ls
/bin/ls
/bin/lsblk
/bin/lsmod
/sbin/lsmod
/sbin/lspcmcia
/usr/bin/lsattr
/usr/bin/lsb_release
/usr/bin/lscpu
/usr/bin/lsdiff
/usr/bin/lshw
/usr/bin/lsinitramfs
/usr/bin/lsipc
/usr/bin/lslocks
/usr/bin/lslogins
/usr/bin/lsof
/usr/bin/lspci
/usr/bin/lspgpot
/usr/bin/lsusb
/usr/lib/klibc/bin/ls
/usr/lib/plainbox-provider-checkbox/bin/lsmod_info
/usr/lib/plainbox-provider-resource-generic/bin/lsb_resource
```

دستور `locate` با هر بخشی از یک نام مسیر منطبق می‌شود، نه فقط نام فایل.

### updatedb

پایگاه داده پیش‌فرض استفاده شده توسط locate در سیستم‌فایل `/var` ذخیره شده است، در مکانی مانند `/var/lib/locatedb`. _این ممکن است در سیستم‌هایی که از پکیج‌های slocate یا mlocate برای ارائه امنیت یا سرعت اضافی استفاده می‌کنند متفاوت باشد_. شما می‌توانید آمار روی پایگاه داده locate خود را با استفاده از locate-S پیدا کنید:

```
root@ubuntu16-1:~# locate -S
Database /var/lib/mlocate/mlocate.db:
	25,916 directories
	251,926 files
	13,669,511 bytes in file names
	6,067,728 bytes used to store database
```

پایگاه داده با استفاده از دستور `updatedb` ایجاد یا به‌روزرسانی می‌شود. _(این معمولاً به صورت روزانه به عنوان یک cron job اجرا می‌شود).

```
root@ubuntu16-1:~# updatedb
```

> از `-v` برای حالت verbose استفاده کنید تا ببینید چه اتفاقی پس از دستور updatedb می‌افتد!

فایل `/etc/updatedb.conf`، یا گاهی `/etc/sysconfig/locate`، فایل پیکربندی برای `updatedb` است:

```
PRUNE_BIND_MOUNTS="yes"
# PRUNENAMES=".git .bzr .hg .svn"
PRUNEPATHS="/tmp /var/spool /media /home/.ecryptfs /var/lib/schroot"
PRUNEFS="NFS nfs nfs4 rpc_pipefs afs binfmt_misc proc smbfs autofs iso9660 ncpfs coda devpts ftpfs devfs mfs shfs sysfs cifs lustre tmpfs usbfs udf fuse.glusterfs fuse.sshfs curlftpfs ecryptfs fusesmb devtmpfs"
```

برخی PRUNING در فایل پیکربندی وجود دارند که باعث می‌شوند locate هرگز برای آن انواع فایل‌ها یا دایرکتوری‌ها مانند `/tmp` یا `/var/spool` جستجو نکند. می‌توانید به locate اجازه دهید تا اگر دوست دارید برای آن‌ها هم جستجو کند با دستکاری این فایل.

همه چیز تمام شد.

## تبریک، lpic1-101 را تمام کردیم !!!

.

.


.


[https://developer.ibm.com/tutorials/l-lpic1-104-7/](https://developer.ibm.com/tutorials/l-lpic1-104-7/)

[https://www.geeksforgeeks.org/linux-file-hierarchy-structure/](https://www.geeksforgeeks.org/linux-file-hierarchy-structure/)

[https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps](https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps)[https://jadi.gitbooks.io/lpic1/content/1047\_find_system_files_and_place_files_in_the_correct_location.html](https://jadi.gitbooks.io/lpic1/content/1047\_find_system_files_and_place_files_in_the_correct_location.html)

.
