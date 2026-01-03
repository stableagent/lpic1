# 104.1 ایجاد پارتیشن‌ها و سیستم‌فایل‌ها (partitions and filesystems)

## **104.1 ایجاد پارتیشن‌ها و سیستم‌فایل‌ها (partitions and filesystems)**

**وزن:** ۲

**توضیحات:** داوطلبان باید قادر به پیکربندی پارتیشن‌های دیسک و سپس ایجاد سیستم‌فایل‌ها روی رسانه‌هایی مانند هارد دیسک باشند. این شامل مدیریت پارتیشن‌های swap نیز می‌شود.

**حوزه‌های کلیدی دانش:**

* مدیریت جدول پارتیشن MBR
* استفاده از دستورات مختلف mkfs برای ساخت filesystemهای مختلف مثل:
  * ext2/ext3/ext4
  * XFS
  * VFAT
* آشنایی با ReiserFS و Btrfs
* دانش پایه درباره gdisk و parted همراه با GPT

**اصطلاحات و ابزارهای کاربردی:**

* fdisk
* gdisk
* parted
* mkfs
* mkswap

### BIOS

Basic Input/Output System یا BIOS (که با نام‌هایی مثل System BIOS و ROM BIOS هم شناخته می‌شود) یک استاندارد برای تعریف یک firmware interface است. نرم‌افزار BIOS داخل PC قرار دارد و اولین نرم‌افزاری است که هنگام روشن شدن کامپیوتر اجرا می‌شود.

هدف‌های اصلی BIOS این است که کامپوننت‌های سخت‌افزاری سیستم را initialize و تست کند و سپس یک bootloader یا یک operating system را از یک mass memory device بارگذاری کند.

### UEFI

Unified Extensible Firmware Interface یا UEFI یک specification است که یک software interface بین operating system و platform firmware تعریف می‌کند. UEFI برای جایگزینی firmware interface مربوط به BIOS طراحی شده است. در عمل، بیشتر UEFI imageها برای سرویس‌های BIOS پشتیبانی legacy فراهم می‌کنند. UEFI حتی بدون یک operating system دیگر، می‌تواند از remote diagnostics و تعمیر کامپیوترها پشتیبانی کند.

{% hint style="info" %}
> مشخصات اصلی EFI (Extensible Firmware Interface) توسط اینتل توسعه یافته است. UEFI هنوز فراگیر نشده است اما شرکت‌های سخت‌افزاری بزرگ تقریباً به طور انحصاری به استفاده از UEFI روی آورده‌اند. بسیاری از مادربردهای قدیمی‌تر و ارزان‌تر هنوز از سیستم BIOS استفاده می‌کنند.
{% endhint %}

### MBR

Master Boot Record یا MBR یک نوع ویژه از boot sector است که در ابتدای deviceهای ذخیره‌سازی پارتیشن‌بندی‌شده (مثل دیسک‌های ثابت یا removable driveها) قرار می‌گیرد.

MBR اطلاعات مربوط به نحوه سازمان‌دهی logical partitionها (که filesystemها را داخل خود دارند) را نگهداری می‌کند. علاوه بر این، MBR شامل کد اجرایی است که نقش loader برای operating system نصب‌شده را بازی می‌کند (معمولاً با انتقال کنترل به مرحله دوم loader). به این کد MBR معمولاً boot loader گفته می‌شود.

![](.gitbook/assets/bootloader-mbr.jpg)

**Master Boot Record چند محدودیت دارد:**

* MBR همه اطلاعات را در sector اول هارد دیسک قرار می‌دهد؛ بنابراین اگر برای آن sector مشکلی پیش بیاید، سیستم دیگر قادر به boot شدن نخواهد بود.
* MBR فقط چهار entry (slot) برای چهار پارتیشن Primary دارد که یکی از آن‌ها می‌تواند یک پارتیشن Extended باشد. پارتیشن Extended شامل فضای unallocated است که می‌توان داخل آن تعداد زیادی logical partition ساخت.

![](.gitbook/assets/createpartition-mbrpart.jpg)

* نحوه سازمان‌دهی partition table در MBR باعث می‌شود حداکثر فضای قابل آدرس‌دهی دیسک به 2TB محدود شود.

بنابراین، طرح پارتیشن‌بندی مبتنی بر MBR در کامپیوترهای جدید در حال جایگزین شدن با طرح GUID Partition Table یا GPT است. GPT می‌تواند در کنار MBR هم وجود داشته باشد تا نوعی backward compatibility محدود برای سیستم‌های قدیمی فراهم شود.

### GPT

GUID Partition Table (GPT) یک استاندارد برای layout جدول پارتیشن روی یک هارد دیسک فیزیکی است که از شناسه‌های یکتای سراسری (GUID) استفاده می‌کند.

هرچند GPT بخشی از استاندارد UEFI است، اما به دلیل محدودیت‌های جدول پارتیشن MBR، روی بعضی سیستم‌های BIOS هم استفاده می‌شود.

### MBR در برابر GPT

| MBR (Master Boot Record) | GPT (GUID Partition Table) |
| --- | --- |
| از سال 1983 | جدیدتر (2005-...) |
| در sector اول قرار دارد | در چند نقطه از دیسک ذخیره می‌شود |
| <p>محدود به 4 پارتیشن در هر دیسک</p><p>محدود به پارتیشن‌های 2TB</p> | <p>پشتیبانی از 128 پارتیشن در هر دیسک</p><p>پشتیبانی از پارتیشن‌های بسیار بزرگ</p> |

> دیسک‌های GPT و MBR می‌توانند basic یا dynamic باشند.

### BIOS در برابر UEFI

| BIOS (Basic Input/Output System) | UEFI (Unified Extensible Firmware Interface) |
| ------------------------------------------------ | ------------------------------------------------- |
| فقط از MBR پشتیبانی می‌کند (بنابراین GPT گاهی مثل MBR رفتار می‌کند) | پشتیبانی native از GPT |
| GPT را مثل یک drive با یک پارتیشن MBR واحد می‌بیند | معمولاً برای پشتیبانی از MBR باید در legacy mode اجرا شود |

{% hint style="success" %}
معمولاً MBR و BIOS به همراه هم **(MBR + BIOS)**، و GPT و UEFI به همراه هم **(GPT + UEFI)** می‌آیند. این برای برخی سیستم‌ها (ویندوز) الزامی است، در حالی که برای برخی دیگر (لینوکس) اختیاری است.
{% endhint %}

### Block devices

block device یک لایه abstraction برای هر device ذخیره‌سازی است که می‌تواند در blockهای با اندازه ثابت format شود و blockها باید قابلیت دسترسی تصادفی (random access) داشته باشند.

مثال‌هایی از block deviceها شامل اولین هارد IDE یا SATA روی سیستم (مثل `/dev/sda` یا `/dev/hda`) یا دومین دیسک SCSI/IDE/USB (مثل `/dev/sdb`) است. برای دیدن entryهای `/dev` می‌توانید از دستور `ls -l` استفاده کنید.

```
root@ubuntu16-1:~# ls -l  /dev/null /dev/sd[a-z] /dev/sr0 /dev/tty0
crw-rw-rw-  1 root root   1, 3 Dec  2  2018 /dev/null
brw-rw----  1 root disk   8, 0 Dec  2  2018 /dev/sda
brw-rw----+ 1 root cdrom 11, 0 Dec  2  2018 /dev/sr0
crw--w----  1 root tty    4, 0 Dec  2  2018 /dev/tty0
```

اولین کاراکتر هر خط خروجی برای **b** به معنی device از نوع **block** است (مثل floppy، CD drive، IDE hard drive یا SCSI hard drive) و برای **c** به معنی device از نوع **character** است (مثل tty یا null device).

#### پارتیشن‌بندی دیسک (Disk Partitioning)

حالا که با layoutهای هارد دیسک (MBR و GPT) آشنا شدیم، بیایید یاد بگیریم چطور با fdisk پارتیشن‌های MBR و با gdisk پارتیشن‌های GPT بسازیم.

### fdisk

`fdisk` (که گاهی format disk هم گفته می‌شود) یک ابزار متنیِ منو-محور (dialog-driven) در Linux است که برای ساختن و دستکاری جدول پارتیشن دیسک استفاده می‌شود. این ابزار برای مشاهده، ساخت، حذف، تغییر، resize، کپی و جابه‌جایی پارتیشن‌ها روی یک هارد دیسک به کار می‌رود.

fdisk به ما اجازه می‌دهد حداکثر چهار primary partition بسازیم و تعداد logical partitionها بستگی به اندازه هارد دیسکی دارد که استفاده می‌کنید. به طور کلی fdisk برای این کارها استفاده می‌شود:

* ایجاد فضا برای پارتیشن‌های جدید
* سازمان‌دهی فضا برای دیسک‌های جدید
* سازمان‌دهی مجدد دیسک‌های قدیمی
* کپی یا انتقال داده به دیسک/پارتیشن جدید

```
fdsik [options] device
or
fdisk -l [device...]
```

قبل از هر کاری با دیسک و پارتیشن‌ها، بهتر است با گزینه `-l` جزئیات پایه همه پارتیشن‌های موجود روی سیستم را ببینیم (ubuntu 16.04):

```
root@ubuntu16-1:~# fdisk -l
Disk /dev/sda: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x101c66bb

Device     Boot     Start       End   Sectors  Size Id Type
/dev/sda1  *         2048 102762495 102760448   49G 83 Linux
/dev/sda2       102764542 104855551   2091010 1021M  5 Extended
/dev/sda5       102764544 104855551   2091008 1021M 82 Linux swap / Solaris


Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

همانطور که می‌بینید دو دیسک داریم (sda و sdb). دیسک sda چند پارتیشن دارد اما sdb خام (raw) است.

* **Boot**: ستون Boot نشان می‌دهد پارتیشن اول یعنی `/dev/sda1` یک ستاره **(\*)** دارد؛ یعنی این پارتیشن فایل‌های مورد نیاز boot loader برای boot کردن سیستم را دارد.
* **Start و End**: ستون‌های start و end سکتور شروع و پایان هر پارتیشن را نشان می‌دهند.
* **Sectors/Blocks**: تعداد سکتورها/بلوک‌های تخصیص داده‌شده به پارتیشن.
* **Id و Type**: نوع پارتیشن را مشخص می‌کنند.

مشاهده پارتیشن‌ها روی یک دیسک مشخص (sda):

```
root@ubuntu16-1:~# fdisk -l /dev/sda
Disk /dev/sda: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x101c66bb

Device     Boot     Start       End   Sectors  Size Id Type
/dev/sda1  *         2048 102762495 102760448   49G 83 Linux
/dev/sda2       102764542 104855551   2091010 1021M  5 Extended
/dev/sda5       102764544 104855551   2091008 1021M 82 Linux swap / Solaris
```

حالا وارد حالت تعاملی می‌شویم و همه فرمان‌های موجود را می‌بینیم (روی sdb):

```
root@ubuntu16-1:~# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.27.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xbd67d3c2.

Command (m for help): m

Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table


Command (m for help): 
```

حالا یک پارتیشن می‌سازیم:

```
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-41943039, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-41943039, default 41943039): 

Created a new partition 1 of type 'Linux' and of size 20 GiB.

Command (m for help): 
```

بعد باید نوع پارتیشن را بر اساس استفاده‌ای که در نظر داریم مشخص کنیم:

```
Command (m for help): l

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris        
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden or  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx         
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data    
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility   
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt         
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access     
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O        
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor      
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi ea  Rufus alignment
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         eb  BeOS fs        
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ee  GPT            
 10  OPUS            55  EZ-Drive        a7  NeXTSTEP        ef  EFI (FAT-12/16/
 11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f0  Linux/PA-RISC b
 12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f1  SpeedStor      
 14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f4  SpeedStor      
 16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      f2  DOS secondary  
 17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fb  VMware VMFS    
 18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fc  VMware VMKCORE 
 1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fd  Linux raid auto
 1c  Hidden W95 FAT3 75  PC/IX           bc  Acronis FAT32 L fe  LANstep        
 1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot    ff  BBT   
```

**Partition Types**

نوع پارتیشن‌ها را می‌توان با ابزار fdisk دید و تغییر داد. یک لیست جزئی از رایج‌ترین انواع پارتیشن:

* **83: Linux**
* **82: Linux swap**
* **5: Extended**
* **8e: Linux LVM**

```
Command (m for help): t

Selected partition 1
Partition type (type L to list all types): 83
Changed type of partition 'Linux' to 'Linux'.
```

حالا با گزینه `p` جدول پارتیشن را چاپ می‌کنیم:

```
Command (m for help): p
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xbd67d3c2

Device     Boot Start      End  Sectors Size Id Type
/dev/sdb1        2048 41943039 41940992  20G 83 Linux

Command (m for help): 
```

fdisk تا زمانی که با گزینه `w` از آن نخواهیم، هیچ تغییری روی هارد دیسک نمی‌نویسد. اگر مطمئن نیستید از `q` برای خروج استفاده کنید تا هارد دیسک دست‌نخورده بماند.

```
Command (m for help): v

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

برای حذف پارتیشن از گزینه `d` استفاده کنید، اما خیلی دقت کنید.

> برای دیدن پیام راهنما و لیست همه گزینه‌ها، از `fdisk -h` استفاده کنید.

### gdisk

می‌توانیم پارتیشن‌های GPT را با gdisk مدیریت کنیم. مانند fdisk، gdisk هم یک ابزار متنیِ منو-محور برای ایجاد و دستکاری جدول پارتیشن است. این ابزار می‌تواند یک جدول پارتیشن قدیمی MBR را به فرمت جدید GPT (Globally Unique Identifier Partition Table) در حافظه تبدیل کند، یا یک جدول پارتیشن GPT را load کند.

```
root@ubuntu16-1:~# gdisk /dev/sdb
GPT fdisk (gdisk) version 1.0.1

Partition table scan:
  MBR: MBR only
  BSD: not present
  APM: not present
  GPT: not present


***************************************************************
Found invalid GPT and valid MBR; converting MBR to GPT format
in memory. THIS OPERATION IS POTENTIALLY DESTRUCTIVE! Exit by
typing 'q' if you don't want to convert your MBR partitions
to GPT format!
***************************************************************


Warning! Secondary partition table overlaps the last partition by
33 blocks!
You will need to delete this partition or resize it in another utility.

Command (? for help): ?
b	back up GPT data to a file
c	change a partition's name
d	delete a partition
i	show detailed information on a partition
l	list known partition types
n	add a new partition
o	create a new empty GUID partition table (GPT)
p	print the partition table
q	quit without saving changes
r	recovery and transformation options (experts only)
s	sort partitions
t	change a partition's type code
v	verify disk
w	write table to disk and exit
x	extra functionality (experts only)
?	print this menu

Command (? for help): q
root@ubuntu16-1:~# 
```

### parted

دستور `parted` یک partition editor است که هم با دیسک‌های MBR و هم با دیسک‌های GPT کار می‌کند.

### File System

Linux File System (یا به طور کلی هر filesystem) لایه‌ای در زیر operating system است که محل قرارگیری داده‌های شما روی storage را مدیریت می‌کند. بدون filesystem، سیستم نمی‌داند هر فایل از کجا شروع می‌شود و کجا تمام می‌شود.

### **انواع filesystem**

Linux چندین filesystem مختلف را پشتیبانی می‌کند. هر کدام نقاط قوت و ضعف و ویژگی‌های عملکردی (performance characteristics) خودشان را دارند.

**Ext, Ext2, Ext3, Ext4, JFS, XFS, btrfs and swap**

یکی از ویژگی‌های مهم یک filesystem، journaling است.

**journaling چیست؟**

journaling برای جلوگیری از خراب شدن داده (data corruption) در اثر crash یا قطع ناگهانی برق طراحی شده است. فرض کنید سیستم در حال نوشتن یک فایل روی دیسک است و ناگهان برق قطع می‌شود. بدون journal، کامپیوتر نمی‌فهمد فایل به طور کامل روی دیسک نوشته شده یا نه؛ فایل روی دیسک باقی می‌ماند اما خراب (corrupt) می‌شود.

![](.gitbook/assets/createpartition-journaling.jpg)

با داشتن journal، کامپیوتر قبل از نوشتن فایل، در journal ثبت می‌کند که قرار است فلان فایل نوشته شود، سپس فایل را روی دیسک می‌نویسد و بعد آن job را از journal حذف می‌کند. اگر برق وسط نوشتن فایل قطع شود، Linux هنگام boot شدن journal را بررسی می‌کند و jobهای نیمه‌کاره را ادامه می‌دهد. این کار جلوی از دست رفتن داده و خراب شدن فایل را می‌گیرد.

journaling مقدار کمی عملکرد نوشتن دیسک را کند می‌کند، اما برای desktop یا laptop کاملاً ارزشش را دارد.

{% hint style="success" %}
کدام filesystem برای شما مناسب‌تر است؟

به طور کلی وقتی انتخاب دارید، filesystem دارای journaling نسبت به نوع بدون journaling ترجیح داده می‌شود. همچنین ممکن است بخواهید بررسی کنید filesystem انتخابی شما از _Security Enhanced Linux_ (یا SELinux) پشتیبانی می‌کند یا نه.
{% endhint %}

در ادامه یک خلاصه کوتاه از انواعی که برای آزمون LPI باید بدانید آمده است:

| فرمت | توضیحات |
| ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p><strong>ext2</strong></p><p>(1993)</p> | <p>filesystem از نوع ext2 (که با نام <em>second extended filesystem</em> هم شناخته می‌شود) برای رفع برخی ضعف‌های filesystem مینیکس (Minix) که در نسخه‌های اولیه Linux استفاده می‌شد توسعه یافت. این نوع سال‌ها در Linux به طور گسترده استفاده شده است. ext2 journaling ندارد و تا حد زیادی جای خود را به ext3 و بعدتر ext4 داده است.</p><p></p><ol><li>حداکثر اندازه فایل <strong>16GB – 2TB</strong> است.</li></ol><p>*معمولاً روی رسانه‌های مبتنی بر Flash مثل <strong>USB Flash drive</strong> و <strong>SD Card</strong> و ... استفاده می‌شود.</p>             |
| <p><strong>ext3</strong></p><p>(2001)</p> | <p>ext3 قابلیت journaling را به filesystem استاندارد ext2 اضافه می‌کند و در واقع یک تکامل تدریجی برای یک filesystem بسیار پایدار است. در بیشتر شرایط عملکرد مناسبی دارد و همچنان بهبود پیدا می‌کند. چون journaling را روی ext2ِ امتحان‌پس‌داده اضافه می‌کند، امکان تبدیل ext2 موجود به ext3 و حتی بازگردانی به ext2 (در صورت نیاز) وجود دارد.</p><ol><li>حداکثر اندازه فایل <strong>16GB – 2TB</strong>.</li><li>با ویژگی journaling در <strong>Kernel 2.4.15</strong> ادغام شد.</li></ol> |
| <p><strong>ext4</strong></p><p>(2008)</p> | <p>ext4 به عنوان توسعه‌ای از ext3 شروع شد تا نیاز filesystemهای بسیار بزرگ را با افزایش محدودیت‌های ذخیره‌سازی و بهبود عملکرد پوشش دهد. برخی تغییرات نسبت به ext3:</p><ol><li>حداکثر اندازه فایل از <strong>16GB</strong> تا <strong>16TB</strong>.</li><li>در <strong>kernel 2.6.28</strong> اضافه شد.</li><li>امکان <strong>خاموش کردن</strong> journaling.</li><li>ویژگی‌های دیگر مانند <strong>Fast FSCK</strong> و ...</li></ol>                                                                             |
| **ReiserFS**                              | ReiserFS یک filesystem مبتنی بر B-tree است که عملکرد کلی بسیار خوبی دارد، مخصوصاً برای تعداد زیاد فایل‌های کوچک. journaling دارد. توسعه فعال آن متوقف شده، از SELinux پشتیبانی نمی‌کند و تا حد زیادی با Reiser4 (که آینده‌اش نامشخص است) جایگزین شده است. |
| **XFS**                                   | XFS یک filesystem دارای journaling است. قابلیت‌های robust دارد و برای scalability بهینه شده است. XFS داده‌های در حال انتقال را به شکل تهاجمی در RAM cache می‌کند؛ اگر UPS دارید عالی است. |
| **btrfs**                                 | btrfs (B-Tree file system) ابتدا توسط Oracle (GPL) توسعه یافت. یک filesystem جدید copy-on-write برای Linux است که روی ویژگی‌های پیشرفته (snapshots، compression و ...) تمرکز دارد و همزمان fault tolerance، تعمیرپذیری (repair) و مدیریت آسان را هدف گرفته است. برای کار با فایل‌های بزرگ و filesystemهای چند-device طراحی شده است. |
| **vfat**                                  | <p>(که با نام <em>FAT32</em> هم شناخته می‌شود) journaling ندارد و بسیاری از قابلیت‌های لازم برای یک Linux filesystem کامل را ندارد. برای تبادل داده بین Windows و Linux مفید است. این filesystem را <strong>استفاده نکنید</strong> مگر برای اشتراک‌گذاری داده.</p><p>*اگر یک آرشیو Linux را روی دیسک vfat unzip یا untar کنید، مجوزها (مثل execute) و همچنین symbolic linkهای داخل آرشیو را از دست خواهید داد.</p>                                                                                          |
| **swap**                                  | فضای swap باید برای استفاده به عنوان swap format شود، اما معمولاً به عنوان یک filesystem در نظر گرفته نمی‌شود. |

قبل از اینکه بتوانید از هر device ذخیره‌سازی در Linux استفاده کنید، باید روی آن filesystem بسازید.

### partitioning

Linux از دستور `mkfs` برای ساخت filesystem و از `mkswap` برای ساخت swap space استفاده می‌کند.

{% hint style="danger" %}
قبل از شروع تغییر دادن پارتیشن‌ها، چند نکته مهم را به خاطر داشته باشید. اگر این موارد را رعایت نکنید ممکن است داده‌های فعلی خود را از دست بدهید:

1. قبل از شروع، از داده‌های مهم backup بگیرید.
2. پارتیشن‌هایی که در حال استفاده هستند را تغییر ندهید.
3. ابزار خود را بشناسید.
4. اگر اشتباه کردید، متوقف شوید.
{% endhint %}

Linux از دستور `mkfs` برای ساخت filesystem و از `mkswap` برای ساخت swap space استفاده می‌کند.

### mkfs

دستور mkfs (make filesystem) برای ایجاد یک filesystem استفاده می‌شود.

`mkfs` در واقع یک front end برای چندین دستور مخصوص filesystem است؛ مثل `mkfs.ext3` برای ext3، `mkfs.ext4` برای ext4 و `mkfs.btrfs` برای btrfs.

```
root@ubuntu16-1:~# mkfs
mkfifo        mkfs          mkfs.ext2     mkfs.ext4dev  mkfs.msdos
mkfontdir     mkfs.bfs      mkfs.ext3     mkfs.fat      mkfs.ntfs
mkfontscale   mkfs.cramfs   mkfs.ext4     mkfs.minix    mkfs.vfat

root@ubuntu16-1:~# ls /sbin/mk*
/sbin/mkfs         /sbin/mkfs.ext2  /sbin/mkfs.ext4dev  /sbin/mkfs.msdos
/sbin/mkfs.bfs     /sbin/mkfs.ext3  /sbin/mkfs.fat      /sbin/mkfs.ntfs
/sbin/mkfs.cramfs  /sbin/mkfs.ext4  /sbin/mkfs.minix    /sbin/mkfs.vfat

```

برخی شکل‌های مختلف یک دستور: `mke2fs`، `mkfs.ext2` و `mkfs.ext3` در واقع همان فایل هستند؛ در حالی که `mkfs.msdos` و `mkfs.vfat` معمولاً symbolic link به `mkdosfs` هستند.

برای ساخت filesystem با mkfs، آرگومان‌های لازم عبارت‌اند از filesystem-type و device-filename:

```
mkfs [options] [-t type fs-options] device [size]
```

یا می‌توانیم از دستور `mkfs._fstype_` استفاده کنیم (مثلاً `mkfs.ext3 /dev/sdb1`) یا از `mkfs` همراه با گزینه `-t` برای تعیین فرمت استفاده کنیم (مثلاً `mkfs -t ext3 /dev/sdb1`). نتیجه هر دو یکی است.

```
root@ubuntu16-1:~# mkfs -t ext3 /dev/sdb1
mke2fs 1.42.13 (17-May-2015)
Creating filesystem with 5242624 4k blocks and 1310720 inodes
Filesystem UUID: fb1e0a9a-04c3-4985-80e9-132f01731c9f
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done  c

root@ubuntu16-1:~# 
```

اگر بخواهیم هنگام فرمت کردن به پارتیشن label بدهیم، باید از `-L labelname` استفاده کنیم؛ مثلاً: `mkfs -t ext3 -L MyData /dev/sdb1`

{% hint style="info" %}
برای mount کردن پارتیشن format شده می‌توانیم از label یا از **UUID** استفاده کنیم. **UUID** یک شناسه یکتا برای شناسایی پارتیشن‌ها است. برای گرفتن UUID پارتیشن تازه ساخته‌شده، این را امتحان کنید: `blkid /dev/sdb1`
{% endhint %}

| پارتیشن | نوع فرمت | نمونه دستور | نکته |
| --------- | ----------- | --------------------------------- | ----------------------------------------------------------------- |
| /dev/sdb1 | ext4        | mkfs -t ext4 -L data /dev/sdb1    | label را تنظیم می‌کند (معادل mkfs.ext4) |
| /dev/sdb2 | xfs         | mkfs -t xfs -i size=512 /dev/sdb2 | inode بزرگ‌تر (پیش‌فرض 256)؛ می‌تواند به SELinux کمک کند |
| /dev/sdc1 | ReiserFS    | mkfs -t reiserfs /dev/sdc1        | یا می‌توانید از `mkreiserfs` استفاده کنید |
| /dev/sdc2 | vFAT        | mkfs -t vfat /dev/sdc2            | یا می‌توانید از `mkfs.vfat` استفاده کنید |
| /dev/sdc3 | Btrfs       | mkfs -t btrfs /dev/sdc3           | یا می‌توانید از `mkfs.btrfs` استفاده کنید |

### mkswap

دستور `mkswap` روی یک device یا یک فایل، swap space می‌سازد.

```
mkswap [options] device [size]
```

آرگومان device معمولاً یک disk partition است (مثل `/dev/sdb1`) اما می‌تواند یک فایل هم باشد.

kernel در Linux به partition ID نگاه نمی‌کند، اما بسیاری از اسکریپت‌های نصب فرض می‌کنند پارتیشن‌های با نوع hex برابر 82 (LINUX_SWAP) برای swap هستند.

پس برای ساخت swap space، ابتدا با fdisk یک پارتیشن از نوع 82 بسازید و سپس `mkswap` را اجرا کنید:

```
root@ubuntu16-1:/# mkswap /dev/sdb1
mkswap: /dev/sdb1: warning: wiping old ext3 signature.
Setting up swapspace version 1, size = 20 GiB (21473783808 bytes)
no label, UUID=0dcd7e90-4b45-4d5f-808c-320f1e5ba8a3
```

برای استفاده از پارتیشن ساخته‌شده به عنوان swap، مراحل دیگری لازم است که در درس‌های بعدی گفته خواهد شد.

تمام!

.

.

.

[https://wiki.manjaro.org/index.php?title=Some_basics_of_MBR_v/s_GPT_and_BIOS_v/s_UEFI](https://wiki.manjaro.org/index.php?title=Some_basics_of_MBR_v/s_GPT_and_BIOS_v/s_UEFI)

[https://developer.ibm.com/tutorials/l-lpic1-104-1/](https://developer.ibm.com/tutorials/l-lpic1-104-1/)

[https://www.geeksforgeeks.org/file-systems-in-operating-system/](https://www.geeksforgeeks.org/file-systems-in-operating-system/)

[https://likegeeks.com/linux-file-system/](https://likegeeks.com/linux-file-system/)

[https://developer.ibm.com/tutorials/l-lpic1-102-1/](https://developer.ibm.com/tutorials/l-lpic1-102-1/)

[https://www.computerhope.com/jargon/p/partition.htm](https://www.computerhope.com/jargon/p/partition.htm)

[https://www.geeksforgeeks.org/fdisk-command-in-linux-with-examples/](https://www.geeksforgeeks.org/fdisk-command-in-linux-with-examples/)

[https://www.tecmint.com/what-is-ext2-ext3-ext4-and-how-to-create-and-convert-linux-file-systems/](https://www.tecmint.com/what-is-ext2-ext3-ext4-and-how-to-create-and-convert-linux-file-systems/)

[https://www.howtogeek.com/howto/33552/htg-explains-which-linux-file-system-should-you-choose/](https://www.howtogeek.com/howto/33552/htg-explains-which-linux-file-system-should-you-choose/)

[https://developer.ibm.com/tutorials/l-lpic1-104-1/](https://developer.ibm.com/tutorials/l-lpic1-104-1/)

[https://jadi.gitbooks.io/lpic1/content/1041\_create_partitions_and_filesystems.html](https://jadi.gitbooks.io/lpic1/content/1041\_create_partitions_and_filesystems.html)

.
