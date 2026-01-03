# 102.3 مدیریت کتابخانه‌های مشترک (shared libraries)

## **102.3 مدیریت کتابخانه‌های مشترک (shared libraries)**

**وزن:** 1

**توضیحات:** داوطلبان باید قادر به تعیین کتابخانه‌های مشترکی باشند که برنامه‌های اجرایی به آن‌ها وابسته هستند و در صورت نیاز آن‌ها را نصب کنند.

**حوزه‌های کلیدی دانش:**

* شناسایی کتابخانه‌های مشترک (shared libraries)
* شناسایی مکان‌های معمول کتابخانه‌های سیستم
* بارگذاری کتابخانه‌های مشترک

**اصطلاحات و ابزارهای کاربردی:**

* ldd
* ldconfig
* /etc/ld.so.conf
* LD_LIBRARY_PATH

#### کتابخانه (library) چیست؟

در برنامه‌نویسی، یک کتابخانه مجموعه‌ای از قطعات کد از پیش کامپایل شده است که می‌توان آن‌ها را در یک برنامه دوباره استفاده کرد. کتابخانه‌ها زندگی برنامه‌نویسان را ساده می‌کنند، زیرا توابع، روتین‌ها، کلاس‌ها، ساختارهای داده و غیره (نوشته شده توسط یک برنامه‌نویس دیگر) را فراهم می‌کنند که می‌توانند در برنامه‌های خود استفاده کنند.

![](.gitbook/assets/sharedlib-intro.jpg)

لینوکس از دو کلاس کتابخانه پشتیبانی می‌کند:

* کتابخانه‌های استاتیک (Static libraries) – در زمان کامپایل به صورت ایستا به برنامه متصل می‌شوند.
* کتابخانه‌های پویا یا مشترک (Dynamic or shared libraries) – هنگام راه‌اندازی برنامه بارگذاری شده و در حافظه قرار می‌گیرند و اتصال در زمان اجرا (run time) اتفاق می‌افتد.

**کتابخانه‌های استاتیک در مقابل کتابخانه‌های مشترک**

* هر برنامه‌ای که از روتین‌های یک کتابخانه استاتیک استفاده می‌کند، یک کپی از آن‌ها را در فایل اجرایی خود دارد. این کار باعث هدر رفتن فضای دیسک و (اگر بیش از یک چنین برنامه‌ای به طور همزمان در حال استفاده باشد) RAM نیز می‌شود. همچنین، هنگامی که یک کتابخانه استاتیک به‌روزرسانی می‌شود، تمام برنامه‌هایی که از آن استفاده می‌کنند باید دوباره کامپایل شوند تا از کد جدید بهره‌مند شوند. 
* در مقابل، وقتی یک برنامه از کتابخانه مشترک استفاده می‌کند، باینری برنامه شامل کپی کد نیست، بلکه فقط ارجاعی به کتابخانه دارد. loader زمان اجرا، کتابخانه را پیدا کرده و همزمان با برنامه در حافظه بارگذاری می‌کند. یکی از مزایای استفاده از کتابخانه پویا این است که وقتی یک کتابخانه به‌روزرسانی می‌شود (مسائل امنیتی)، تمام برنامه‌های دیگر بدون نیاز به کامپایل مجدد از کد جدید بهره‌مند می‌شوند.

{% hint style="info" %}
کتابخانه‌های پویا یا مشترک را می‌توان بیشتر به دو گروه تقسیم کرد (فراتر از محدوده آزمون LPIC):

* **کتابخانه‌های لینک شده به صورت پویا (Dynamically linked libraries)** – در اینجا برنامه با کتابخانه مشترک لینک شده و هسته (kernel) کتابخانه را هنگام اجرا بارگذاری می‌کند (در صورتی که در حافظه نباشد).
* **کتابخانه‌های بارگذاری شده به صورت پویا (Dynamically loaded libraries)** – برنامه با فراخوانی توابع داخل کتابخانه، کنترل کامل را به دست می‌گیرد.
{% endhint %}

### مکان‌های کتابخانه‌ها در لینوکس

بسته به کتابخانه، لینوکس کتابخانه‌های خود را عمدتاً در سه مکان ذخیره می‌کند:

* **/lib**
* **/usr/lib**
* **/usr/local/lib**

**/lib**: دایرکتوری /lib حاوی آن **تصاویر کتابخانه مشترکی است که برای بوت کردن** سیستم و اجرای دستورات در سیستم‌فایل ریشه مورد نیاز است، یعنی برای باینری‌های موجود در /bin و /sbin.

```
root@ubuntu16-1:~# ls /lib
apparmor  ifupdown                              modules        udev
brltty    init                                  recovery-mode  ufw
cpp       klibc-k3La8MUnuzHQ0_kG8hokcGAC0PA.so  resolvconf     x86_64-linux-gnu
crda      linux-sound-base                      systemd        xtables
firmware  lsb                                   sysvinit
hdparm    modprobe.d                            terminfo
```

دایرکتوری‌های هم‌خانواده پوشه /lib، دایرکتوری‌های /lib32 و /lib64 هستند.

**/usr/lib**: تمام **کتابخانه‌های نرم‌افزاری** در اینجا نصب می‌شوند. این دایرکتوری **شامل کتابخانه‌های پیش‌فرض سیستم یا هسته نیست**.

```
root@ubuntu16-1:~# ls /usr/lib/
```

**/usr/local/lib**: برای قرار دادن **فایل‌های کتابخانه اضافی سیستم** در اینجا است. این فایل‌های کتابخانه می‌توانند توسط برنامه‌های مختلف استفاده شوند.

```
root@ubuntu16-1:~# ls /usr/local/lib/
python2.7  python3.5
```

همچنین دایرکتوری **/var/lib**، **کتابخانه‌ها/فایل‌های داده پویا** مانند پایگاه داده rpm/dpkg و امتیازات بازی‌ها را نگه می‌دارد.

```
root@ubuntu16-1:~# ls /var/lib/
AccountsService      fwupd            nginx                    udisks2
acpi-support         gconf            nssdb                    update-manager
alsa                 ghostscript      os-prober                update-notifier
apparmor             git              pam                      update-rc.d
app-info             hp               plymouth                 upower
apt                  initramfs-tools  polkit-1                 urandom
aspell               initscripts      python                   ureadahead
avahi-autoipd        insserv          resolvconf               usb_modeswitch
binfmts              ispell           samba                    usbutils
bluetooth            libreoffice      sgml-base                vim
colord               lightdm          snapd                    vmware
dbus                 lightdm-data     snmp                     vmware-caf
dhcp                 locales          sudo                     whoopsie
dictionaries-common  logrotate        systemd                  xfonts
doc-base             man-db           ubiquity                 xkb
dpkg                 misc             ubuntu-drivers-common    xml-core
emacsen-common       mlocate          ubuntu-release-upgrader
fail2ban             NetworkManager   ucf
```

در این مقاله به طور خاص در مورد Shared Libraries صحبت خواهیم کرد.

### قراردادهای نام‌گذاری کتابخانه‌های مشترک (Shared Library Naming Conventions)

Shared libraries are named in three ways:

* library name (a.k.a "soname")  
* filename  (a.k.a "real name")(absolute path to file which stores library code)
* "linker name"

**1)**Every shared library has a special name called the **"soname''**. The soname has the prefix "**lib**'', the name of the library, the phrase "**.so**'', followed by a period and a **version number** that is incremented whenever the interface changes . 

```
libreadline.so.6
```

A fully-qualified soname includes as a prefix the directory it's in.

```
/usr/lib/libreadline.so.6
```

On a working system a fully-qualified soname is simply a symbolic link to the shared library's "real name''.

**2)**Every shared library also has a **"real name''**, which is the filename containing the actual library code. The real name adds to the soname a period, a minor number, another period, and the release number. The last period and release number are optional.

```
libreadline.so.6.3
```

**3)**In addition, there's the name that the compiler uses when requesting a library, (I'll call it the **"linker name''**), which is simply the soname without any version number.

```
libreadline.so
```

Okey lets explain all in an example:

```
root@ubuntu16-1:/lib/x86_64-linux-gnu# ls -l | grep libread
lrwxrwxrwx 1 root root      18 Nov 26  2017 libreadline.so.6 -> libreadline.so.6.3
-rw-r--r-- 1 root root  282392 Feb  4  2016 libreadline.so.6.3
```

Thus, `/usr/lib/libreadline.so.6` is a fully-qualified soname, which is set to be a symbolic link to some realname like `/usr/lib/libreadline.so.6.3`. There should also be a linker name, `/usr/lib/libreadline.so` which could be a symbolic link referring to `/usr/lib/libreadline.so.6`.

### ldd

برای **به دست آوردن لیستی از تمام وابستگی‌های کتابخانه مشترک برای یک فایل باینری**، می‌توانید از ابزار ldd استفاده کنید. به عنوان مثال بیایید ببینیم چه کتابخانه‌های مشترکی توسط دستور ls مورد نیاز است:

```
root@ubuntu16-1:~# which ls
/bin/ls
root@ubuntu16-1:~# ldd /bin/ls
    linux-vdso.so.1 =>  (0x00007ffe511d5000)
    libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f687d3b1000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f687cfe7000)
    libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f687cd77000)
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f687cb72000)
    /lib64/ld-linux-x86-64.so.2 (0x00005591d42e3000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f687c955000)
```

Try `ltrace ls` to get surprised! But how poor ls can find its required shared libraries?

### Locating Shared Libraries in Linux

A program can call a library using its library name or filename, and a library path stores directories where libraries can be found in the filesystem. But How it can find them? Lets show the structure in a picture:

![](.gitbook/assets/sharedlib-filestructure.jpg)

These three files helps programs to find their required shared libraries.

### ld.so.conf

فایل ld.so.conf به سیستم می‌گوید که کجا به دنبال فایل‌های کتابخانه بگردد، این فایل نوعی دفترچه آدرس است.

```
root@ubuntu16-1:~# cat /etc/ld.so.conf
include /etc/ld.so.conf.d/*.conf
```

Traditionally in the past, it included all the paths which libraries existed. Today more abstraction has been implemented and it just points to ld.so.conf.d directory. Simply it says go to the ld.so.conf.d directory and load any configuration files.

### /etc/ld.so.conf.d

دایرکتوری ld.so.conf.d شامل چندین فایل پیکربندی برای هسته یا سایر برنامه‌های شخص ثالث (third party) است.

```
root@ubuntu16-1:~# ls -la /etc/ld.so.conf.d/
total 32
drwxr-xr-x   2 root root  4096 Oct  7 00:37 .
drwxr-xr-x 142 root root 12288 Nov 11 00:36 ..
-rw-rw-r--   1 root root    38 Nov 24  2014 fakeroot-x86_64-linux-gnu.conf
-rw-r--r--   1 root root    44 Jan 27  2016 libc.conf
-rw-r--r--   1 root root   276 Oct  7 00:37 vmware-tools-libraries.conf
-rw-r--r--   1 root root    68 Apr 14  2016 x86_64-linux-gnu.conf
lrwxrwxrwx   1 root root    43 Nov 26  2017 x86_64-linux-gnu_EGL.conf -> /etc/alternatives/x86_64-linux-gnu_egl_conf
lrwxrwxrwx   1 root root    42 Nov 26  2017 x86_64-linux-gnu_GL.conf -> /etc/alternatives/x86_64-linux-gnu_gl_conf
```

Lets see what is inside configuration files :

```
root@ubuntu16-1:~# cat /etc/ld.so.conf.d/libc.conf 
# libc default configuration
/usr/local/lib
root@ubuntu16-1:~# cat /etc/ld.so.conf.d/x86_64-linux-gnu.conf 
# Multiarch support
/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu
root@ubuntu16-1:~# cat /etc/ld.so.conf.d/vmware-tools-libraries.conf 
/usr/lib/vmware-tools/lib32/libvmGuestLib.so
/usr/lib/vmware-tools/lib64/libvmGuestLib.so
/usr/lib/vmware-tools/lib32/libvmGuestLibJava.so
/usr/lib/vmware-tools/lib64/libvmGuestLibJava.so
/usr/lib/vmware-tools/lib32/libDeployPkg.so
/usr/lib/vmware-tools/lib64/libDeployPkg.so
```

ما می‌توانیم یک فایل پیکربندی جدید اضافه کنیم یا هر یک از آن‌ها را ویرایش کنیم تا مسیر جدیدی را شامل شود.

### ldconfig

از آنجایی که کتابخانه‌های مشترک می‌توانند در دایرکتوری‌های مختلفی وجود داشته باشند، جستجو در تمام این دایرکتوری‌ها هنگام راه‌اندازی یک برنامه بسیار ناکارآمد خواهد بود، که یکی از معایب احتمالی کتابخانه‌های پویا است. بنابراین مکانیزمی از حافظه پنهان (caching) به کار گرفته شده است که توسط برنامه ldconfig انجام می‌شود.

By default, ldconfig reads the content of /etc/ld.so.conf, creates the appropriate symbolic links in the dynamic link directories, and then writes a cache to /etc/ld.so.cache which is then easily used by other programs.

### ld.so.cache

این یک فایل باینری است، بنابراین چیزی نیست که معمولاً دوست داشته باشیم آن را ببینیم:

```
root@ubuntu16-1:~# cat /etc/ld.so.cache 
ld.so-1.7.0^^<^P^~^^^^^
                                       _<_F_f_s_____``<`K`t``````a#aJa\aaaaaa
b4bFbrbbbbbb$c7cdcucccccd(dTdgddddde0e^ereeeeef,fVfgfffff
                                           ggFgSgvgggggg(h7h`hthhhhh"i9ijiiiiij>jRjjjjjk%kTklkkkkl6lKlzllllm"mXmpmmmmmn%nLn]nnnnn6oFopoooooo,p=phppppp    q:qQqqqqqqq&r3rZrmrrrrrssHsZsssssstWtltttuuPucuuuuu
vv@vKvpv}vvvvvww<wMwxwwwwwwx5xfxzxxxxx&y<ylyyyyzz>z\zzzzz {.{V{l{{{{|N|a|||||*}?}n}}}}
~"~T~f~~~~~~
4Adr'NZЀހPmցJW~$6bt؃
4L~ȅ$3\hĆц@Z;tڈ(Niމ
:Gn~ 6fxҎ)Zxďь
<The out put has been truncuated>
```

use `string` command intead and it just shows human readable binary files, also `lconfig -p` prints cache.

این کار بسیار مهم است، به خصوص زمانی که به تازگی کتابخانه‌های مشترک جدیدی نصب کرده‌ایم یا کتابخانه‌های خودمان را ایجاد کرده‌ایم، یا دایرکتوری‌های کتابخانه جدیدی ساخته‌ایم. برای اعمال تغییرات باید دستور ldconfig را اجرا کنیم (خروجی کوتاه شده است):

```
root@ubuntu16-1:~# ldconfig -v
/sbin/ldconfig.real: Path `/lib/x86_64-linux-gnu' given more than once
/sbin/ldconfig.real: Path `/usr/lib/x86_64-linux-gnu' given more than once
/usr/lib/x86_64-linux-gnu/libfakeroot:
    libfakeroot-0.so -> libfakeroot-tcp.so
/usr/local/lib:
/usr/lib/vmware-tools/lib32/libvmGuestLib.so:
    libvmGuestLib.so -> libvmGuestLib.so
/usr/lib/vmware-tools/lib64/libvmGuestLib.so:
    libvmGuestLib.so -> libvmGuestLib.so
/usr/lib/vmware-tools/lib32/libvmGuestLibJava.so:
    libvmGuestLibJava.so -> libvmGuestLibJava.so
/usr/lib/vmware-tools/lib64/libvmGuestLibJava.so:
    libvmGuestLibJava.so -> libvmGuestLibJava.so
/usr/lib/vmware-tools/lib32/libDeployPkg.so:
    libDeployPkg.so -> libDeployPkg.so
/usr/lib/vmware-tools/lib64/libDeployPkg.so:
    libDeployPkg.so -> libDeployPkg.so
/lib/x86_64-linux-gnu:
    libsepol.so.1 -> libsepol.so.1
    libisc-export.so.160 -> libisc-export.so.160.0.0
    libdns-export.so.162 -> libdns-export.so.162.1.3
    libselinux.so.1 -> libselinux.so.1
    libuuid.so.1 -> libuuid.so.1.3.0
    libc.so.6 -> libc-2.23.so
    libnss_mdns6.so.2 -> libnss_mdns6.so.2
    libnss_files.so.2 -> libnss_files-2.23.so
    ...
/sbin/ldconfig.real: /lib/x86_64-linux-gnu/ld-2.23.so is the dynamic linker, ignoring

    ld-linux-x86-64.so.2 -> ld-2.23.so
    libnih-dbus.so.1 -> libnih-dbus.so.1.0.0
    libnss_mdns.so.2 -> libnss_mdns.so.2
    libatasmart.so.4 -> libatasmart.so.4.0.5
    libdbus-1.so.3 -> libdbus-1.so.3.14.6
    libpthread.so.0 -> libpthread-2.23.so
    libutil.so.1 -> libutil-2.23.so
    libz.so.1 -> libz.so.1.2.8
    ...
/usr/lib/x86_64-linux-gnu:
    libplc4.so -> libplc4.so
    libcdr-0.1.so.1 -> libcdr-0.1.so.1.0.2
    libmwaw-0.3.so.3 -> libmwaw-0.3.so.3.0.7
    libjbig.so.0 -> libjbig.so.0
    libnssutil3.so -> libnssutil3.so
    libOxideQtQuick.so.0 -> libOxideQtQuick.so.0
    libubsan.so.0 -> libubsan.so.0.0.0
    libgudev-1.0.so.0 -> libgudev-1.0.so.0.2.0
    libx265.so.79 -> libx265.so.79
    libnetfilter_conntrack.so.3 -> libnetfilter_conntrack.so.3.5.0
    libsamba-util.so.0 -> libsamba-util.so.0.0.1
    ...
d.so
    libtotem-plparser-mini.so.18 -> libtotem-plparser-mini.so.18.1.0
    libhcrypto.so.4 -> libhcrypto.so.4.1.0
    libxcb-shape.so.0 -> libxcb-shape.so.0.0.0
    libQt5PrintSupport.so.5 -> libQt5PrintSupport.so.5.5.1
    libdmapsharing-3.0.so.2 -> libdmapsharing-3.0.so.2.9.34
    libyaml-0.so.2 -> libyaml-0.so.2.0.4
    libfreerdp-core.so.1.1 -> libfreerdp-core.so.1.1.0
    libzeitgeist-1.0.so.1 -> libzeitgeist-1.0.so.1.1.4
    libHalf.so.12 -> libHalf.so.12.0.0
    libxcb-util.so.1 -> libxcb-util.so.1.0.0
2.4.so.1.3.0
    libhardware.so.2 -> libhardware.so.2.0.0
    libindicator3.so.7 -> libindicator3.so.7.0.0
    libcmis-0.5.so.5 -> libcmis-0.5.so.5.0.0
    libmpxwrappers.so.0 -> libmpxwrappers.so.0.0.0
    libkrb5.so.3 -> libkrb5.so.3.3
    libavdevice-ffmpeg.so.56 -> libavdevice-ffmpeg.so.56.4.100
    libndp.so.0 -> libndp.so.0.0.2
    libao.so.4 -> libao.so.4.0.0
    libsignon-plugins.so.1 -> libsignon-plugins.so.1.0.0
    liba52-0.7.4.so -> liba52-0.7.4.so
    ...
/usr/lib/x86_64-linux-gnu/mesa-egl:
    libEGL.so.1 -> libEGL.so.1.0.0
/usr/lib/x86_64-linux-gnu/mesa:
    libGL.so.1 -> libGL.so.1.2.0
/lib:
/usr/lib:
    libgdiplus.so.0 -> libgdiplus.so.0.0.0
    libMonoPosixHelper.so -> libMonoPosixHelper.so
    libMonoSupportW.so -> libMonoSupportW.so
    libnetpbm.so.10 -> libnetpbm.so.10.0
    libfsplib.so.0 -> libfsplib.so.0.0.0
```

ldconfig command has some options:

```
root@ubuntu16-1:~# ldconfig --help
Usage: ldconfig.real [OPTION...]
Configure Dynamic Linker Run Time Bindings.

  -c, --format=FORMAT        Format to use: new, old or compat (default)
  -C CACHE                   Use CACHE as cache file
  -f CONF                    Use CONF as configuration file
  -i, --ignore-aux-cache     Ignore auxiliary cache file
  -l                         Manually link individual libraries.
  -n                         Only process directories specified on the command
                             line.  Don't build cache.
  -N                         Don't build cache
  -p, --print-cache          Print cache
  -r ROOT                    Change to and use ROOT as root directory
  -v, --verbose              Generate verbose messages
  -X                         Don't update symbolic links
  -?, --help                 Give this help list
      --usage                Give a short usage message
  -V, --version              Print program version

Mandatory or optional arguments to long options are also mandatory or optional
for any corresponding short options.

For bug reporting instructions, please see:
<https://bugs.launchpad.net/ubuntu/+source/glibc/+bugs>.
```

پس از ایجاد کتابخانه مشترک خود، باید آن را نصب کنیم.

### Installing a Dynamic Library: <a href="f3ce" id="f3ce"></a>

چندین راه برای نصب یک کتابخانه پویا وجود دارد:

**1)**We can either move it into any of the standard directories mentioned above, and run the ldconfig command.

**2)**The method above sets the library path permanently. To set it temporarily, use the LD_LIBRARY_PATH environment variable on the command line.

## LD_LIBRARY_PATH

در لینوکس، متغیر محیطی LD_LIBRARY_PATH مجموعه‌ای از دایرکتوری‌ها است که با دونقطه (colon) از هم جدا شده‌اند و کتابخانه‌ها باید ابتدا در آنجا جستجو شوند، قبل از مجموعه استاندارد دایرکتوری‌ها؛ این کار هنگام دیباگ کردن یک کتابخانه جدید یا استفاده از یک کتابخانه غیراستاندارد برای اهداف خاص مفید است.

```
root@ubuntu16-1:~# echo $LD_LIBRARY_PATH
```

The usual dynamic linker on Linux uses a cache to find its libraries, So there is no default value for LD_LIBRARY_PATH, default library lookup doesn’t need it at all. If LD_LIBRARY_PATH is defined, then it is used first, but doesn’t disable the other lookups (which also include a few default directories).

Whether it has a default value or not we can use blow command to add our new library path:

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/your/custom/path/
```

فراموش نکنید که این تغییرات دائمی نیستند و اگر می‌خواهید تغییرات را دائمی نگه دارید، خط بالا را در فایل مقداردهی اولیه شل یعنی **/etc/profile** (سراسری) یا **~/.profile** (خاص کاربر) اضافه کنید که در درس‌های بعدی مورد بحث قرار خواهد گرفت.

تمام شد!

.

.

.

Sources:

[https://www.tecmint.com/understanding-shared-libraries-in-linux/](https://www.tecmint.com/understanding-shared-libraries-in-linux/)

[https://www.ibm.com/developerworks/linux/library/l-dynamic-libraries/](https://www.ibm.com/developerworks/linux/library/l-dynamic-libraries/)

[http://www.linuxforums.org/forum/newbie/151875-what-libraries.html](http://www.linuxforums.org/forum/newbie/151875-what-libraries.html)

[https://www.linuxnix.com/linux-directory-structure-lib-explained/](https://www.linuxnix.com/linux-directory-structure-lib-explained/)

[https://askubuntu.com/questions/17545/where-does-ubuntu-store-its-library-files](https://askubuntu.com/questions/17545/where-does-ubuntu-store-its-library-files)

[https://medium.com/@jhuang1012bn/static-library-and-shared-library-de6def6b1d3b](https://medium.com/@jhuang1012bn/static-library-and-shared-library-de6def6b1d3b)

[http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html](http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html)

[https://unix.stackexchange.com/questions/354295/what-is-the-default-value-of-ld-library-path](https://unix.stackexchange.com/questions/354295/what-is-the-default-value-of-ld-library-path)
