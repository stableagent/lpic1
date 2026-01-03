# 102.2 نصب یک مدیر بوت (boot manager)

## **102.2 نصب یک مدیر بوت (boot manager)**

**وزن:** 2

**توضیحات:** داوطلبان باید قادر به انتخاب، نصب و پیکربندی یک مدیر بوت باشند.

**حوزه‌های کلیدی دانش:**

* ارائه مکان‌های بوت جایگزین و گزینه‌های بوت پشتیبان
* نصب و پیکربندی یک boot loader مانند GRUB Legacy
* انجام تغییرات پیکربندی اساسی برای GRUB 2
* تعامل با boot loader

**در اینجا لیستی جزئی از فایل‌ها، اصطلاحات و ابزارهای مورد استفاده آمده است:**

* menu.lst، grub.cfg و grub.conf
* grub-install
* grub-mkconfig
* MBR

ما در مورد فرآیند بوت لینوکس و boot loaderها صحبت کرده‌ایم. با LILO به عنوان یک boot loader بسیار قدیمی آشنا شدیم که در اواخر دهه 1990 با GRUB جایگزین شد. در این درس نگاه دقیق‌تری به boot loaderهای GRUB و GRUB2 می‌اندازیم.

## GRUB

![](.gitbook/assets/Grub_logo.png)![](.gitbook/assets/Grub_logo2.png)

GRUB (GRand Unified Bootloader) یک پکیج boot loader است که برای پشتیبانی از چندین سیستم‌عامل توسعه یافته و به کاربر اجازه می‌دهد در حین بوت شدن یکی از آن‌ها را انتخاب کند.

#### نسخه‌های GRUB

GRUB توسط Erich Stefan Boleyn ایجاد شد و تحت پروژه GNU با نام GNU GRUB بیشتر توسعه یافت. پکیج اصلی هنوز برای دانلود در دسترس است اما دیگر توسعه نمی‌یابد.

**GRUB2** جایگزین چیزی شده است که قبلاً با نام GRUB (یعنی نسخه 0.9x) شناخته می‌شد، که به نوبه خود به **GRUB Legacy** تبدیل شده است. بهبودهای GRUB2 هنوز در حال انجام است، اما نسخه‌های منتشر شده فعلی برای عملیات عادی کاملاً قابل استفاده هستند.

### GRUB چگونه کار می‌کند؟

هنگامی که یک کامپیوتر بوت می‌شود، BIOS کنترل را به اولین دستگاه بوت منتقل می‌کند که می‌تواند هارد دیسک، فلاپی دیسک، CD-ROM یا هر دستگاه دیگری باشد که توسط BIOS شناسایی شده است.

#### MBR

اولین سکتور روی هارد، رکورد راه انداز اصلی (Master Boot Record یا MBR) نامیده می‌شود. این سکتور تنها 512 بایت طول دارد و شامل یک قطعه کد کوچک (446 بایت) به نام primary boot loader و جدول پارتیشن (partition table) (64 بایت) است که پارتیشن‌های اصلی و گسترده را توصیف می‌کند.

![](.gitbook/assets/bootloader-mbr.jpg)

GRUB کد پیش‌فرض MBR را با کد خود جایگزین می‌کند:

```
[root@centos7-1 ~]# xxd -l 512 /dev/sda
```

![](.gitbook/assets/mbr_color2.jpg)

به طور پیش‌فرض، کد MBR به دنبال پارتیشن علامت‌گذاری شده به عنوان active می‌گردد و به محض یافتن چنین پارتیشنی، سکتور بوت آن را در حافظه بارگذاری کرده و کنترل را به آن منتقل می‌کند.

```
[root@centos7-1 ~]# fdisk -l /dev/sda 

Disk /dev/sda: 53.7 GB, 53687091200 bytes, 104857600 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000a1a5f

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200   104857599    51379200   8e  Linux LVM
```

علاوه بر این، GRUB در مراحلی (stages) کار می‌کند.

* **Stage 1** در MBR قرار دارد و عمدتاً به Stage 2 اشاره می‌کند، زیرا MBR برای قرار دادن تمام داده‌های مورد نیاز بسیار کوچک است.
* **Stage 2** به فایل پیکربندی خود اشاره می‌کند که شامل تمام رابط‌های کاربری پیچیده و گزینه‌هایی است که معمولاً هنگام صحبت در مورد GRUB با آن‌ها آشنا هستیم. Stage 2 می‌تواند در هر جای دیسک قرار داشته باشد. اگر Stage 2 نتواند جدول پیکربندی خود را پیدا کند، GRUB توالی بوت را متوقف کرده و خط فرمان را برای پیکربندی دستی به کاربر ارائه می‌دهد.

معماری مرحله‌ای (Stage architecture) به GRUB اجازه می‌دهد تا در مقایسه با اکثر بوت‌لودرها که برای جا گرفتن در محدودیت‌های جدول پارتیشن، کم‌حجم و ساده هستند، حجیم (~20-30K) و در نتیجه نسبتاً پیچیده و بسیار قابل پیکربندی باشد.

### GRUB Legacy در مقابل GRUB2

بیایید یک تصویر بزرگ ترسیم کنیم:

![](.gitbook/assets/bootloader-grubvsgrub2.jpg)

/boot/grub/menu.lst در GRUB Legacy با /boot/grub/grub.cfg در GRUB2 جایگزین شده است.

```
### GRUB Legacy
[root@centos5-1 ~]# ls /boot
config-2.6.18-398.el5      message
grub                       symvers-2.6.18-398.el5.gz
initrd-2.6.18-398.el5.img  System.map-2.6.18-398.el5
lost+found                 vmlinuz-2.6.18-398.el5

[root@centos5-1 ~]# ls /boot/grub/
device.map     grub.conf         minix_stage1_5     stage2
e2fs_stage1_5  iso9660_stage1_5  reiserfs_stage1_5  ufs2_stage1_5
fat_stage1_5   jfs_stage1_5      splash.xpm.gz      vstafs_stage1_5
ffs_stage1_5   menu.lst          stage1             xfs_stage1_5
##############################################################
### GRUB2
[root@centos7-1 ~]# ls /boot/
config-3.10.0-693.el7.x86_64
efi
grub
grub2
initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
initramfs-3.10.0-693.el7.x86_64.img
initramfs-3.10.0-693.el7.x86_64kdump.img
initrd-plymouth.img
symvers-3.10.0-693.el7.x86_64.gz
System.map-3.10.0-693.el7.x86_64
vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e
vmlinuz-3.10.0-693.el7.x86_64
[root@centos7-1 ~]# ls /boot/grub
splash.xpm.gz

[root@centos7-1 ~]# ls /boot/grub2
device.map  fonts  grub.cfg  grubenv  i386-pc  locale
```

کدام نسخه از لینوکس از کدام grub استفاده می‌کند؟

![](.gitbook/assets/bootloader-grubdistro.jpg)

**نمایش**

به منظور نشان دادن تفاوت‌های بین GRUB Legacy و GRUB2، بیایید پارامتر `timeout` را در دو سیستم (CentOS 5 و CentOS 7) تغییر دهیم:

### menu.lst (GRUB Legacy)

```
[root@centos5-1 ~]# cat /boot/grub/menu.lst 
# grub.conf generated by anaconda
#
# Note that you do not have to rerun grub after making changes to this file
# NOTICE:  You have a /boot partition.  This means that
#          all kernel and initrd paths are relative to /boot/, eg.
#          root (hd0,0)
#          kernel /vmlinuz-version ro root=/dev/sda2
#          initrd /initrd-version.img
#boot=/dev/sda
default=0
timeout=5
splashimage=(hd0,0)/grub/splash.xpm.gz
hiddenmenu
title CentOS (2.6.18-398.el5)
        root (hd0,0)
        kernel /vmlinuz-2.6.18-398.el5 ro root=LABEL=/ rhgb quiet
        initrd /initrd-2.6.18-398.el5.img
```

فقط یک تغییر در menu.lst ایجاد کنید و آن را ذخیره کنید و تمام.

### grub.cfg (GRUB2)

فایل **grub.cfg** توسط برخی به‌روزرسانی‌های پکیج Grub 2، هر زمان که هسته‌ای اضافه یا حذف شود، یا زمانی که کاربر دستور update-grub را اجرا کند، بازنویسی می‌شود. **فایل grub.cfg را مستقیماً ویرایش نکنید!!**

```
[root@centos7-1 ~]# cat /boot/grub2/grub.cfg 
#
# DO NOT EDIT THIS FILE
#
# It is automatically generated by grub2-mkconfig using templates
# from /etc/grub.d and settings from /etc/default/grub
#

### BEGIN /etc/grub.d/00_header ###
set pager=1

if [ -s $prefix/grubenv ]; then
  load_env
fi
if [ "${next_entry}" ] ; then
   set default="${next_entry}"
   set next_entry=
   save_env next_entry
   set boot_once=true
else
   set default="${saved_entry}"
fi

if [ x"${feature_menuentry_id}" = xy ]; then
  menuentry_id_option="--id"
else
  menuentry_id_option=""
fi

export menuentry_id_option

if [ "${prev_saved_entry}" ]; then
  set saved_entry="${prev_saved_entry}"
  save_env saved_entry
  set prev_saved_entry=
  save_env prev_saved_entry
  set boot_once=true
fi

function savedefault {
  if [ -z "${boot_once}" ]; then
    saved_entry="${chosen}"
    save_env saved_entry
  fi
}

function load_video {
  if [ x$feature_all_video_module = xy ]; then
    insmod all_video
  else
    insmod efi_gop
    insmod efi_uga
    insmod ieee1275_fb
    insmod vbe
    insmod vga
    insmod video_bochs
    insmod video_cirrus
  fi
}

terminal_output console
if [ x$feature_timeout_style = xy ] ; then
  set timeout_style=menu
  set timeout=5
# Fallback normal timeout code in case the timeout_style feature is
# unavailable.
else
  set timeout=5
fi
### END /etc/grub.d/00_header ###

### BEGIN /etc/grub.d/00_tuned ###
set tuned_params=""
set tuned_initrd=""
### END /etc/grub.d/00_tuned ###

### BEGIN /etc/grub.d/01_users ###
if [ -f ${prefix}/user.cfg ]; then
  source ${prefix}/user.cfg
  if [ -n "${GRUB2_PASSWORD}" ]; then
    set superusers="root"
    export superusers
    password_pbkdf2 root ${GRUB2_PASSWORD}
  fi
fi
### END /etc/grub.d/01_users ###

### BEGIN /etc/grub.d/10_linux ###
menuentry 'CentOS Linux (3.10.0-693.el7.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-693.el7.x86_64-advanced-162b8754-3820-4229-9ab4-810de8ab8558' {
    load_video
    set gfxpayload=keep
    insmod gzio
    insmod part_msdos
    insmod xfs
    set root='hd0,msdos1'
    if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  985589ee-8d6c-4dbb-9463-90576a5a1ff4
    else
      search --no-floppy --fs-uuid --set=root 985589ee-8d6c-4dbb-9463-90576a5a1ff4
    fi
    linux16 /vmlinuz-3.10.0-693.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet LANG=en_US.UTF-8
    initrd16 /initramfs-3.10.0-693.el7.x86_64.img
}
menuentry 'CentOS Linux (0-rescue-1cf30b938dc94f2bb08fb045c7a0734e) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e-advanced-162b8754-3820-4229-9ab4-810de8ab8558' {
    load_video
    insmod gzio
    insmod part_msdos
    insmod xfs
    set root='hd0,msdos1'
    if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  985589ee-8d6c-4dbb-9463-90576a5a1ff4
    else
      search --no-floppy --fs-uuid --set=root 985589ee-8d6c-4dbb-9463-90576a5a1ff4
    fi
    linux16 /vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet
    initrd16 /initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
}

### END /etc/grub.d/10_linux ###

### BEGIN /etc/grub.d/20_linux_xen ###
### END /etc/grub.d/20_linux_xen ###

### BEGIN /etc/grub.d/20_ppc_terminfo ###
### END /etc/grub.d/20_ppc_terminfo ###

### BEGIN /etc/grub.d/30_os-prober ###
### END /etc/grub.d/30_os-prober ###

### BEGIN /etc/grub.d/40_custom ###
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.
### END /etc/grub.d/40_custom ###

### BEGIN /etc/grub.d/41_custom ###
if [ -f  ${config_directory}/custom.cfg ]; then
  source ${config_directory}/custom.cfg
elif [ -z "${config_directory}" -a -f  $prefix/custom.cfg ]; then
  source $prefix/custom.cfg;
fi
### END /etc/grub.d/41_custom ###
```

برای اعمال هرگونه تغییر در grub.cfg دو مرحله لازم است.

### 1- /etc/default/grub

ابتدا /etc/default/grub را ویرایش کنید:

```
[root@centos7-1 ~]# cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
```

تغییرات را در /etc/default/grub ذخیره کنید. پیکربندی‌ها با استفاده از دستور grub-mkconfig در /boot/grub2/grub.cfg نوشته می‌شوند.

### 2- grub-mkconfig

grub-mkconfig یک فایل پیکربندی GRUB تولید می‌کند.

```
[root@centos7-1 ~]# grub2-mkconfig 
Generating grub configuration file ...
#
# DO NOT EDIT THIS FILE
#
# It is automatically generated by grub2-mkconfig using templates
# from /etc/grub.d and settings from /etc/default/grub
#

### BEGIN /etc/grub.d/00_header ###
set pager=1

if [ -s $prefix/grubenv ]; then
  load_env
fi
if [ "${next_entry}" ] ; then
   set default="${next_entry}"
   set next_entry=
   save_env next_entry
   set boot_once=true
else
   set default="${saved_entry}"
fi

if [ x"${feature_menuentry_id}" = xy ]; then
  menuentry_id_option="--id"
else
  menuentry_id_option=""
fi

export menuentry_id_option

if [ "${prev_saved_entry}" ]; then
  set saved_entry="${prev_saved_entry}"
  save_env saved_entry
  set prev_saved_entry=
  save_env prev_saved_entry
  set boot_once=true
fi

function savedefault {
  if [ -z "${boot_once}" ]; then
    saved_entry="${chosen}"
    save_env saved_entry
  fi
}

function load_video {
  if [ x$feature_all_video_module = xy ]; then
    insmod all_video
  else
    insmod efi_gop
    insmod efi_uga
    insmod ieee1275_fb
    insmod vbe
    insmod vga
    insmod video_bochs
    insmod video_cirrus
  fi
}

terminal_output console
if [ x$feature_timeout_style = xy ] ; then
  set timeout_style=menu
  set timeout=5
# Fallback normal timeout code in case the timeout_style feature is
# unavailable.
else
  set timeout=5
fi
### END /etc/grub.d/00_header ###

### BEGIN /etc/grub.d/00_tuned ###
set tuned_params=""
set tuned_initrd=""
### END /etc/grub.d/00_tuned ###

### BEGIN /etc/grub.d/01_users ###
if [ -f ${prefix}/user.cfg ]; then
  source ${prefix}/user.cfg
  if [ -n "${GRUB2_PASSWORD}" ]; then
    set superusers="root"
    export superusers
    password_pbkdf2 root ${GRUB2_PASSWORD}
  fi
fi
### END /etc/grub.d/01_users ###

### BEGIN /etc/grub.d/10_linux ###
Found linux image: /boot/vmlinuz-3.10.0-693.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-693.el7.x86_64.img
menuentry 'CentOS Linux (3.10.0-693.el7.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-693.el7.x86_64-advanced-162b8754-3820-4229-9ab4-810de8ab8558' {
    load_video
    set gfxpayload=keep
    insmod gzio
    insmod part_msdos
    insmod xfs
    set root='hd0,msdos1'
    if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  985589ee-8d6c-4dbb-9463-90576a5a1ff4
    else
      search --no-floppy --fs-uuid --set=root 985589ee-8d6c-4dbb-9463-90576a5a1ff4
    fi
    linux16 /vmlinuz-3.10.0-693.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet 
    initrd16 /initramfs-3.10.0-693.el7.x86_64.img
}
Found linux image: /boot/vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e
Found initrd image: /boot/initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
menuentry 'CentOS Linux (0-rescue-1cf30b938dc94f2bb08fb045c7a0734e) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e-advanced-162b8754-3820-4229-9ab4-810de8ab8558' {
    load_video
    insmod gzio
    insmod part_msdos
    insmod xfs
    set root='hd0,msdos1'
    if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  985589ee-8d6c-4dbb-9463-90576a5a1ff4
    else
      search --no-floppy --fs-uuid --set=root 985589ee-8d6c-4dbb-9463-90576a5a1ff4
    fi
    linux16 /vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet 
    initrd16 /initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
}
if [ "x$default" = 'CentOS Linux (3.10.0-693.el7.x86_64) 7 (Core)' ]; then default='Advanced options for CentOS Linux>CentOS Linux (3.10.0-693.el7.x86_64) 7 (Core)'; fi;
### END /etc/grub.d/10_linux ###

### BEGIN /etc/grub.d/20_linux_xen ###
### END /etc/grub.d/20_linux_xen ###

### BEGIN /etc/grub.d/20_ppc_terminfo ###
### END /etc/grub.d/20_ppc_terminfo ###

### BEGIN /etc/grub.d/30_os-prober ###
### END /etc/grub.d/30_os-prober ###

### BEGIN /etc/grub.d/40_custom ###
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.
### END /etc/grub.d/40_custom ###

### BEGIN /etc/grub.d/41_custom ###
if [ -f  ${config_directory}/custom.cfg ]; then
  source ${config_directory}/custom.cfg
elif [ -z "${config_directory}" -a -f  $prefix/custom.cfg ]; then
  source $prefix/custom.cfg;
fi
### END /etc/grub.d/41_custom ###
done
```

فایل‌های اسکریپت اصلی grub را از **/etc/grub.d** و تنظیمات را از **/etc/default/grub** می‌خواند و پیکربندی تولید شده را چاپ می‌کند.

```
[root@centos7-1 ~]# ls /etc/grub.d/
00_header  01_users  20_linux_xen     30_os-prober  41_custom
00_tuned   10_linux  20_ppc_terminfo  40_custom     README
```

گزینه‌های grub2-mkconfig:

```
[root@centos7-1 ~]# grub2-mkconfig --help
Usage: grub2-mkconfig [OPTION]
Generate a grub config file

  -o, --output=FILE       output generated config to FILE [default=stdout]
  -h, --help              print this message and exit
  -v, --version           print the version information and exit

Report bugs to <bug-grub@gnu.org>.
```

برای ذخیره پیکربندی تولید شده از گزینه `-o` استفاده می‌کنیم (توصیه می‌شود از فایل `/boot/grub2/grub.cfg` نسخه پشتیبان تهیه کنید، با این حال این کار روی Master Boot Record تاثیری نمی‌گذارد و فقط منوی GRUB را بازنویسی می‌کند):

```
[root@centos7-1 ~]# grub2-mkconfig -o /boot/grub2/grub.cfg 
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-693.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-693.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e
Found initrd image: /boot/initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
done
```

ما می‌توانستیم از دستور `grub2-mkconfig > /boot/grub2/grub.cfg` نیز استفاده کنیم و مراحل پیکربندی GRUB2 در اینجا به پایان می‌رسد.

#### grub-update

وقتی دستور update-grub را اجرا می‌کنید، GRUB به طور خودکار تنظیمات فایل /etc/default/grub، اسکریپت‌های دایرکتوری /etc/grub.d/ و بقیه موارد را ترکیب کرده و یک فایل /boot/grub/grub.cfg ایجاد می‌کند که در هنگام بوت خوانده می‌شود.

### رابط‌های GRUB

سه رابط در GRUB وجود دارد که همگی سطوح مختلفی از عملکرد را ارائه می‌دهند. هسته لینوکس می‌تواند توسط کاربران با کمک این رابط‌ها بوت شود. جزئیات مربوط به رابط‌ها عبارتند از:

**1- رابط منو (Menu Interface)**

GRUB توسط برنامه نصب در رابط منو پیکربندی می‌شود. این رابط پیش‌فرض موجود است. این رابط شامل لیستی از سیستم‌عامل‌ها یا هسته‌ها است که بر اساس نام مرتب شده‌اند. یک سیستم‌عامل یا هسته خاص را می‌توان با استفاده از کلیدهای جهت‌نما انتخاب کرد و با استفاده از کلید enter بوت کرد.

GRUB Legacy

![](.gitbook/assets/bootloader-grub1menu.jpg)

GRUB2

![](.gitbook/assets/bootloader-grub2menu.jpg)

Missing 'a' hah!

**2- رابط ویرایشگر ورودی منو (Menu Entry Editor Interface)**

کلید e (کلید a در GRUB Legacy) در منوی بوت‌لودر برای دسترسی به ویرایشگر ورودی منو استفاده می‌شود. تمام دستورات GRUB برای آن ورودی خاص منو در آنجا نمایش داده می‌شوند و این دستورات ممکن است قبل از بارگذاری سیستم‌عامل تغییر داده شوند.

GRUB Legacy

![](.gitbook/assets/bootloader-grub1menuent.jpg)

GRUB2

![](.gitbook/assets/bootloader-grub2menuent.jpg)

**3- رابط خط فرمان (Command Line Interface)**

این رابط ابتدایی‌ترین رابط GRUB است اما بیشترین کنترل را به کاربر می‌دهد. با استفاده از رابط خط فرمان، هر دستوری را می‌توان با تایپ کردن آن و سپس فشردن enter اجرا کرد. این رابط همچنین دارای برخی ویژگی‌های پیشرفته شل است.

GRUB Legacy

![](.gitbook/assets/bootloader-grub1menucmd.jpg)

امکان داشتن اعلان (prompt) فرمان GRUB Legacy پس از ری‌بوت کردن سیستم و کسب اطلاعات از آنجا وجود دارد.

```
[root@centos5-1 ~]# grub
Probing devices to guess BIOS drives. This may take a long time.


    GNU GRUB  version 0.97  (640K lower / 3072K upper memory)

 [ Minimal BASH-like line editing is supported.  For the first word, TAB
   lists possible command completions.  Anywhere else TAB lists the possible
   completions of a device/filename.]
```

GRUB2

![](.gitbook/assets/bootloader-grub2menucmd.jpg)

{% hint style="info" %}
توجه: شماره‌گذاری پارتیشن‌ها در GRUB2 تغییر کرده است. اولین پارتیشن در GRUB2 شماره 1 است به جای 0 (GRUB Legacy). اولین دستگاه/درایو هنوز به طور پیش‌فرض hd0 است (تغییری نکرده).
{% endhint %}

### نصب GRUB

معمولاً وقتی سیستمی را راه‌اندازی می‌کنیم، GRUB برای ما نصب می‌شود، اما مواردی وجود دارد که ممکن است نیاز داشته باشیم خودمان GRUB را نصب کنیم. روش‌های مختلفی برای نصب GRUB وجود دارد.

اگر در سیستم قدیمی با GRUB legacy هستیم، می‌توانیم دستور grub-install را اجرا کنیم یا از دستور setup در محیط شل GRUB استفاده کنیم.

```
[root@centos5-1 ~]# grub-install /dev/sdb
```

اگر در GRUB2 هستیم می‌توانیم از یکی از ابزارهای GRUB2 استفاده کنیم.

```
[root@centos7-1 ~]# grub2-
grub2-bios-setup           grub2-mkconfig             grub2-ofpathname
grub2-editenv              grub2-mkfont               grub2-probe
grub2-file                 grub2-mkimage              grub2-reboot
grub2-fstest               grub2-mklayout             grub2-rpm-sort
grub2-get-kernel-settings  grub2-mknetdir             grub2-script-check
grub2-glue-efi             grub2-mkpasswd-pbkdf2      grub2-set-default
grub2-install              grub2-mkrelpath            grub2-setpassword
grub2-kbdcomp              grub2-mkrescue             grub2-sparc64-setup
grub2-menulst2cfg          grub2-mkstandalone         grub2-syslinux2cfg
```

### grub2-install

grub-install، نرم‌افزار GRUB را روی یک دستگاه نصب می‌کند. این شامل کپی کردن تصاویر GRUB در دایرکتوری مقصد (معمولاً /boot/grub) است و در برخی پلتفرم‌ها ممکن است شامل نصب GRUB روی سکتور بوت نیز باشد.

```
[root@centos7-1 ~]# grub2-install --help
Usage: grub2-install [OPTION...] [OPTION] [INSTALL_DEVICE]
Install GRUB on your drive.

      --compress[=no,xz,gz,lzo]   compress GRUB files [optional]
  -d, --directory=DIR        use images and modules under DIR
                             [default=/usr/lib/grub/<platform>]
      --fonts=FONTS          install FONTS [default=unicode]
      --install-modules=MODULES   install only MODULES and their dependencies
                             [default=all]
  -k, --pubkey=FILE          embed FILE as public key for signature checking
      --locale-directory=DIR use translations under DIR
                             [default=/usr/share/locale]
      --locales=LOCALES      install only LOCALES [default=all]
      --modules=MODULES      pre-load specified modules MODULES
      --themes=THEMES        install THEMES [default=starfield]
  -v, --verbose              print verbose messages.
      --allow-floppy         make the drive also bootable as floppy (default
                             for fdX devices). May break on some BIOSes.
      --boot-directory=DIR   install GRUB images under the directory DIR/grub2
                             instead of the boot/grub2 directory
      --bootloader-id=ID     the ID of bootloader. This option is only
                             available on EFI and Macs.
      --core-compress=xz|none|auto
                             choose the compression to use for core image
      --disk-module=MODULE   disk module to use (biosdisk or native). This
                             option is only available on BIOS target.
      --efi-directory=DIR    use DIR as the EFI System Partition root.
      --force                install even if problems are detected
      --force-file-id        use identifier file even if UUID is available
      --label-bgcolor=COLOR  use COLOR for label background
      --label-color=COLOR    use COLOR for label
      --label-font=FILE      use FILE as font for label
      --macppc-directory=DIR use DIR for PPC MAC install.
      --no-bootsector        do not install bootsector
      --no-nvram             don't update the `boot-device'/`Boot*' NVRAM
                             variables. This option is only available on EFI
                             and IEEE1275 targets.
      --no-rs-codes          Do not apply any reed-solomon codes when
                             embedding core.img. This option is only available
                             on x86 BIOS targets.
      --product-version=STRING   use STRING as product version
      --recheck              delete device map if it already exists
      --removable            the installation device is removable. This option
                             is only available on EFI.
  -s, --skip-fs-probe        do not probe for filesystems in DEVICE
      --target=TARGET        install GRUB for TARGET platform
                             [default=i386-pc]; available targets: arm-efi,
                             arm-uboot, arm64-efi, i386-coreboot, i386-efi,
                             i386-ieee1275, i386-multiboot, i386-pc,
                             i386-qemu, i386-xen, ia64-efi, mips-arc,
                             mips-qemu_mips, mipsel-arc, mipsel-loongson,
                             mipsel-qemu_mips, powerpc-ieee1275,
                             sparc64-ieee1275, x86_64-efi, x86_64-xen
  -?, --help                 give this help list
      --usage                give a short usage message
  -V, --version              print program version

Mandatory or optional arguments to long options are also mandatory or optional
for any corresponding short options.

INSTALL_DEVICE must be system device filename.
grub2-install copies GRUB images into boot/grub2.  On some platforms, it may
also install GRUB into the boot sector.

Report bugs to <bug-grub@gnu.org>.
```

به عنوان مثال `grub2-install /dev/sdb` گراب را روی دستگاه sdb، در رکورد راه انداز اصلی هارد دیسک من نصب می‌کند.

با این حال، نصب GRUB از یک سیستم در حال اجرا جالب به نظر می‌رسد، اما بیشتر اوقات مشکلی در طول فرآیند بوت وجود دارد و ما دیگر نمی‌توانیم وارد سیستم خود شویم، بنابراین باید GRUB را از محیط شل GRUB دوباره نصب کنیم. متاسفانه دستور setup از شل GRUB2 حذف شده است و نیاز به تلاش بیشتری دارد. ما باید سیستم را در حالت نجات (rescue mode) با استفاده از یک live cd بالا بیاوریم و سپس GRUB را روی هارد دیسک خود با استفاده از سیستم‌فایل ریشه موقت فعلی نصب کنیم:

{% hint style="danger" %}
این فقط یک مثال است و آن را اجرا نکنید زیرا سیستم فعلی خود را به هم می‌ریزید!
{% endhint %}

`grub2-install --boot-directory=/tmp/root/boot /dev/sda`



.

.

.

Sources:

[https://en.wikipedia.org/wiki/GNU_GRUB](https://en.wikipedia.org/wiki/GNU_GRUB)

[https://whatis.techtarget.com/definition/GRUB-GRand-Unified-Bootloader](https://whatis.techtarget.com/definition/GRUB-GRand-Unified-Bootloader)

[https://www.gnu.org/software/grub/](https://www.gnu.org/software/grub/)

[https://www.dedoimedo.com/computers/grub.html](https://www.dedoimedo.com/computers/grub.html)

[https://www.tutorialspoint.com/what-is-grub-in-linux](https://www.tutorialspoint.com/what-is-grub-in-linux)

[https://help.ubuntu.com/community/Grub2](https://help.ubuntu.com/community/Grub2)

[https://www.mankier.com/8/grub2-install](https://www.mankier.com/8/grub2-install)

.
