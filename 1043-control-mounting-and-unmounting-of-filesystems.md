# 104.3. کنترل mounting و unmounting مربوط به filesystems

**وزن:**3

**توضیحات:**داوطلبان باید قادر به پیکربندی مانت کردن (mounting) یک سیستم‌فایل باشند.

**حوزه‌های کلیدی دانش:**

* مانت کردن و آن‌مانت کردن (unmount) سیستم‌فایل‌ها به صورت دستی
* پیکربندی مانت شدن سیستم‌فایل هنگام بوت شدن سیستم
* پیکربندی سیستم‌فایل‌های جداشدنی (removable) قابل مانت توسط کاربر

**اصطلاحات و ابزارهای کاربردی:**

* /etc/fstab
* /media/
* mount
* umount

در درس‌های قبلی یاد گرفتیم که چگونه هارد درایوها را پارتیشن‌بندی کنیم و چگونه سیستم‌فایل را روی آن پارتیشن‌ها فرمت کنیم. در این بخش یاد خواهیم گرفت که چگونه سیستم‌فایل‌ها را مانت و آن‌مانت کنیم تا بتوانیم به آن‌ها دسترسی داشته باشیم، فایل‌ها را در هارد درایو واقعی ذخیره کرده و از آن بازیابی کنیم.

### سیستم‌فایل‌های لینوکس (Linux filesystems) <a href="linux-filesystems" id="linux-filesystems"></a>

سیستم‌فایل لینوکس یک درخت بزرگ با ریشه در / است، با این حال ما سیستم‌فایل‌هایی روی دستگاه‌ها و پارتیشن‌های مختلف داریم. چگونه این ناسازگاری ظاهری را حل کنیم؟

سیستم‌فایل ریشه (/) به عنوان بخشی از فرآیند مقداردهی اولیه مانت می‌شود. هر یک از سیستم‌فایل‌های دیگری که ایجاد می‌کنید تا زمانی که در یک **نقطه اتصال (mount point)** _مانت_ نشوند، توسط سیستم لینوکس قابل استفاده نیستند.

## مانت کردن و آن‌مانت کردن (mounting and unmounting)

دستور **mount** برای مانت کردن سیستم‌فایل موجود روی یک دستگاه به ساختار درختی بزرگ (سیستم‌فایل لینوکس) با ریشه در ‘/‘ استفاده می‌شود. در مقابل، دستور دیگری به نام **umount** می‌تواند برای جدا کردن این دستگاه‌ها از درخت استفاده شود.

### mount

```
mount -t type device location
```

> در واقع مکانی که ما به عنوان مکان مشخص می‌کنیم، همان نقطه اتصال (mount point) است. نقطه اتصال باید وجود داشته باشد در غیر این صورت دستگاه نمی‌تواند مانت شود.

با گزینه t- می‌توانیم نوع سیستم‌فایل را مشخص کنیم، هرچند می‌توان آن را حذف کرد زیرا دستور mount به اندازه کافی هوشمند است که نوع سیستم‌فایل را تشخیص دهد! (اوبونتو 16.4):

بیایید دایرکتوری به نام "mydisk" را به عنوان یک نقطه اتصال ایجاد کرده و dev/sdb1/ را روی آن مانت کنیم:

```
root@ubuntu16-1:/mnt# pwd
/mnt

root@ubuntu16-1:/mnt# mkdir mydisk

root@ubuntu16-1:/mnt# mount -t ext4 /dev/sdb1 /mnt/mydisk

root@ubuntu16-1:/mnt# cd mydisk/

root@ubuntu16-1:~/mydisk# ls
file1.txt  lost+found

root@ubuntu16-1:/mnt/mydisk# cat file1.txt 
 this text file is on my hard disk!

```

{% hint style="info" %}
وقتی یک سیستم‌فایل را روی یک دایرکتوری موجود مانت می‌کنید، فایل‌های موجود در سیستم‌فایلی که مانت می‌کنید به فایل‌ها و زیردایرکتوری‌های آن نقطه اتصال تبدیل می‌شوند. اگر دایرکتوری نقطه اتصال از قبل حاوی فایل‌ها یا زیردایرکتوری‌هایی بود، آن‌ها از بین نمی‌روند، اما تا زمانی که سیستم‌فایل مانت شده آن‌مانت نشود، دیگر قابل مشاهده نیستند و در آن زمان دوباره قابل مشاهده می‌شوند. ایده خوبی است که با استفاده از دایرکتوری‌های خالی به عنوان نقاط اتصال، از این مشکل جلوگیری کنید.
{% endhint %}

دستور mount بدون هیچ آرگومانی تمام مانت‌های موجود در سیستم را نمایش می‌دهد:

```
root@ubuntu16-1:~# mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=462796k,nr_inodes=115699,mode=755)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,noexec,relatime,size=98508k,mode=755)
/dev/sda1 on / type ext4 (rw,relatime,errors=remount-ro,data=ordered)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
tmpfs on /run/lock type tmpfs (rw,nosuid,nodev,noexec,relatime,size=5120k)
tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/lib/systemd/systemd-cgroups-agent,name=systemd)
pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls,net_prio)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
cgroup on /sys/fs/cgroup/rdma type cgroup (rw,nosuid,nodev,noexec,relatime,rdma)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=28,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=19700)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,pagesize=2M)
mqueue on /dev/mqueue type mqueue (rw,relatime)
debugfs on /sys/kernel/debug type debugfs (rw,relatime)
configfs on /sys/kernel/config type configfs (rw,relatime)
fusectl on /sys/fs/fuse/connections type fusectl (rw,relatime)
binfmt_misc on /proc/sys/fs/binfmt_misc type binfmt_misc (rw,relatime)
vmware-vmblock on /run/vmblock-fuse type fuse.vmware-vmblock (rw,nosuid,nodev,relatime,user_id=0,group_id=0,default_permissions,allow_other)
tmpfs on /run/user/1001 type tmpfs (rw,nosuid,nodev,relatime,size=98508k,mode=700,uid=1001,gid=1001)
gvfsd-fuse on /run/user/1001/gvfs type fuse.gvfsd-fuse (rw,nosuid,nodev,relatime,user_id=1001,group_id=1001)
/dev/sr0 on /media/user1/Fedora-WS-Live-28-1-1 type iso9660 (ro,nosuid,nodev,relatime,nojoliet,check=s,map=n,blocksize=2048,uid=1001,gid=1001,dmode=500,fmode=400,uhelper=udisks2)
/dev/sdb1 on /mnt/mydisk type ext4 (rw,relatime,data=ordered)
```

#### گزینه‌های mount

دستور `mount` چندین گزینه دارد که رفتار پیش‌فرض را لغو می‌کنند. برای مثال، ما می‌توانیم یک سیستم‌فایل را با تعیین `-o ro` به صورت فقط-خواندنی مانت کنیم. اگر سیستم‌فایل قبلاً مانت شده است، `remount:` را اضافه کنید:

```
root@ubuntu16-1:/mnt# mount -o remount,ro /dev/sdb1 /mnt/mydisk/
root@ubuntu16-1:/mnt# cd /mnt/mydisk/
root@ubuntu16-1:/mnt/mydisk# ls
file1.txt  lost+found
root@ubuntu16-1:/mnt/mydisk# touch file2
touch: cannot touch 'file2': Read-only file system
```

> `نکات:`

> * از کاما برای جدا کردن چندین گزینه استفاده کنید، مانند `remount` و `ro`.
> * هنگام مانت کردن مجدد (remounting) یک سیستم‌فایل که قبلاً مانت شده است، کافی است یا نقطه اتصال یا نام دستگاه را مشخص کنید. نیازی به مشخص کردن هر دو نیست.
> * شما نمی‌توانید یک سیستم‌فایل فقط-خواندنی را به صورت خواندنی-نوشتنی مانت کنید. رسانه‌هایی که نمی‌توانند اصلاح شوند، مانند دیسک‌های CD-ROM، به طور خودکار به صورت فقط-خواندنی مانت می‌شوند.
> * برای مانت کردن مجدد یک دستگاه قابل نوشتن به صورت خواندنی-نوشتنی، `-o remount,rw` را مشخص کنید.

| mount command examples                 | description                                                                |
| -------------------------------------- | -------------------------------------------------------------------------- |
| mount -l                               | Add the ext2, ext3 and XFS labels in the mount output.                     |
| mount -r /dev/sdb1 /mydisk             | Mount the file system read-only. Same as -o ro                             |
| mount -r /dev/sdb1 /mydisk             | Mount the file system read/write. This is the default. It is same as -o rw |
| mount -L mydata /mydisk/               | Mount the partition that has the specified label                           |
| mount -U uuid /mydisk                  | Mount the partition that has the specified uuid                            |
| mount -t iso9660 -o ro /dev/cdrom /mnt | Mount a CD-ROM(just in case system didn't that)                            |
| mount -o loop /mydisk.iso /mnt         | Mount an iso image                                                         |

 ` mount -B /mydisk /mysecondplace` :After mounting to a directory the mount point can be changed. We will provide the current mounted point and new mount point in a row with `-B` parameter. This actually does not removes old mount only adds the new directory as the mount.

> All mounts that we create are not permanent and get vanished as system restarted. In order to make a them permanent we need to edit /etc/fstab file. 

### umount

زمانی که یک سیستم فایل مانت شد، می‌توانیم از دستور umount (بدون "n") برای آن‌مانت کردن سیستم فایل استفاده کنیم. ما می‌توانیم سیستم فایل را با استفاده از umount به همراه دستگاه یا نقطه اتصال آن‌مانت کنیم.

```
umount device
OR
umount mount-point
```

>  Note that a file system cannot be unmounted when it is busy - for example, when there are open files on it, or when some process has its working directory there, or when a swap file on it is in use.

 بیایید به عنوان مثال dev/sdb1/ را آن‌مانت کنیم:

```
umount /dev/sdb1
```

### /etc/fstab

در بخش‌های قبلی درباره مدیران بوت یاد گرفتیم که پارامتر =root در هر دو GRUB و LILO به بوت‌لودر می‌گوید که چه سیستم‌فایلی باید به عنوان ریشه مانت شود. برای GRUB2، این دستور `setroot` است. هنگامی که سیستم‌فایل ریشه مانت شد، فرآیند مقداردهی اولیه دستور `mount` را با گزینه `-a` اجرا می‌کند تا مجموعه‌ای از سیستم‌فایل‌ها را به طور خودکار مانت کند. این مجموعه در فایل etc/fstab/ مشخص شده است.

Fstab جدول سیستم‌فایل سیستم‌عامل است:

```
root@ubuntu16-1:~# cat /etc/fstab 

# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=142a64e5-96f3-4789-9c91-1dc1570057b7 /               ext4    errors=remount-ro 0       1
# swap was on /dev/sda5 during installation
UUID=b4801c8b-ca75-4548-8697-182d1b6d895c none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
```

*  **file system :**این می‌تواند نام یک دستگاه مانند /dev/sda1، یا یک برچسب (LABEL=) یا UUID (UUID=) باشد. 

> > ما می‌توانیم با استفاده از نام دستگاه، برچسب‌ها یا uuid مانت کنیم. کدام یک بهتر است؟ هر کسی می‌تواند هر برچسبی را که می‌خواهد تنظیم کند، نام دستگاه ممکن است تغییر کند مثلاً اگر دو دستگاه usb متصل کنید، بنابراین بهترین راه استفاده از uuid برای مانت کردن است (از دستور `blkid `command).

*  **mount point : **نقطه اتصال، اما برای فضای swap، این باید مقدار ‘none’ یا ‘swap’ باشد.
*  **type : **نوع سیستم‌فایل را مشخص می‌کند. it can be (**ext2,3,4, reiserfs,swap, vfat and ntfs,ISO9660,auto**)
* **option** : گزینه‌های مانت را مشخص می‌کند. Specify defaults if you want default mount options.

{% hint style="info" %}
* sync/async - All I/O to the file system should be done (a)synchronously.
* auto - The filesystem can be mounted automatically (at bootup, or when mount is passed the -a option). This is really unnecessary as this is the default action of mount -a anyway.
* noauto - The filesystem will NOT be automatically mounted at startup, or when mount passed -a. You must explicitly mount the filesystem.
* dev/nodev - Interpret/Do not interpret character or block special devices on the file system.
* exec / noexec - Permit/Prevent the execution of binaries from the filesystem.
* suid/nosuid - Permit/Block the operation of suid, and sgid bits.
* ro - Mount read-only.
* rw - Mount read-write.
* user - Permit any user to mount the filesystem. This automatically implies noexec, nosuid,nodev unless overridden.
* nouser - Only permit root to mount the filesystem. This is also a default setting.
* defaults - Use default settings. Equivalent to rw, suid, dev, exec, auto, nouser, async.
* \_netdev - this is a network device, mount it after bringing up the network. Only valid with fstype nfs.
{% endhint %}

> note1: User-mounted filesystems default to noexec unless exec is specified afteruser.
>
> note2: noatime will disable recording of access times. Not using access times may improve performance.

* **dump: **Specifies whether the dump command should consider this ext2 or ext3 filesystem for backups. A value of 0 tells dump to ignore this filesystem.
* **pass: **Non-zero values of pass specify the order of checking filesystems at boot time,( based on check interval and mount count, try `tune2fs -l` ).

Lets add /dev/sdb1 to the /etc/fstab file using UUID  and have it even after reboot:

```
root@ubuntu16-1:~# blkid
/dev/sdb1: LABEL="mydata" UUID="03c28ca3-daa3-49c2-9bc4-b083c3b0957b" TYPE="ext4" PARTUUID="4d71bc84-01"
/dev/sda1: LABEL="myroot" UUID="142a64e5-96f3-4789-9c91-1dc1570057b7" TYPE="ext4" PARTUUID="101c66bb-01"
/dev/sda5: UUID="b4801c8b-ca75-4548-8697-182d1b6d895c" TYPE="swap" PARTUUID="101c66bb-05"
/dev/sr0: UUID="2018-04-25-06-43-09-00" LABEL="Fedora-WS-Live-28-1-1" TYPE="iso9660" PTUUID="3a663a44" PTTYPE="dos"

root@ubuntu16-1:~# vim /etc/fstab 
```

```
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=142a64e5-96f3-4789-9c91-1dc1570057b7 /               ext4    errors=remount-ro 0       1
# swap was on /dev/sda5 during installation
UUID=b4801c8b-ca75-4548-8697-182d1b6d895c none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
### added by root! 
UUID=03c28ca3-daa3-49c2-9bc4-b083c3b0957b /mnt/mydisk  auto defaults   0 0

```

اگر هر چیزی در etc/fstab/ اشتباه پیش برود، می‌تواند باعث شکست سیستم شود و سیستم بوت نشود! بنابراین دستور `mount -a` را امتحان کنید تا قبل از ری‌بوت کردن سیستم، تمام موارد fstab را مانت کنید و خطاها را بررسی کنید.

```
root@ubuntu16-1:~# mount -a
mount: wrong fs type, bad option, bad superblock on /dev/sdb1,
       missing codepage or helper program, or other error

       In some cases useful info is found in syslog - try
       dmesg | tail or so.
```

اوه!defaults است نه default، پس سعی کنید این خط را دوباره در fstab ویرایش کنید:

```
### added by root! 
UUID=03c28ca3-daa3-49c2-9bc4-b083c3b0957b /mnt/mydisk  auto defaults   0 0
```

و کار تمام است:

```
root@ubuntu16-1:~# mount -a
root@ubuntu16-1:~# cd /mnt/mydisk/
root@ubuntu16-1:/mnt/mydisk# ls
file1.txt  file2.txt  lost+found
```

کاربران فقط می‌توانند چیزهایی را مانت و آن‌مانت کنند که در etc/fstab/ مجاز شده‌اند!

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-3/](https://developer.ibm.com/tutorials/l-lpic1-104-3/)

[https://www.geeksforgeeks.org/mount-command-in-linux-with-examples/](https://www.geeksforgeeks.org/mount-command-in-linux-with-examples/)

[https://www.sanfoundry.com/mount-command-usage-example/](https://www.sanfoundry.com/mount-command-usage-example/)

[https://www.poftut.com/linux-mount-command-tutorial-examples/](https://www.poftut.com/linux-mount-command-tutorial-examples/)

[https://www.thegeekdiary.com/how-to-mount-and-umount-a-file-system-in-linux/](https://www.thegeekdiary.com/how-to-mount-and-umount-a-file-system-in-linux/)

[https://help.ubuntu.com/community/Fstab](https://help.ubuntu.com/community/Fstab)

[https://linoxide.com/file-system/understanding-each-entry-of-linux-fstab-etcfstab-file/](https://linoxide.com/file-system/understanding-each-entry-of-linux-fstab-etcfstab-file/)

.
