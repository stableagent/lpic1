# 102.4 استفاده از مدیریت پکیج Debian

## **102.4 استفاده از مدیریت پکیج Debian**

**وزن:** 3

**توضیحات:** داوطلبان باید قادر به انجام مدیریت پکیج با استفاده از ابزارهای پکیج Debian باشند.

**حوزه‌های کلیدی دانش:**

* نصب، ارتقا و حذف پکیج‌های باینری Debian
* یافتن پکیج‌های حاوی فایل‌ها یا کتابخانه‌های خاص که ممکن است نصب شده باشند یا نباشند
* به دست آوردن اطلاعات پکیج مانند نسخه، محتوا، وابستگی‌ها، یکپارچگی پکیج و وضعیت نصب (نصب شده یا نشده)

**اصطلاحات و ابزارهای کاربردی:**

* /etc/apt/sources.list
* dpkg
* dpkg-reconfigure
* apt-get
* apt-cache
* aptitude

### پکیج (package) چیست؟

یک پکیج قطعه‌ای از نرم‌افزار است که بخشی از سیستم را فراهم می‌کند. مثال‌هایی از پکیج‌ها:

* Linux Kernel
* کامپایلر C
* مرورگر وب Firefox
* کتابخانه USB برای تعامل با دستگاه‌های USB

یک پکیج حاوی فایل‌ها و سایر دستورالعمل‌های مورد نیاز برای کارکرد یک مؤلفه نرم‌افزاری در سیستم است.

پکیج‌ها به پکیج‌های دیگری وابستگی (dependencies) دارند که باید قبل از نصب پکیج اصلی، دانلود و نصب شوند.

در گذشته، مدیران لینوکس برای نصب یک پکیج با "جهنم وابستگی" (dependency hell) روبرو بودند که باعث شد هندلرهای متا پکیج (meta package handlers) متولد شوند.

### مدیریت پکیج (Package Manager) چیست؟

مدیریت پکیج ابزاری است که دانلود، از حالت فشرده خارج کردن و قرار دادن قطعات استخراج شده در مکان‌های مناسب را انجام می‌دهد.

![](.gitbook/assets/debpack-packagemgmnt.png)

گردش کار کلی با درخواست پکیج توسط کاربر با استفاده از مدیریت پکیج موجود در سیستم شروع می‌شود. سپس مدیریت پکیج، پکیج درخواستی را از یک مکان شناخته شده (مخزن نرم‌افزاری یا software repository) پیدا کرده و دانلود می‌کند. سپس مدیریت پکیج، پکیج را نصب کرده و در مورد هر مرحله دستی که لازم بداند توصیه می‌کند.

ابزارهای مدیریت پکیج به مدیران سیستم/سرور از جهات بسیاری کمک می‌کنند، مانند:

* دانلود و نصب نرم‌افزار
* کامپایل نرم‌افزار از منبع (source)
* پیگیری تمام نرم‌افزارهای نصب شده، به‌روزرسانی‌ها و ارتقاهای آن‌ها
* مدیریت وابستگی‌ها (dependencies)
* و همچنین نگه داشتن سایر اطلاعات در مورد نرم‌افزارهای نصب شده و بسیاری موارد دیگر

#### مخزن نرم‌افزاری (Software Repository) چیست؟

یک مخزن لینوکس **یک مکان ذخیره‌سازی است که سیستم ما به‌روزرسانی‌های سیستم‌عامل و برنامه‌ها را از آنجا بازیابی و نصب می‌کند.** هر مخزن مجموعه‌ای از نرم‌افزارها است که روی یک سرور راه دور میزبانی می‌شود و برای نصب و به‌روزرسانی پکیج‌های نرم‌افزاری در سیستم‌های لینوکس در نظر گرفته شده است.

![](.gitbook/assets/debpack-swrepo.jpg)

مدیریت پکیج شامل دو نهاد است:

* ابزار سطح پایین (low-level tool)
* ابزار سطح بالا (high-level tool)

![](.gitbook/assets/debpack-all.jpg)

یک ابزار **سطح پایین** (مانند **dpkg** یا **rpm**)، جزئیات از حالت فشرده خارج کردن پکیج‌های منفرد، اجرای اسکریپت‌ها و نصب صحیح نرم‌افزار را بر عهده می‌گیرد، در حالی که یک ابزار **سطح بالا** (مانند **apt-get**، yum یا **zypper**) با گروه‌هایی از پکیج‌ها کار می‌کند، پکیج‌ها را از فروشنده دانلود می‌کند و وابستگی‌ها را تشخیص می‌دهد.

بخش‌های اصلی یک توزیع لینوکس و بیشتر نرم‌افزارهای افزودنی آن از طریق سیستم مدیریت پکیج نصب می‌شوند.

در این درس درباره مدیریت پکیج در توزیع‌های مبتنی بر Debian صحبت خواهیم کرد.

![](.gitbook/assets/debpack-deb.jpg)

### dpkg

dpkg یک مدیریت پکیج برای سیستم‌های مبتنی بر Debian است. این ابزار می‌تواند پکیج‌ها را نصب، حذف و ایجاد کند، اما برخلاف سایر سیستم‌های مدیریت پکیج، **نمی‌تواند به طور خودکار پکیج‌ها یا وابستگی‌های آن‌ها را دانلود و نصب کند.**

دیتابیس dpkg در دایرکتوری /var/lib/dpkg/ قرار دارد؛

```
root@ubuntu16-1:~# ls -l /var/lib/dpkg/
total 4416
drwxr-xr-x 2 root root    4096 Dec  2  2018 alternatives
-rw-r--r-- 1 root root      11 Nov 26  2017 arch
-rw-r--r-- 1 root root  170080 Aug  1  2017 available
-rw-r--r-- 1 root root       8 Aug  1  2017 cmethopt
-rw-r--r-- 1 root root    1214 Jul  1  2018 diversions
-rw-r--r-- 1 root root    1133 Jul  1  2018 diversions-old
drwxr-xr-x 2 root root  405504 Dec  2  2018 info
-rw-r----- 1 root root       0 Dec  2  2018 lock
-rw-r----- 1 root root       0 Dec  2  2018 lock-frontend
drwxr-xr-x 7 root root    4096 Dec  2  2018 methods
drwxr-xr-x 2 root root    4096 Jan 12  2016 parts
-rw-r--r-- 1 root root     228 Aug  1  2017 statoverride
-rw-r--r-- 1 root root 1948486 Dec  2  2018 status
-rw-r--r-- 1 root root 1948486 Dec  2  2018 status-old
drwxr-xr-x 2 root root    4096 Dec  1  2018 triggers
drwxr-xr-x 2 root root    4096 Dec  2  2018 updates
```

فایل "status" حاوی لیست نرم‌افزارهای نصب شده در سیستم فعلی است.

**بیایید به مفیدترین سوئیچ‌ها نگاهی بیندازیم:**

\-l | --list لیست تمام پکیج‌های نصب شده روی سیستم (گزینه --get-selection نیز همین کار را انجام می‌دهد):

```
root@ubuntu16-1:~# dpkg -l
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name           Version      Architecture Description
+++-==============-============-============-=================================
ii  a11y-profile-m 0.1.10-0ubun amd64        Accessibility Profile Manager - U
ii  account-plugin 0.12+16.04.2 all          GNOME Control Center account plug
ii  account-plugin 0.12+16.04.2 all          GNOME Control Center account plug
ii  account-plugin 0.12+16.04.2 all          GNOME Control Center account plug
...
<output has been truncated>
```

برای مشاهده اینکه آیا یک پکیج خاص نصب شده است یا خیر، از گزینه “-l” به همراه نام پکیج استفاده کنید.

dpkg -i | --install ، نصب یک فایل محلی .deb:

{% hint style="success" %}
.deb پسوند فرمت پکیج نرم‌افزاری برای توزیع لینوکس Debian است.
{% endhint %}

```
root@ubuntu16-1:~# dpkg -i zip_3.0-11_amd64.deb 
(Reading database ... 192789 files and directories currently installed.)
Preparing to unpack zip_3.0-11_amd64.deb ...
Unpacking zip (3.0-11) over (3.0-11) ...
Setting up zip (3.0-11) ...
Processing triggers for man-db (2.7.5-1) ...
```

و اگر پکیجی به وابستگی نیاز داشته باشد، به مشکل برمی‌خورید:

```
root@ubuntu16-1:~# dpkg -i jcal_0.4.1-2_amd64.deb 
dpkg: warning: downgrading jcal from 0.4.1-2build1 to 0.4.1-2
(Reading database ... 192782 files and directories currently installed.)
Preparing to unpack jcal_0.4.1-2_amd64.deb ...
Unpacking jcal (0.4.1-2) over (0.4.1-2build1) ...
dpkg: dependency problems prevent configuration of jcal:
 jcal depends on libjalali0; however:
  Package libjalali0 is not installed.

dpkg: error processing package jcal (--install):
 dependency problems - leaving unconfigured
Processing triggers for man-db (2.7.5-1) ...
Errors were encountered while processing:
 jcal
```

dpkg وابستگی‌ها را مدیریت نمی‌کند، بنابراین اگر سعی کنیم پکیجی را نصب کنیم، یکی از دو اتفاق زیر می‌افتد:

* یا کاملاً شکست می‌خورد!
* یا پکیج را نصب می‌کند اما آن را پیکربندی نشده رها می‌کند تا زمانی که تمام وابستگی‌ها نصب شوند و از ابزار apt برای اتمام پیکربندی استفاده شود (`apt-get install -f` را خواهیم دید).

ما همچنین می‌توانیم از `dpkg --force-depends` برای نادیده گرفتن وابستگی‌ها یا `--force-conflicts` برای بستن چشم‌ها بر روی تداخل‌های احتمالی یا `--force-reinstall` برای نصب مجدد استفاده کنیم، اما فراموش نکنید که هر دستور `--force` می‌تواند باعث ایجاد مشکل شده و سیستم را در وضعیت **ناپایدار** قرار دهد.

dpkg -L | --listfiles ، برای لیست کردن فایل‌های نصب شده توسط یک پکیج:

```
root@ubuntu16-1:~# dpkg -L zip 
/.
/usr
/usr/share
/usr/share/doc
/usr/share/doc/zip
/usr/share/doc/zip/WHATSNEW
/usr/share/doc/zip/TODO
/usr/share/doc/zip/copyright
/usr/share/doc/zip/changelog.Debian.gz
/usr/share/man
/usr/share/man/man1
/usr/share/man/man1/zipsplit.1.gz
/usr/share/man/man1/zipcloak.1.gz
/usr/share/man/man1/zip.1.gz
/usr/share/man/man1/zipnote.1.gz
/usr/bin
/usr/bin/zipnote
/usr/bin/zipcloak
/usr/bin/zip
/usr/bin/zipsplit
/usr/share/doc/zip/changelog.gz
```

dpkg -S | --search ، اگر مطمئن نیستیم کدام پکیج یک فایل را نصب کرده است، dpkg -S ممکن است بتواند به ما بگوید:

```
root@ubuntu16-1:~# dpkg -S /usr/bin/zipcloak 
zip: /usr/bin/zipcloak
```

توجه: ما همچنین می‌توانیم از `dpkg -S string` استفاده کنیم و این دستور از جستجوی regular expression در سیستم‌فایل برای یافتن هرگونه مطابقت با رشته‌ای که تایپ کرده‌ایم استفاده می‌کند.

dpkg -s | --status ، بررسی نصب بودن یا نبودن یک پکیج:

```
root@ubuntu16-1:~# dpkg -s zip
Package: zip
Status: deinstall ok installed
Priority: optional
Section: utils
Installed-Size: 573
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture: amd64
Multi-Arch: foreign
Version: 3.0-11
Depends: libbz2-1.0, libc6 (>= 2.14)
Recommends: unzip
Description: Archiver for .zip files
 This is InfoZIP's zip program. It produces files that are fully
 compatible with the popular PKZIP program; however, the command line
 options are not identical. In other words, the end result is the same,
 but the methods differ. :-)
 .
 This version supports encryption.
Original-Maintainer: Santiago Vila <sanvila@debian.org>
Homepage: http://www.info-zip.org/Zip.html
```

dpkg -c | --contents ، محتویات یک پکیج “.deb” را در قالب لیست طولانی نمایش می‌دهد:

```
root@ubuntu16-1:~# dpkg -c zip_3.0-11_amd64.deb 
drwxr-xr-x root/root         0 2015-08-16 22:10 ./
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/share/
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/share/doc/
drwxr-xr-x root/root         0 2015-08-16 22:11 ./usr/share/doc/zip/
-rw-r--r-- root/root     15731 2008-07-01 20:27 ./usr/share/doc/zip/WHATSNEW
-rw-r--r-- root/root      6675 2008-06-12 19:03 ./usr/share/doc/zip/TODO
-rw-r--r-- root/root      3815 2015-08-16 14:25 ./usr/share/doc/zip/copyright
-rw-r--r-- root/root      1040 2015-08-16 22:11 ./usr/share/doc/zip/changelog.Debian.gz
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/share/man/
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/share/man/man1/
-rw-r--r-- root/root       596 2015-08-16 22:10 ./usr/share/man/man1/zipsplit.1.gz
-rw-r--r-- root/root       951 2015-08-16 22:10 ./usr/share/man/man1/zipcloak.1.gz
-rw-r--r-- root/root     28488 2015-08-16 22:10 ./usr/share/man/man1/zip.1.gz
-rw-r--r-- root/root       793 2015-08-16 22:10 ./usr/share/man/man1/zipnote.1.gz
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/bin/
-rwxr-xr-x root/root     81840 2015-08-16 22:10 ./usr/bin/zipnote
-rwxr-xr-x root/root     86224 2015-08-16 22:10 ./usr/bin/zipcloak
-rwxr-xr-x root/root    192520 2015-08-16 22:10 ./usr/bin/zip
-rwxr-xr-x root/root     81840 2015-08-16 22:10 ./usr/bin/zipsplit
lrwxrwxrwx root/root         0 2015-08-16 22:10 ./usr/share/doc/zip/changelog.gz -> CHANGES.gz
```

گزینه `-c` را با -C اشتباه نگیرید، -C پکیج‌هایی را که به طور ناقص نصب شده‌اند بررسی می‌کند.

> **debconf چیست؟**
>
> هنگامی که پکیج‌ها در حال نصب هستند، debconf سوالاتی از کاربر می‌پرسد که محتویات فایل‌های پیکربندی سیستم مرتبط با آن پکیج را تعیین می‌کند و ترجیحات کاربر/مدیر را در یک دیتابیس ذخیره می‌کند.
>
> بعداً که پکیج‌ها در حال نصب هستند، اسکریپت‌های آن‌ها از ترجیحات پیکربندی در دیتابیس برای تولید فایل‌های پیکربندی و انجام سایر وظایف مدیریتی استفاده می‌کنند. این کار باعث صرفه‌جویی در زحمت ویرایش دستی فایل‌های پیکربندی و همچنین انتظار برای نصب هر پکیج جداگانه قبل از پاسخگویی به سوالات خاص پیکربندی می‌شود.

### dpkg-reconfigure

پس از نصب پکیج، امکان بازگشت و تغییر پیکربندی یک پکیج با استفاده از برنامه dpkg-reconfigure (یا برنامه دیگری مانند Synaptic) وجود دارد. برای مثال امتحان کنید:

```
root@ubuntu16-1:~# dpkg-reconfigure tzdata
```

dpkg -r | --remove ، حذف یک پکیج با استفاده از نام آن:

```
root@ubuntu16-1:~# dpkg -r telnet
(Reading database ... 229441 files and directories currently installed.)
Removing telnet (0.17-40) ...
Processing triggers for man-db (2.7.5-1) ...
```

dpkg به طور پیش‌فرض به وابستگی‌ها اهمیت می‌دهد و آن‌ها را حذف نمی‌کند (به همین دلیل است که فایل .deb را نمی‌گیرد، بلکه به نام پکیج برای بررسی وابستگی‌ها نیاز دارد)، با این حال می‌توانیم با دستور `dpkg -r --force-depends` آن را مجبور کنیم که توصیه نمی‌شود. بهتر است از یک مدیریت پکیج استفاده کنید که وابستگی‌ها را مدیریت می‌کند تا اطمینان حاصل شود که سیستم در وضعیت پایداری قرار دارد.

توجه: dpkg -P | --purge پکیج را به طور کامل پاک (purge) می‌کند! ما همچنین می‌توانیم از گزینه ‘P’ به جای ‘r’ استفاده کنیم که پکیج را به همراه فایل‌های پیکربندی حذف می‌کند. گزینه ‘r’ فقط پکیج را حذف می‌کند و فایل‌های پیکربندی را باقی می‌گذارد.

```
root@ubuntu16-1:~# dpkg -P vsftpd
(Reading database ... 229487 files and directories currently installed.)
Removing vsftpd (3.0.3-3ubuntu2) ...
Purging configuration files for vsftpd (3.0.3-3ubuntu2) ...
Processing triggers for man-db (2.7.5-1) ...
```

گزینه -P را با -p اشتباه نگیرید، -p اطلاعات مربوط به پکیج را چاپ می‌کند.

dpkg گزینه‌ها و سوئیچ‌های زیادی دارد، برای اطلاعات بیشتر dpkg --help را امتحان کنید:

```
root@ubuntu16-1:~# dpkg --help
Usage: dpkg [<option> ...] <command>

Commands:
  -i|--install       <.deb file name> ... | -R|--recursive <directory> ...
  --unpack           <.deb file name> ... | -R|--recursive <directory> ...
  -A|--record-avail  <.deb file name> ... | -R|--recursive <directory> ...
  --configure        <package> ... | -a|--pending
  --triggers-only    <package> ... | -a|--pending
  -r|--remove        <package> ... | -a|--pending
  -P|--purge         <package> ... | -a|--pending
  -V|--verify <package> ...        Verify the integrity of package(s).
  --get-selections [<pattern> ...] Get list of selections to stdout.
  --set-selections                 Set package selections from stdin.
  --clear-selections               Deselect every non-essential package.
  --update-avail [<Packages-file>] Replace available packages info.
  --merge-avail [<Packages-file>]  Merge with info from file.
  --clear-avail                    Erase existing available info.
  --forget-old-unavail             Forget uninstalled unavailable pkgs.
  -s|--status <package> ...        Display package status details.
  -p|--print-avail <package> ...   Display available version details.
  -L|--listfiles <package> ...     List files 'owned' by package(s).
  -l|--list [<pattern> ...]        List packages concisely.
  -S|--search <pattern> ...        Find package(s) owning file(s).
  -C|--audit [<package> ...]       Check for broken package(s).
  --yet-to-unpack                  Print packages selected for installation.
  --predep-package                 Print pre-dependencies to unpack.
  --add-architecture <arch>        Add <arch> to the list of architectures.
  --remove-architecture <arch>     Remove <arch> from the list of architectures.
  --print-architecture             Print dpkg architecture.
  --print-foreign-architectures    Print allowed foreign architectures.
  --assert-<feature>               Assert support for the specified feature.
  --compare-versions <a> <op> <b>  Compare version numbers - see below.
  --force-help                     Show help on forcing.
  -Dh|--debug=help                 Show help on debugging.

  -?, --help                       Show this help message.
      --version                    Show the version.

Assertable features: support-predepends, working-epoch, long-filenames,
  multi-conrep, multi-arch, versioned-provides.

Use dpkg with -b, --build, -c, --contents, -e, --control, -I, --info,
  -f, --field, -x, --extract, -X, --vextract, --ctrl-tarfile, --fsys-tarfile
on archives (type dpkg-deb --help).

Options:
  --admindir=<directory>     Use <directory> instead of /var/lib/dpkg.
  --root=<directory>         Install on a different root directory.
  --instdir=<directory>      Change installation dir without changing admin dir.
  --path-exclude=<pattern>   Do not install paths which match a shell pattern.
  --path-include=<pattern>   Re-include a pattern after a previous exclusion.
  -O|--selected-only         Skip packages not selected for install/upgrade.
  -E|--skip-same-version     Skip packages whose same version is installed.
  -G|--refuse-downgrade      Skip packages with earlier version than installed.
  -B|--auto-deconfigure      Install even if it would break some other package.
  --[no-]triggers            Skip or force consequential trigger processing.
  --verify-format=<format>   Verify output format (supported: 'rpm').
  --no-debsig                Do not try to verify package signatures.
  --no-act|--dry-run|--simulate
                             Just say what we would do - don't do it.
  -D|--debug=<octal>         Enable debugging (see -Dhelp or --debug=help).
  --status-fd <n>            Send status change updates to file descriptor <n>.
  --status-logger=<command>  Send status change updates to <command>'s stdin.
  --log=<filename>           Log status changes and actions to <filename>.
  --ignore-depends=<package>,...
                             Ignore dependencies involving <package>.
  --force-...                Override problems (see --force-help).
  --no-force-...|--refuse-...
                             Stop when problems encountered.
  --abort-after <n>          Abort after encountering <n> errors.

Comparison operators for --compare-versions are:
  lt le eq ne ge gt       (treat empty version as earlier than any version);
  lt-nl le-nl ge-nl gt-nl (treat empty version as later than any version);
  < << <= = >= >> >       (only for compatibility with control file syntax).

Use 'apt' or 'aptitude' for user-friendly package management.
```

هنگامی که با dpkg کار می‌کنیم، باید پکیج .deb را در اختیار داشته باشیم. اما مدیریت پکیج چطور؟ مدیریت پکیجی مانند apt چگونه مخازن نرم‌افزاری را در سیستم‌های مبتنی بر Debian پیدا می‌کند؟

### /etc/apt/sources.list

این فایل حاوی اطلاعاتی درباره مخازنی [آنلاین / راه دور] است که سیستم استفاده خواهد کرد. این فایل شامل خطوطی با فرمت زیر است:

```
deb location-of-resources distribution component(s)
```

```
root@ubuntu16-1:~# cat /etc/apt/sources.list
# deb cdrom:[Ubuntu 16.04.3 LTS _Xenial Xerus_ - Release amd64 (20170801)]/ xenial main restricted

# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://us.archive.ubuntu.com/ubuntu/ xenial main restricted
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://us.archive.ubuntu.com/ubuntu/ xenial-updates main restricted
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://us.archive.ubuntu.com/ubuntu/ xenial universe
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial universe
deb http://us.archive.ubuntu.com/ubuntu/ xenial-updates universe
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu 
## team, and may not be under a free licence. Please satisfy yourself as to 
## your rights to use the software. Also, please note that software in 
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://us.archive.ubuntu.com/ubuntu/ xenial multiverse
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial multiverse
deb http://us.archive.ubuntu.com/ubuntu/ xenial-updates multiverse
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://us.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse

## Uncomment the following two lines to add software from Canonical's
## 'partner' repository.
## This software is not part of Ubuntu, but is offered by Canonical and the
## respective vendors as a service to Ubuntu users.
# deb http://archive.canonical.com/ubuntu xenial partner
# deb-src http://archive.canonical.com/ubuntu xenial partner

deb http://security.ubuntu.com/ubuntu xenial-security main restricted
# deb-src http://security.ubuntu.com/ubuntu xenial-security main restricted
deb http://security.ubuntu.com/ubuntu xenial-security universe
# deb-src http://security.ubuntu.com/ubuntu xenial-security universe
deb http://security.ubuntu.com/ubuntu xenial-security multiverse
# deb-src http://security.ubuntu.com/ubuntu xenial-security multiverse
```

`deb` خطوط مربوط به پکیج‌های باینری هستند که می‌توانیم با apt نصب کنیم.
`deb-src` خطوط مربوط به پکیج‌های منبع (source) هستند (که توسط apt-get source $package دانلود می‌شوند) و سپس کامپایل می‌شوند. پکیج‌های منبع فقط در صورتی مورد نیاز هستند که بخواهیم پکیجی را خودمان کامپایل کنیم یا کد منبع را برای یافتن باگ بررسی کنیم. کاربران عادی نیازی به گنجاندن چنین مخازنی ندارند.

اجزای مخزن عبارتند از:

* Main - نرم‌افزارهای رسماً پشتیبانی شده.
* Restricted - نرم‌افزارهای پشتیبانی شده که تحت مجوز کاملاً رایگان نیستند.
* Universe - نرم‌افزارهای نگهداری شده توسط جامعه (community)، یعنی نرم‌افزارهایی که رسماً پشتیبانی نمی‌شوند.
* Multiverse - نرم‌افزارهایی که رایگان نیستند.

برخی توزیع‌ها مانند Ubuntu همچنین دارای دایرکتوری `/etc/apt/sources.list.d` هستند که راهی برای اضافه کردن ورودی‌های sources.list در فایل‌های مجزای `.list` فراهم می‌کند.

```
root@ubuntu16-1:/# ls /etc/apt/sources.list.d
peek-developers-ubuntu-stable-xenial.list
root@ubuntu16-1:/# cat /etc/apt/sources.list.d/peek-developers-ubuntu-stable-xenial.list 
deb http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial main
# deb-src http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial main
```

تمام فایل‌های `.list` جمع‌آوری شده و به فایل `/etc/apt/sources.list` اضافه می‌شوند.

## APT (Advanced Packaging Tool)

این یک سیستم مدیریت پکیج خط فرمان بسیار محبوب، رایگان، قدرتمند و مفید است که یک واسط (front end) برای سیستم مدیریت پکیج dpkg می‌باشد. در ابتدا برای پکیج‌های `.deb` دبیان طراحی شده بود.

Apt کاملاً خط فرمان است و رابط گرافیکی (GUI) ندارد. هرگاه از خط فرمان به همراه مشخص کردن نام پکیج برای نصب فراخوانی شود، آن پکیج را در لیست پیکربندی شده منابع مشخص شده در ‘/etc/apt/sources.list’ به همراه لیست وابستگی‌های آن پکیج پیدا کرده و آن‌ها را مرتب می‌کند و به طور خودکار همراه با پکیج فعلی نصب می‌کند، بنابراین کاربر نگران نصب وابستگی‌ها نخواهد بود.

این سیستم شامل دو دستور است:

* **apt-get**
* **apt-cache**

### **apt-get چیست؟**

apt-get ابزار خط فرمان برای کار با پکیج‌های نرم‌افزاری APT است که برای کار با کتابخانه APT (Advanced Packaging Tool) اوبونتو جهت انجام نصب پکیج‌های نرم‌افزاری جدید، حذف پکیج‌های موجود، ارتقای پکیج‌های موجود و حتی ارتقای کل سیستم‌عامل استفاده می‌شود.

**apt-get update**

این دستور به ما کمک می‌کند تا **لیستی از پکیج‌ها** را از مخازن مختلف موجود در سیستم خود دانلود کنیم و هنگامی که نسخه‌های جدیدی از پکیج‌ها و وابستگی‌های آن‌ها وجود دارد، آن‌ها را به‌روزرسانی کنیم. (پس از هرگونه تغییر در مخازن، باید apt-update را به صورت دستی اجرا کنیم، در غیر این صورت از کش محلی سیستم از مخازن قبلی استفاده خواهد شد)

```
root@ubuntu16-1:/# apt-get update 
Hit:1 http://us.archive.ubuntu.com/ubuntu xenial InRelease                     
Hit:2 http://security.ubuntu.com/ubuntu xenial-security InRelease              
Hit:3 http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial InRelease  
Hit:4 http://us.archive.ubuntu.com/ubuntu xenial-updates InRelease             
Hit:5 http://us.archive.ubuntu.com/ubuntu xenial-backports InRelease
Reading package lists... Done
Building dependency tree       
Reading state information... Done
287 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

برای دیدن اینکه چه پکیج‌هایی قابل ارتقا هستند:

```
root@ubuntu16-1:/# apt-get list --upgradable 
Listing... Done
appstream/xenial-updates 0.9.4-1ubuntu4 amd64 [upgradable from: 0.9.4-1ubuntu3]
apt/xenial-updates 1.2.29 amd64 [upgradable from: 1.2.24]
apt-transport-https/xenial-updates 1.2.29 amd64 [upgradable from: 1.2.24]
apt-utils/xenial-updates 1.2.29 amd64 [upgradable from: 1.2.24]
apturl/xenial-updates 0.5.2ubuntu11.2 amd64 [upgradable from: 0.5.2ubuntu11.1]
apturl-common/xenial-updates 0.5.2ubuntu11.2 amd64 [upgradable from: 0.5.2ubuntu11.1]
avahi-autoipd/xenial-updates 0.6.32~rc+dfsg-1ubuntu2.2 amd64 [upgradable from: 0.6.32~rc+dfsg-1ubuntu2]
...
<the output has been truncated>
```

**apt-get upgrade**

دستور ‘upgrade‘ برای ارتقای تمام پکیج‌های نرم‌افزاری نصب شده فعلی در سیستم استفاده می‌شود. تحت هیچ شرایطی پکیج‌های نصب شده فعلی حذف نمی‌شوند یا پکیج‌هایی که قبلاً نصب نشده‌اند برای برآوردن وابستگی‌های ارتقا، بازیابی و نصب نمی‌شوند.

نسخه‌های جدید پکیج‌های نصب شده فعلی که بدون تغییر وضعیت نصب پکیج دیگری قابل ارتقا نیستند، در نسخه فعلی خود باقی می‌مانند.

ابتدا باید یک update انجام شود تا apt-get بداند که نسخه‌های جدید پکیج‌ها در دسترس هستند.

```
root@ubuntu16-1:/# apt-get upgrade 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
The following packages were automatically installed and are no longer required:
  libecap3 squid squid-common squid-langpack
Use 'apt autoremove' to remove them.
The following packages have been kept back:
  libdrm-amdgpu1 libdrm2 libegl1-mesa libgbm1 libgl1-mesa-dri libmm-glib0
  libqmi-proxy libwayland-egl1-mesa libxatracker2 linux-generic-hwe-16.04
  linux-headers-generic-hwe-16.04 linux-image-generic-hwe-16.04
  mesa-vdpau-drivers modemmanager ubuntu-minimal
The following packages will be upgraded:
  appstream apt apt-transport-https apt-utils apturl apturl-common
  avahi-autoipd avahi-daemon avahi-utils bamfdaemon base-files binutils
  bsdutils compiz compiz-core compiz-gnome compiz-plugins-default
  console-setup console-setup-linux cracklib-runtime desktop-file-utils dpkg
  dpkg-dev firefox firefox-locale-en fonts-opensymbol friendly-recovery fwupd
  fwupdate fwupdate-signed gettext gettext-base ghostscript ghostscript-x
  ...
  <the output has been truncated>
  ...
  update-notifier-common ure util-linux uuid-runtime va-driver-all
  wireless-regdb x11-common xdg-user-dirs xorg xserver-common
  xserver-xorg-video-amdgpu-hwe-16.04 xserver-xorg-video-ati-hwe-16.04
  xserver-xorg-video-intel-hwe-16.04 xserver-xorg-video-nouveau-hwe-16.04
  xserver-xorg-video-radeon-hwe-16.04
272 upgraded, 0 newly installed, 0 to remove and 15 not upgraded.
Need to get 276 MB of archives.
After this operation, 23.1 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 base-files amd64 9.4ubuntu4.7 [65.9 kB]
Get:2 http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial/main amd64 peek amd64 1.3.1-0~ppa23~ubuntu16.04.1 [163 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 bsdutils amd64 1:2.27.1-6ubuntu3.6 [51.2 kB]
...
<the output has been truncated>
...
Get:252 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 xserver-xorg-video-nouveau-hwe-16.04 amd64 1:1.0.15-2~16.04.1 [87.0 kB]
Fetched 270 MB in 4min 49s (933 kB/s)                                          
Extracting templates from packages: 100%
Preconfiguring packages ...
(Reading database ... 192776 files and directories currently installed.)
Preparing to unpack .../base-files_9.4ubuntu4.7_amd64.deb ...
Unpacking base-files (9.4ubuntu4.7) over (9.4ubuntu4.5) ...
Processing triggers for cracklib-runtime (2.9.2-1build2) ...
Processing triggers for man-db (2.7.5-1) ...
...
```

**apt-get dist-upgrade**

dist-upgrade علاوه بر انجام عملکرد upgrade، جابجایی وابستگی‌ها با نسخه‌های جدید پکیج‌ها (از جمله هسته یا kernel) را نیز به صورت هوشمند مدیریت می‌کند.

اگر می‌خواهیم ارتقا دهیم و نگران این نیستیم که پکیج‌های نرم‌افزاری برای تکمیل وابستگی‌ها اضافه یا حذف شوند، از زیردستور ‘dist-upgrade‘ استفاده کنید.

apt-get دارای یک سیستم حل تداخل "هوشمند" است و در صورت لزوم سعی می‌کند مهم‌ترین پکیج‌ها را به قیمت پکیج‌های کمتر مهم ارتقا دهد.

```
root@ubuntu16-1:~# apt-get dist-upgrade 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
The following packages were automatically installed and are no longer required:
  libecap3 squid squid-common squid-langpack
Use 'apt autoremove' to remove them.
The following NEW packages will be installed:
  amd64-microcode intel-microcode iucode-tool libdrm-common libllvm6.0
  libqmi-glib5 linux-headers-4.15.0-39 linux-headers-4.15.0-39-generic
  linux-image-4.15.0-39-generic linux-modules-4.15.0-39-generic
  linux-modules-extra-4.15.0-39-generic ubuntu-advantage-tools
The following packages will be upgraded:
  libdrm-amdgpu1 libdrm2 libegl1-mesa libgbm1 libgl1-mesa-dri libmm-glib0
  libqmi-proxy libwayland-egl1-mesa libxatracker2 linux-generic-hwe-16.04
  linux-headers-generic-hwe-16.04 linux-image-generic-hwe-16.04
  mesa-vdpau-drivers modemmanager ubuntu-minimal
15 upgraded, 12 newly installed, 0 to remove and 0 not upgraded.
Need to get 91.8 MB of archives.
After this operation, 428 MB of additional disk space will be used.
Do you want to continue? [Y/n] 
...
Get:1 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 ubuntu-advantage-tools all 10ubuntu0.16.04.1 [11.5 kB]
Get:2 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 ubuntu-minimal amd64 1.361.2 [2,662 B]
Get:3 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 libdrm-common all 2.4.91-2~16.04.1 [4,764 B]
Get:4 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 libdrm2 amd64 2.4.91-2~16.04.1 [30.8 kB]
Get:5 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 iucode-tool amd64 1.5.1-1ubuntu0.1 [33.8 kB]
Get:6 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 libdrm-amdgpu1 amd64 2.4.91-2~16.04.1 [18.9 kB]
...
Fetched 91.8 MB in 53s (1,725 kB/s)                                            
Selecting previously unselected package ubuntu-advantage-tools.
(Reading database ... 192821 files and directories currently installed.)
Preparing to unpack .../ubuntu-advantage-tools_10ubuntu0.16.04.1_all.deb ...
Unpacking ubuntu-advantage-tools (10ubuntu0.16.04.1) ...
Preparing to unpack .../ubuntu-minimal_1.361.2_amd64.deb ...
Unpacking ubuntu-minimal (1.361.2) over (1.361) ...
Selecting previously unselected package libdrm-common.
Preparing to unpack .../libdrm-common_2.4.91-2~16.04.1_all.deb ...
Unpacking libdrm-common (2.4.91-2~16.04.1) ...
Preparing to unpack .../libdrm2_2.4.91-2~16.04.1_amd64.deb ...
Unpacking libdrm2:amd64 (2.4.91-2~16.04.1) over (2.4.76-1~ubuntu16.04.1) ...
Selecting previously unselected package iucode-tool.
Preparing to unpack .../iucode-tool_1.5.1-1ubuntu0.1_amd64.deb ...
Unpacking iucode-tool (1.5.1-1ubuntu0.1) ...
...
```

**apt-get autoremove**

زیردستور ‘autoremove‘ برای حذف خودکار پکیج‌هایی استفاده می‌شود که قطعاً برای برآوردن وابستگی‌های سایر پکیج‌ها نصب شده بودند اما اکنون دیگر مورد نیاز نیستند. برای مثال:

```
root@ubuntu16-1:~# apt-get autoremove
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages will be REMOVED:
  libecap3 squid squid-common squid-langpack
0 upgraded, 0 newly installed, 4 to remove and 0 not upgraded.
After this operation, 10.8 MB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 228575 files and directories currently installed.)
Removing squid (3.5.12-1ubuntu7.6) ...
Removing libecap3:amd64 (1.0.1-3ubuntu3) ...
Removing squid-common (3.5.12-1ubuntu7.6) ...
Removing squid-langpack (20150704-1) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for libc-bin (2.23-0ubuntu10) ...
```

**apt-get install**

یک پکیج را با مشخص کردن نام یک پکیج واحد به صورت زیر نصب کنید یا چندین پکیج را با لیست کردن تمام نام‌های آن‌ها به طور همزمان نصب کنید:

```
root@ubuntu16-1:~# apt-get install vsftpd
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  openbsd-inetd pure-ftpd-common
Use 'apt autoremove' to remove them.
The following packages will be REMOVED:
  pure-ftpd
The following NEW packages will be installed:
  vsftpd
0 upgraded, 1 newly installed, 1 to remove and 2 not upgraded.
Need to get 115 kB of archives.
After this operation, 213 kB disk space will be freed.
Do you want to continue? [Y/n] y
Get:1 http://mirror.hmc.edu/ubuntu xenial/main amd64 vsftpd amd64 3.0.3-3ubuntu2 [115 kB]
Fetched 115 kB in 7s (15.8 kB/s)                                               
Preconfiguring packages ...
(Reading database ... 229455 files and directories currently installed.)
Removing pure-ftpd (1.0.36-3.2build1) ...
Processing triggers for man-db (2.7.5-1) ...
Selecting previously unselected package vsftpd.
(Reading database ... 229443 files and directories currently installed.)
Preparing to unpack .../vsftpd_3.0.3-3ubuntu2_amd64.deb ...
Unpacking vsftpd (3.0.3-3ubuntu2) ...
Processing triggers for systemd (229-4ubuntu21.10) ...
Processing triggers for ureadahead (0.100.0-19) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up vsftpd (3.0.3-3ubuntu2) ...
Installing new version of config file /etc/ftpusers ...
Processing triggers for systemd (229-4ubuntu21.10) ...
Processing triggers for ureadahead (0.100.0-19) ...
```

**apt-get remove**

حذف پکیج‌ها بدون فایل‌های پیکربندی آن‌ها:

```
root@ubuntu16-1:~# apt-get remove vsftpd 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  openbsd-inetd pure-ftpd-common
Use 'apt autoremove' to remove them.
The following packages will be REMOVED:
  vsftpd
0 upgraded, 0 newly installed, 1 to remove and 2 not upgraded.
After this operation, 336 kB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 229498 files and directories currently installed.)
Removing vsftpd (3.0.3-3ubuntu2) ...
Processing triggers for man-db (2.7.5-1) ...
```

**apt-get purge**

دستور apt با remove، فقط فایل‌های پکیج را حذف می‌کند اما فایل‌های پیکربندی در سیستم باقی می‌مانند. بنابراین برای حذف یک پکیج و فایل‌های پیکربندی آن، باید از purge استفاده کنیم.

```
root@ubuntu16-1:~# apt-get purge vsftpd
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  openbsd-inetd pure-ftpd-common
Use 'apt autoremove' to remove them.
The following packages will be REMOVED:
  vsftpd*
0 upgraded, 0 newly installed, 1 to remove and 2 not upgraded.
After this operation, 0 B of additional disk space will be used.
Do you want to continue? [Y/n] y
(Reading database ... 229446 files and directories currently installed.)
Removing vsftpd (3.0.3-3ubuntu2) ...
Purging configuration files for vsftpd (3.0.3-3ubuntu2) ...
```

#### apt-get clean

برای حذف پکیج‌های دانلود شده (.deb) که قبلاً نصب شده‌اند (و دیگر نیازی به آن‌ها نیست) استفاده می‌شود و با پاک کردن کش، فضای بیشتری آزاد می‌کند.

```
root@ubuntu16-1:~# apt-get clean
```

#### apt-get autoclean

برای حذف تمام آرشیوهای ذخیره شده در کش شما برای پکیج‌هایی که دیگر قابل دانلود نیستند (بنابراین پکیج‌هایی که دیگر در مخزن نیستند یا نسخه جدیدتری از آن‌ها در مخزن وجود دارد).

```
root@ubuntu16-1:~# apt-get autoclean 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
```

#### apt-get check

پکیج‌های نصب شده فعلی را برای هرگونه نصب خراب بررسی می‌کند.

```
root@ubuntu16-1:~# apt-get check 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
```

### **apt-cache چیست؟**

ابزار خط فرمان apt-cache برای جستجوی کش پکیج‌های نرم‌افزاری apt استفاده می‌شود. به زبان ساده، این ابزار برای جستجوی پکیج‌های نرم‌افزاری، جمع‌آوری اطلاعات پکیج‌ها و همچنین جستجوی پکیج‌های موجود که آماده نصب در سیستم‌های مبتنی بر Debian یا Ubuntu هستند استفاده می‌شود. فایل‌های کش شده APT در `/var/cache/apt/archives/` قرار دارند.

قبل از شروع استفاده از apt-cache، بهتر است ابتدا apt-get check را انجام دهید:

```
root@ubuntu16-1:~# apt-get check 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
```

زیرا این دستور با مقایسه وضعیت فعلی با وضعیت پکیج‌ها به گونه‌ای که در مخازن لیست شده‌اند، کش جدیدی می‌سازد. زمانی که apt-cache ساخته شد، می‌توانیم از آن کوئری بگیریم.

**apt-cache search**

پیدا کردن نام پکیج و توضیحات نرم‌افزار (نیازی به دسترسی root ندارد زیرا از کش کوئری می‌گیرد)

```
root@ubuntu16-1:~# apt-cache search netcat
netcat-openbsd - TCP/IP swiss army knife
corkscrew - tunnel TCP connections through HTTP proxies
cryptcat - A lightweight version netcat extended with twofish encryption
kafkacat - generic producer and consumer for Apache Kafka
libexpect-perl - Expect.pm - Perl Expect interface
netcat - TCP/IP swiss army knife -- transitional package
netcat-traditional - TCP/IP swiss army knife
netrw - netcat like tool with nice features to transport files over network
netsed - network packet-altering stream editor
socat - multipurpose relay for bidirectional data transfer
```

**apt-cache show**

بررسی اطلاعات پکیج به همراه توضیحات کوتاه آن (مانند شماره نسخه، check sumها، اندازه، اندازه نصب شده، دسته‌بندی و غیره).

```
root@ubuntu16-1:~# apt-cache show netcat
Package: netcat
Priority: optional
Section: universe/net
Installed-Size: 30
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Ruben Molina <rmolina@udea.edu.co>
Architecture: all
Version: 1.10-41
Depends: netcat-traditional (>= 1.10-39)
Filename: pool/universe/n/netcat/netcat_1.10-41_all.deb
Size: 3438
MD5sum: ef404dcc16fdf7b7fc049e8499b5d6dd
SHA1: 6445cd6789821840978146ef1d33a2475e092c42
SHA256: 1865119bdf88c21df3c039f987492defac68668bf7293ba279e76943c0fd1785
Description-en: TCP/IP swiss army knife -- transitional package
 This is a "dummy" package that depends on lenny's default version of
 netcat, to ease upgrades. It may be safely removed.
Description-md5: 1353f8c1d079348417c2180319bdde09
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
```

**apt-cache showpkg**

زیردستور ‘showpkg‘ برای بررسی وابستگی‌ها برای پکیج‌های نرم‌افزاری خاص و اینکه آیا آن پکیج‌های وابستگی نصب شده‌اند یا خیر.

```
root@ubuntu16-1:~# apt-cache showpkg netcat
Package: netcat
Versions: 
1.10-41 (/var/lib/apt/lists/mirror.iranserver.com_ubuntu_dists_xenial_universe_binary-amd64_Packages) (/var/lib/apt/lists/mirror.iranserver.com_ubuntu_dists_xenial_universe_binary-i386_Packages) (/var/lib/dpkg/status)
 Description Language: 
                 File: /var/lib/apt/lists/mirror.iranserver.com_ubuntu_dists_xenial_universe_binary-amd64_Packages
                  MD5: 1353f8c1d079348417c2180319bdde09
 Description Language: en
                 File: /var/lib/apt/lists/mirror.iranserver.com_ubuntu_dists_xenial_universe_i18n_Translation-en
                  MD5: 1353f8c1d079348417c2180319bdde09


Reverse Depends: 
  netcat-openbsd,netcat 1.10-35
  playonlinux,netcat
  tomcat8-user,netcat
  tomcat7-user,netcat
  ltsp-client-core,netcat
  ltsp-client,netcat
 ...
Dependencies: 
1.10-41 - netcat-traditional (2 1.10-39) 
Provides: 
1.10-41 - 
Reverse Provides: 
netcat-traditional 1.10-41 (= )
netcat-openbsd 1.105-7ubuntu1 (= )
```

**apt-cache depends** و **apt-cache rdepends** به ما اجازه می‌دهند وابستگی‌ها را کوئری بگیریم.

```
root@ubuntu16-1:~# apt-cache depends telnet
telnet
  Depends: netbase
  Depends: libc6
  Depends: libstdc++6
  Replaces: <netstd>
root@ubuntu16-1:~# apt-cache rdepends telnet
telnet
Reverse Depends:
  ubuntu-standard
  ubuntu-standard
 |zssh
  tucnak
  ser2net
  procserv
  mininet
 |matanza
  libtelnet-utils
  libtelnet-dev
  lavapdu-daemon
 |dish
 |coturn
  coldfire
```

See telnet dependencies and see which other packages depends on telnet.

use **apt-cache stat** to show statistics of installed packages on your computer.

> dselect
>
> dselect یک واسط برای dpkg است که برای مدیریت پکیج‌های نرم‌افزاری در دبیان و توزیع‌های لینوکس مبتنی بر دبیان استفاده می‌شود. می‌توانید از dselect برای نصب پکیج‌ها در سیستم خود از آرشیوهای APT تعریف شده در /etc/apt/sources.list، بررسی پکیج‌های نصب شده قبلی، حذف و ارتقای پکیج‌ها استفاده کنید.
>
> ![](.gitbook/assets/debpack-dselect.png)

## aptitude

aptitude یکی دیگر از مدیریت پکیج‌های سطح بالا برای سیستم‌های مبتنی بر Debian است و می‌تواند برای انجام وظایف مدیریتی (نصب، ارتقا و حذف پکیج‌ها، همچنین مدیریت خودکار حل وابستگی‌ها) به روشی سریع و آسان استفاده شود. این ابزار همان قابلیت‌های apt-get و قابلیت‌های اضافی دیگر، مانند ارائه دسترسی به چندین نسخه از یک پکیج را فراهم می‌کند.

ممکن است نیاز داشته باشید ابتدا aptitude را نصب کنید ( `apt-get install aptitude` )

```
root@ubuntu16-1:~#apt-get install aptitude

root@ubuntu16-1:~# aptitude --help
aptitude 0.7.4
Usage: aptitude [-S fname] [-u|-i]
       aptitude [options] <action> ...
  Actions (if none is specified, aptitude will enter interactive mode):

 install      - Install/upgrade packages.
 remove       - Remove packages.
 purge        - Remove packages and their configuration files.
 hold         - Place packages on hold.
 unhold       - Cancel a hold command for a package.
 markauto     - Mark packages as having been automatically installed.
 unmarkauto   - Mark packages as having been manually installed.
 forbid-version - Forbid aptitude from upgrading to a specific package version.
 update       - Download lists of new/upgradable packages.
 safe-upgrade - Perform a safe upgrade.
 full-upgrade - Perform an upgrade, possibly installing and removing packages.
 build-dep    - Install the build-dependencies of packages.
 forget-new   - Forget what packages are "new".
 search       - Search for a package by name and/or expression.
 show         - Display detailed information about a package.
 versions     - Displays the versions of specified packages.
 clean        - Erase downloaded package files.
 autoclean    - Erase old downloaded package files.
 changelog    - View a package's changelog.
 download     - Download the .deb file for a package.
 reinstall    - Download and (possibly) reinstall a currently installed package.
 why          - Show the manually installed packages that require a package, or
                why one or more packages would require the given package.
 why-not      - Show the manually installed packages that lead to a conflict
                with the given package, or why one or more packages would
                lead to a conflict with the given package if installed.

  Options:
 -h             This help text.
 --no-gui       Do not use the GTK GUI even if available.
 -s             Simulate actions, but do not actually perform them.
 -d             Only download packages, do not install or remove anything.
 -P             Always prompt for confirmation of actions.
 -y             Assume that the answer to simple yes/no questions is 'yes'.
 -F format      Specify a format for displaying search results; see the manual.
 -O order       Specify how search results should be sorted; see the manual.
 -w width       Specify the display width for formatting search results.
 -f             Aggressively try to fix broken packages.
 -V             Show which versions of packages are to be installed.
 -D             Show the dependencies of automatically changed packages.
 -Z             Show the change in installed size of each package.
 -v             Display extra information. (may be supplied multiple times).
 -t [release]   Set the release from which packages should be installed.
 -q             In command-line mode, suppress the incremental progress
                indicators.
 -o key=val     Directly set the configuration option named 'key'.
 --with(out)-recommends    Specify whether or not to treat recommends as
                strong dependencies.
 -S fname       Read the aptitude extended status info from fname.
 -u             Download new package lists on startup.
                   (terminal interface only)
 -i             Perform an install run on startup.
                   (terminal interface only)

                   This aptitude does not have Super Cow Powers.
```

به عنوان مثال بیایید با استفاده از aptitude جستجو کرده و اطلاعاتی درباره netcat به دست آوریم:

```
root@ubuntu16-1:~# aptitude update
Hit http://mirror.iranserver.com/ubuntu xenial InRelease                  
Get: 1 http://mirror.iranserver.com/ubuntu xenial-updates InRelease [109 kB]
Hit http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial InRelease 
Get: 2 http://mirror.iranserver.com/ubuntu xenial-backports InRelease [107 kB]
Get: 3 http://mirror.iranserver.com/ubuntu xenial-security InRelease [107 kB]
Fetched 323 kB in 1s (208 kB/s)                             

root@ubuntu16-1:~# aptitude search netcat
i   netcat                         - TCP/IP swiss army knife -- transitional p
v   netcat:i386                    -                                          
i   netcat-openbsd                 - TCP/IP swiss army knife                  
p   netcat-openbsd:i386            - TCP/IP swiss army knife                  
i A netcat-traditional             - TCP/IP swiss army knife                  
p   netcat-traditional:i386        - TCP/IP swiss army knife                  

root@ubuntu16-1:~# aptitude show netcat
[  0%] Reading package lists
Package: netcat                          
State: installed
Automatically installed: no
Version: 1.10-41
Priority: optional
Section: universe/net
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture: all
Uncompressed Size: 30.7 k
Depends: netcat-traditional (>= 1.10-39)
Description: TCP/IP swiss army knife -- transitional package
 This is a "dummy" package that depends on lenny's default version of netcat, to ease upgrades. It may be safely removed.
Homepage: http://packages.debian.org/netcat-traditional
```

that is all.

.

.

.

Sources:

[https://www.ostechnix.com/difference-between-apt-and-apt-get-explained/](https://www.ostechnix.com/difference-between-apt-and-apt-get-explained/)

[https://www.tecmint.com/useful-basic-commands-of-apt-get-and-apt-cache-in-debian-ubuntu-linux/](https://www.tecmint.com/useful-basic-commands-of-apt-get-and-apt-cache-in-debian-ubuntu-linux/)

[https://www.tecmint.com/20-useful-dpkg-commands-for-debian-ubuntu-linux/](https://www.tecmint.com/20-useful-dpkg-commands-for-debian-ubuntu-linux/)

[https://www.debian.org/doc/manuals/debian-faq/ch-pkg_basics.en.html](https://www.debian.org/doc/manuals/debian-faq/ch-pkg_basics.en.html)

[https://www.linuxtopia.org/online_books/linux_system_administration/debian_linux_guide/ch06s04.html](https://www.linuxtopia.org/online_books/linux_system_administration/debian_linux_guide/ch06s04.html)

[https://www.itprotv.com/blog/apt-get-vs-apt-dist-upgrade/](https://www.itprotv.com/blog/apt-get-vs-apt-dist-upgrade/)

[https://www.computerhope.com/unix/apt-get.htm](https://www.computerhope.com/unix/apt-get.htm)

[https://www.linode.com/docs/tools-reference/package-managers/apt/](https://www.linode.com/docs/tools-reference/package-managers/apt/)

[https://www.itprotv.com/blog/apt-get-vs-apt-dist-upgrade/](https://www.itprotv.com/blog/apt-get-vs-apt-dist-upgrade/)

[https://askubuntu.com/questions/222348/what-is-the-difference-between-apt-get-update-and-upgrade](https://askubuntu.com/questions/222348/what-is-the-difference-between-apt-get-update-and-upgrade)

[https://www.itprotv.com/blog/apt-get-vs-apt-dist-upgrade/](https://www.itprotv.com/blog/apt-get-vs-apt-dist-upgrade/)

[https://www.lifewire.com/apt-get-command-examples-4144743](https://www.lifewire.com/apt-get-command-examples-4144743)

[https://help.ubuntu.com/community/Aptitude](https://help.ubuntu.com/community/Aptitude)

[https://linux.die.net/man/8/aptitude](https://linux.die.net/man/8/aptitude)

[https://www.tecmint.com/aptitude-package-manager-basic-usage/](https://www.tecmint.com/aptitude-package-manager-basic-usage/)

[https://askubuntu.com/questions/150604/what-is-the-difference-between-apt-get-install-and-aptitude-install](https://askubuntu.com/questions/150604/what-is-the-difference-between-apt-get-install-and-aptitude-install)
