# 104.2. حفظ یکپارچگی filesystems

**وزن:** ۲

**توضیحات:** داوطلبان باید قادر به نگهداری یک سیستم‌فایل استاندارد و همچنین داده‌های اضافی مرتبط با یک سیستم‌فایل دارای ژورنال باشند.

**حوزه‌های کلیدی دانش:**

* بررسی یکپارچگی filesystems
* مانیتور کردن فضای آزاد و inodeها
* تعمیر مشکلات ساده filesystem

**اصطلاحات و ابزارهای کاربردی:**

* du
* df
* fsck
* e2fsck
* mke2fs
* debugfs
* dumpe2fs
* tune2fs
* XFS tools (such as xfs_metadump and xfs_info)

در مواقعی که سیستم crash می‌کند یا برق قطع می‌شود، filesystemها ممکن است در وضعیت ناسازگار (inconsistent) باقی بمانند؛ یعنی بعضی تغییرات کامل شده‌اند و بعضی نه.

کار کردن با filesystem آسیب‌دیده ایده خوبی نیست، چون احتمالاً خطاهای موجود را بدتر می‌کند. در این درس چند ابزار را بررسی می‌کنیم که به مدیریت چنین مشکلاتی کمک می‌کنند.

{% hint style="danger" %}
همیشه باید قبل از اقدام به هرگونه تعمیر، از سیستم‌فایل خود نسخه پشتیبان تهیه کنید!
{% endhint %}

### fsck

ابزار اصلی برای بررسی و تعمیر filesystems، دستور `fsck` است که مانند `mkfs` در واقع یک front end برای روتین‌های بررسی filesystem برای انواع مختلف filesystem محسوب می‌شود.

```
root@ubuntu16-1:~# fsck
fsck          fsck.ext3     fsck.fat      fsck.nfs      
fsck.cramfs   fsck.ext4     fsck.minix    fsck.vfat     
fsck.ext2     fsck.ext4dev  fsck.msdos 

root@ubuntu16-1:~# ls /sbin/fsck*
/sbin/fsck         /sbin/fsck.ext3     /sbin/fsck.fat    /sbin/fsck.nfs
/sbin/fsck.cramfs  /sbin/fsck.ext4     /sbin/fsck.minix  /sbin/fsck.vfat
/sbin/fsck.ext2    /sbin/fsck.ext4dev  /sbin/fsck.msdos
```

> بعضی از این‌ها صرفاً لینک‌هایی به دستور e2fsck هستند و عملاً یکسان عمل می‌کنند.

دستور fsck در Linux به ما اجازه می‌دهد به صورت دستی ناسازگاری‌های filesystem را بررسی کنیم. اجرای fsck نیاز به دسترسی superuser یا **root** دارد (ubuntu 16.04):

```
root@ubuntu16-1:~# fsck /dev/sda1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/sda1 is mounted.
e2fsck: Cannot continue, aborting.
```

{% hint style="danger" %}
برای استفاده از fsck، پارتیشن باید unmount شود؛ در غیر این صورت ممکن است باعث آسیب شود.
{% endhint %}

بیایید یک filesystem روی یک پارتیشن ext3 که unmount شده است (sdb1) را بررسی کنیم و در صورت وجود خطا، سعی کنیم آن‌ها را اصلاح کنیم:

```
root@ubuntu16-1:~# fsck /dev/sdb1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/sdb1: clean, 11/1310720 files, 126322/5242624 blocks
```

این دستور تلاش می‌کند `/dev/sdb1` را بررسی کند و هر خطایی که پیدا کند گزارش دهد. کد خروجی (exit code) بازگشتیِ fsck یکی از حالت‌های زیر است:

* 0 بدون خطا
* 1 خطاهای filesystem اصلاح شدند
* 2 سیستم باید reboot شود
* 4 خطاهای filesystem بدون اصلاح باقی ماندند
* 8 خطای عملیاتی (Operational error)
* 16 خطای استفاده یا syntax
* 32 بررسی به درخواست کاربر لغو شد
* 128 خطای shared-library

گزینه `-N` فقط نشان می‌دهد چه چیزی اجرا می‌شود، اما هیچ تلاشی برای تعمیر انجام نمی‌دهد:

```
root@ubuntu16-1:~# fsck -N /dev/sdb1
fsck from util-linux 2.27.1
[/sbin/fsck.ext3 (1) -- /dev/sdb1] fsck.ext3 /dev/sdb1
```

گزینه `-n` باعث می‌شود این دستورات چیزی را اصلاح نکنند و فقط نشان دهند قرار است چه کاری انجام شود:

```
root@ubuntu16-1:~# fsck -n /dev/sdb1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/sdb1: clean, 11/1310720 files, 126322/5242624 blocks
```

به طور معمول، **fsck** بخش‌هایی از filesystem را که «clean» علامت‌گذاری شده‌اند رد می‌کند؛ یعنی همه pending writeها با موفقیت انجام شده‌اند. گزینه **-f** (force) مشخص می‌کند fsck حتی اگر filesystem «dirty» نباشد هم آن را بررسی کند. نتیجه بررسی کامل‌تر است، هرچند ممکن است کارایی کمتری داشته باشد.

```
root@ubuntu16-1:~# fsck -f /dev/sdb1
fsck from util-linux 2.27.1
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/sdb1: 11/1310720 files (0.0% non-contiguous), 126322/5242624 blocks

```

{% hint style="success" %}
**ایندکس نودها (inodes) چیستند؟**

همانطور که گفتیم Linux با همه چیز مثل فایل برخورد می‌کند (حتی deviceهای سخت‌افزاری). کیبورد، ماوس، پرینتر، مانیتور، هارد دیسک، processها و حتی دایرکتوری‌ها در Linux مثل فایل در نظر گرفته می‌شوند. فایل‌های معمولی داده‌هایی مثل متن (text files)، موسیقی و ویدئو (multimedia files) را نگهداری می‌کنند. علاوه بر داده‌های اصلی، اطلاعات دیگری هم درباره فایل وجود دارد؛ مثل اندازه، مالکیت، مجوزها (permissions)، timestamp و ... . این meta-data فایل توسط یک ساختار داده به نام inode (index node) مدیریت می‌شود.
{% endhint %}

> همچنین می‌توانیم filesystemها را با UUID هم بررسی کنیم (با استفاده از دستور blkid):
>
> ```
> root@ubuntu16-1:~# blkid /dev/sdb1
> /dev/sdb1: UUID="b9ca56aa-4ea3-4f61-92c8-18c867fd991f" SEC_TYPE="ext2" TYPE="ext3" PARTUUID="4d71bc84-01"
> root@ubuntu16-1:~# fsck -N UUID="b9ca56aa-4ea3-4f61-92c8-18c867fd991f"
> fsck from util-linux 2.27.1
> e2fsck 1.42.13 (17-May-2015)
> /dev/sdb1: clean, 11/1310720 files, 126322/5242624 blocks
> ```

| نمونه دستور fsck | توضیح |
| ---------------------- | -------------------------------------------- |
| fsck -M /dev/sda1      | جلوگیری از اجرای fsck روی filesystem mount شده |
| fsck -t ext3 /dev/sdb1 | بررسی فقط یک نوع filesystem مشخص |
| fsck -y -f /dev/sdb1   | پاسخ دادن «yes» به همه سؤال‌ها برای تلاش جهت اصلاح |

> برای بررسی یک filesystem از نوع XFS باید از دستور `xfs_check` استفاده کنیم.

## ابزارهای پیشرفته (Advanced tools)

چند ابزار پیشرفته‌تر هم وجود دارد که می‌توانیم برای بررسی یا تعمیر filesystem از آن‌ها استفاده کنیم.

#### ابزارهای ext2 و ext3 <a href="tools-for-ext2-and-ext3-filesystems" id="tools-for-ext2-and-ext3-filesystems"></a>

* **tune2fs:** تنظیم/تغییر پارامترها روی ext2 و ext3. همچنین برای اضافه کردن journal
* **dumpe2fs:** نمایش اطلاعات superblockها
* **debugfs:** ویرایشگر تعاملی filesystem

{% hint style="info" %}
**Super Blocks**

شاید بپرسید این ابزارهای بررسی و تعمیر از کجا می‌فهمند باید از کجا شروع کنند. filesystemهای Linux و UNIX معمولاً یک _superblock_ دارند که _metadata_ مربوط به filesystem را توصیف می‌کند (یعنی داده‌هایی درباره خود filesystem). این اطلاعات معمولاً در یک مکان شناخته‌شده ذخیره می‌شود (اغلب در ابتدای filesystem یا نزدیک آن) و در مکان‌های شناخته‌شده دیگری هم کپی می‌شود. می‌توانید از گزینه `-n` در `mke2fs` برای نمایش محل superblockها در یک filesystem موجود استفاده کنید.

```
root@ubuntu16-1:~# mke2fs -n /dev/sdb1
mke2fs 1.42.13 (17-May-2015)
/dev/sdb1 contains a ext3 file system
	created on Sat Jan 25 22:17:24 2020
Proceed anyway? (y,n) y
Creating filesystem with 5242624 4k blocks and 1310720 inodes
Filesystem UUID: 28ce54ac-abbd-4788-bab8-b71eae9a53bc
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000
```
{% endhint %}

### tune2fs

خانواده ext یک ابزار به نام `tune2fs` هم دارد که می‌توان از آن برای بررسی اطلاعاتی مثل block count و همچنین اینکه filesystem journaled هست (ext3 یا ext4) یا نه (ext2) استفاده کرد.

{% hint style="success" %}
دستور tune2fs به شما اجازه می‌دهد پارامترهای مختلفی از filesystem را روی یک ext2 یا ext3 (حتی وقتی mount شده است) تنظیم کنید.
{% endhint %}

گزینه `-l` محتویات superblock (از جمله مقادیر فعلی پارامترها) را نمایش می‌دهد:

```
root@ubuntu16-1:~# tune2fs -l /dev/sdb1
tune2fs 1.42.13 (17-May-2015)
Filesystem volume name:   <none>
Last mounted on:          <not available>
Filesystem UUID:          b9ca56aa-4ea3-4f61-92c8-18c867fd991f
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype sparse_super large_file
Filesystem flags:         signed_directory_hash 
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              1310720
Block count:              5242624
Reserved block count:     262131
Free blocks:              5116302
Free inodes:              1310709
First block:              0
Block size:               4096
Fragment size:            4096
Reserved GDT blocks:      1022
Blocks per group:         32768
Fragments per group:      32768
Inodes per group:         8192
Inode blocks per group:   512
Filesystem created:       Wed Jan 22 23:42:22 2020
Last mount time:          n/a
Last write time:          Thu Jan 23 02:21:07 2020
Mount count:              0
Maximum mount count:      -1
Last checked:             Thu Jan 23 02:21:07 2020
Check interval:           0 (<none>)
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:	          256
Required extra isize:     28
Desired extra isize:      28
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      73007ad4-213f-44cd-b8f0-4fbd0c1e90b2
Journal backup:           inode blocks

```

> از tune2fs می‌توان برای تنظیم پارامترهای زیاد استفاده کرد یا با گزینه `-j` یک journal اضافه کرد و ext2 را به ext3 تبدیل کرد: `tune2fs -j /dev/sdd1`

همچنین می‌توانیم از tune2fs برای تغییر یا تنظیم label پارتیشن استفاده کنیم:

```
root@ubuntu16-1:~# tune2fs -L myroot /dev/sda1
tune2fs 1.42.13 (17-May-2015)
```

### dumpe2fs

دستور `dumpe2fs` برای چاپ اطلاعات superblock و block group descriptor برای filesystem موجود روی device استفاده می‌شود.

{% hint style="danger" %}
اطلاعات چاپ‌شده ممکن است وقتی filesystem mount است قدیمی یا ناسازگار باشد. قبل از استفاده از این دستور، پارتیشن را unmount کنید.
{% endhint %}

```
root@ubuntu16-1:~# dumpe2fs  /dev/sdb1 | grep superblock
dumpe2fs 1.42.13 (17-May-2015)
  Primary superblock at 0, Group descriptors at 1-2
  Backup superblock at 32768, Group descriptors at 32769-32770
  Backup superblock at 98304, Group descriptors at 98305-98306
  Backup superblock at 163840, Group descriptors at 163841-163842
  Backup superblock at 229376, Group descriptors at 229377-229378
  Backup superblock at 294912, Group descriptors at 294913-294914
  Backup superblock at 819200, Group descriptors at 819201-819202
  Backup superblock at 884736, Group descriptors at 884737-884738
  Backup superblock at 1605632, Group descriptors at 1605633-1605634
  Backup superblock at 2654208, Group descriptors at 2654209-2654210
  Backup superblock at 4096000, Group descriptors at 4096001-4096002

```

### debugfs

یک debugger تعاملی برای filesystem است. از آن برای بررسی یا تغییر وضعیت یک filesystem از نوع ext2 یا ext3 استفاده کنید. به طور پیش‌فرض filesystem را به صورت read-only باز می‌کند مگر اینکه با گزینه `-w` خلاف آن را مشخص کنیم.

{% hint style="danger" %}
اگر filesystem mount باشد، برای inspect کردن مشکلی ندارد، اما **هرگز** روی filesystem mount شده تلاش برای repair انجام ندهید.
{% endhint %}

```
root@ubuntu16-1:~# debugfs /dev/sda1
debugfs 1.42.13 (17-May-2015)
debugfs:  cd /etc/
debugfs:  pwd
[pwd]   INODE: 1179649  PATH: /etc
[root]  INODE:      2  PATH: /
debugfs:  stat passwd

Inode: 1187630   Type: regular    Mode:  0644   Flags: 0x80000
Generation: 2904697195    Version: 0x00000000:00000001
User:     0   Group:     0   Size: 2388
File ACL: 0    Directory ACL: 0
Links: 1   Blockcount: 8
Fragment:  Address: 0    Number: 0    Size: 0
 ctime: 0x5e2064c5:64a1019c -- Thu Jan 16 16:57:33 2020
 atime: 0x5e2cb8a0:4aa345f8 -- Sun Jan 26 01:22:32 2020
 mtime: 0x5e2064c5:64a1019c -- Thu Jan 16 16:57:33 2020
crtime: 0x5e2064c5:64a1019c -- Thu Jan 16 16:57:33 2020
Size of extra inode fields: 32
EXTENTS:
(0):4265700

debugfs:  ncheck 1187630
Inode	Pathname
1187630	/etc/passwd
debugfs:  q
```

### xfs_info

filesystem از نوع XFS خانواده ابزارهای خودش را دارد. `xfs_info` مشابه tune2fs است اما برای XFS.

{% hint style="success" %}
از xfs_info باید روی filesystem mount شده استفاده شود.
{% endhint %}

> پکیج xfsprogs باید نصب باشد.

```
root@ubuntu16-1:~# xfs_info /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=1310656 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1 spinodes=0
data     =                       bsize=4096   blocks=5242624, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

| دستور xfs | توضیح |
| ------------ | ----------------------------- |
| xfs_info     | نمایش اطلاعات |
| xfs_check    | بررسی کامل filesystem |
| xfs_repair   | بررسی و تلاش برای اصلاح مشکل |
| xfs_check -n | مثل xfs_check |

```
root@ubuntu16-1:~# xfs_repair -n /dev/sdb1
xfs_repair: /dev/sdb1 contains a mounted and writable filesystem

fatal error -- couldn't initialize XFS library
```

{% hint style="danger" %}
بدیهی است xfs_repair روی filesystem mount شده کار نمی‌کند.
{% endhint %}

```
root@ubuntu16-1:~# xfs_repair  /dev/sdb1
Phase 1 - find and verify superblock...
Phase 2 - using internal log
        - zero log...
        - scan filesystem freespace and inode maps...
        - found root inode chunk
Phase 3 - for each AG...
        - scan and clear agi unlinked lists...
        - process known inodes and perform inode discovery...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
        - process newly discovered inodes...
Phase 4 - check for duplicate blocks...
        - setting up duplicate extent list...
        - check for inodes claiming duplicate blocks...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
Phase 5 - rebuild AG headers and trees...
        - reset superblock...
Phase 6 - check inode connectivity...
        - resetting contents of realtime bitmap and summary inodes
        - traversing filesystem ...
        - traversal finished ...
        - moving disconnected inodes to lost+found ...
Phase 7 - verify and correct link counts...
done
```

> در XFS فقط می‌توانید filesystem را extend کنید و نمی‌توانید آن را کوچک‌تر (reduce) کنید.

### مانیتور کردن فضای آزاد <a href="monitoring-free-space" id="monitoring-free-space"></a>

روی یک storage device، یک فایل یا دایرکتوری در مجموعه‌ای از _block_ها قرار دارد. اطلاعات مربوط به یک فایل در یک _inode_ قرار می‌گیرد.

> **یادآوری:** inode اطلاعاتی مثل owner، آخرین زمان دسترسی، اندازه فایل، اینکه آیا دایرکتوری است یا نه، و اینکه چه کسی می‌تواند بخواند/بنویسد را نگه می‌دارد. شماره inode همچنین به عنوان file serial number هم شناخته می‌شود و در داخل یک filesystem مشخص یکتا است.

blockهای داده و inodeها هر کدام فضا اشغال می‌کنند؛ بنابراین باید مصرف فضا را مانیتور کنیم تا مطمئن شویم filesystemها فضای کافی برای رشد دارند.

### df

دستور `df` (DiskFree) برای دیدن فضای استفاده‌شده و فضای آزاد filesystemها استفاده می‌شود.

```
df [OPTION]...[FILE]...
```

اگر هیچ نام فایلی به عنوان آرگومان به df داده نشود، فضای موجود روی همه filesystemهای **mount** شده را نمایش می‌دهد.

```
root@ubuntu16-1:~# df
Filesystem     1K-blocks    Used Available Use% Mounted on
udev              462796       0    462796   0% /dev
tmpfs              98508    6544     91964   7% /run
/dev/sda1       50442940 5613840  42243708  12% /
tmpfs             492540     284    492256   1% /dev/shm
tmpfs               5120       4      5116   1% /run/lock
tmpfs             492540       0    492540   0% /sys/fs/cgroup
tmpfs              98508      48     98460   1% /run/user/1001
```

گزینه `-T` نوع filesystem را چاپ می‌کند، و `-h`/`--human-readable` اندازه‌ها را به صورت قابل خواندن (توان‌های 1024) نمایش می‌دهد:

```
root@ubuntu16-1:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  452M     0  452M   0% /dev
tmpfs          tmpfs      97M  6.4M   90M   7% /run
/dev/sda1      ext4       49G  5.4G   41G  12% /
tmpfs          tmpfs     481M  284K  481M   1% /dev/shm
tmpfs          tmpfs     5.0M  4.0K  5.0M   1% /run/lock
tmpfs          tmpfs     481M     0  481M   0% /sys/fs/cgroup
tmpfs          tmpfs      97M   48K   97M   1% /run/user/1001
```

> گزینه `-H` هم خروجی human readable می‌دهد اما با توان‌های 1000.

گزینه `-i` اطلاعات inode را به جای مصرف block نمایش می‌دهد:

```
root@ubuntu16-1:~# df -i
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
udev            115699    456  115243    1% /dev
tmpfs           123135    750  122385    1% /run
/dev/sda1      3211264 256212 2955052    8% /
tmpfs           123135      9  123126    1% /dev/shm
tmpfs           123135      6  123129    1% /run/lock
tmpfs           123135     17  123118    1% /sys/fs/cgroup
tmpfs           123135     27  123108    1% /run/user/1001
```

> یادتان هست؟ روی filesystemهای vfat نه owner داریم و نه access rights؛ در نتیجه vfat inode ندارد.

| نمونه دستور df | توضیح |
| ------------------ | ---------------------------------------------------------------------------------- |
| df -a              | نمایش همه اطلاعات (شامل pseudo، تکراری و filesystemهای غیرقابل دسترس) |
| df -Th /home       | نمایش اطلاعات filesystem مربوط به /home |
| df -k یا -m یا -h  | نمایش اطلاعات بر حسب Bytes/MB/GB |
| df -t ext3         | فقط شامل یک نوع filesystem مشخص |
| df -x xfs          | حذف/استثناء کردن یک نوع filesystem مشخص |

دستور `df` درباره کل یک filesystem اطلاعات می‌دهد. گاهی می‌خواهید بدانید یک فایل یا دایرکتوری مشخص چقدر فضا گرفته است. برای پاسخ به این سؤال از دستور `du` استفاده می‌کنیم.

### du

دستور `du` (Disk Usage) برای دیدن **میزان مصرف دیسک فایل‌ها و دایرکتوری‌ها** روی سیستم استفاده می‌شود.

```
du [OPTION]... [FILE]...
```

| گزینه کاربردی | توضیح |
| ------------- | --------------------------------------------------------------- |
| -a            | نمایش اندازه همه فایل‌ها (نه فقط دایرکتوری‌ها) |
| -h            | خروجی human readable (Bytes/KB/MB/GB/...) |
| -c            | نمایش مجموع کل در خط آخر |
| --max-depth=N | فقط تا عمق N زیردایرکتوری جلو می‌رود |
| -s            | فقط مجموع هر دایرکتوری را نمایش می‌دهد |

```
root@ubuntu16-1:~# du
16	./.aptitude
8	./.cache/dconf
12	./.cache
8	./.config/htop
8	./.config/gedit
8	./.config/dconf
4	./.config/ibus/bus
8	./.config/ibus
36	./.config
4	./.gnupg/private-keys-v1.d
48	./.gnupg
4	./.gconf
12	./.elinks
16	./.ssh
4	./.nano
8	./.local/share
12	./.local
336	./backup
8	./.dbus/session-bus
12	./.dbus
4	./test-space
932	.

root@ubuntu16-1:~# du -sh
932K	.
```

گزینه `--time` برای نمایش آخرین زمان تغییر (last modification time) در خروجی du استفاده می‌شود.

گزینه `--exclude=PATTERN` فایل‌هایی که با PATTERN match شوند را نادیده می‌گیرد. مثال: `du -ah --exclude="*.txt" /home/payam`

#### 

#### summary

در این درس با چند ابزار کلیدی برای بررسی و تعمیر filesystem و همچنین مانیتور کردن فضای آزاد و inodeها آشنا شدیم.

بیایید نگاهی به چند ابزار تعمیر دیگر (علاوه بر ابزارهایی که در این درس بررسی کردیم) بیندازیم:

| file system | command               | توضیح |
| ----------- | --------------------- | ----------------------------------------------------------------------------------------------------------- |
| ext         | **tune2fs**           | تنظیم پارامترها روی ext2 و ext3 و امکان فعال کردن journaling |
| ext         | **dumpe2fs**          | چاپ اطلاعات superblock و block group descriptor برای ext2/ext3 |
| ext         | **debugfs**           | debugger تعاملی filesystem برای ext2/ext3 |
| Reiserfs    | **reiserfstune**      | نمایش و تنظیم پارامترها روی ReiserFS |
| Reiserfs    | **debugreiserfs**     | مشابه dumpe2fs و debugfs برای ReiserFS |
| xfs         | **xfs_info**          | نمایش اطلاعات filesystem از نوع XFS |
| xfs         | **xfs_growfs**        | گسترش (expand) یک filesystem از نوع XFS |
| xfs         | **xfs_admin**         | تغییر پارامترهای یک filesystem از نوع XFS |
| xfs         | **xfs_repair**        | تعمیر یک filesystem از نوع XFS وقتی بررسی‌های mount کافی نیست |
| xfs         | **xfs_db**            | بررسی یا debug کردن یک filesystem از نوع XFS |
| btrfs       | **btrfs**             | نمایش بخش‌های مختلفی از اطلاعات filesystem btrfs |
| btrfs       | **btrfsck**           | بررسی filesystemهای btrfs |
| btrfs       | **btrfs-find-root**   | پیدا کردن blockی که root filesystem btrfs است |
| btrfs       | **btrfs-debug-tree**  | نمایش metadata داخلی btrfs |
| btrfs       | **btrfstune**         | تنظیم پارامترهای btrfs و فعال/غیرفعال کردن برخی قابلیت‌های extended |
| btrfs       | **btrfs-restore**     | تلاش برای بازیابی فایل‌ها از یک filesystem btrfs آسیب‌دیده |

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-2/](https://developer.ibm.com/tutorials/l-lpic1-104-2/)

[https://www.thegeekstuff.com/2012/08/fsck-command-examples/](https://www.thegeekstuff.com/2012/08/fsck-command-examples/)

[https://www.computerhope.com/unix/fsck.htm](https://www.computerhope.com/unix/fsck.htm)

[https://www.geeksforgeeks.org/file-system-consistency-checker-fsck/](https://www.geeksforgeeks.org/file-system-consistency-checker-fsck/)[https://www.serverwatch.com/tutorials/article.php/3797306/tune2fs-Makes-It-Easy-to-Play-With-Filesystems.htm](https://www.serverwatch.com/tutorials/article.php/3797306/tune2fs-Makes-It-Easy-to-Play-With-Filesystems.htm)[https://jadi.gitbooks.io/lpic1/content/1042\_maintain_the_integrity_of_filesystems.html](https://jadi.gitbooks.io/lpic1/content/1042\_maintain_the_integrity_of_filesystems.html)

[https://linoxide.com/linux-command/linux-inode/](https://linoxide.com/linux-command/linux-inode/)

[https://www.geeksforgeeks.org/dumpe2fs-command-in-linux-with-examples/](https://www.geeksforgeeks.org/dumpe2fs-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/dumpe2fs-command-in-linux-with-examples/](https://www.geeksforgeeks.org/dumpe2fs-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/df-command-in-linux-with-examples/](https://www.geeksforgeeks.org/df-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/df-command-linux-examples/](https://www.geeksforgeeks.org/df-command-linux-examples/)

[https://www.tecmint.com/how-to-check-disk-space-in-linux/](https://www.tecmint.com/how-to-check-disk-space-in-linux/)

[https://www.geeksforgeeks.org/du-command-linux/](https://www.geeksforgeeks.org/du-command-linux/)

[https://www.geeksforgeeks.org/du-command-linux-examples/](https://www.geeksforgeeks.org/du-command-linux-examples/)

[https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/](https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/)

با تشکر ویژه از shawn powers.

.

.
