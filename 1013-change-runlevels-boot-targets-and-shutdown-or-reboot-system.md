# 101.3 تغییر runlevels / boot targets و خاموش کردن یا ری‌بوت (reboot) سیستم

## **101.3 تغییر runlevels / boot targets و خاموش کردن یا ری‌بوت (reboot) سیستم**

**وزن:** 3

**توضیحات:** داوطلبان باید قادر به مدیریت SysVinit runlevel یا systemd boot target سیستم باشند. این هدف شامل تغییر به حالت تک کاربره (single user mode)، خاموش کردن یا ری‌بوت کردن سیستم است. داوطلبان باید بتوانند قبل از تعویض runlevels / boot targets به کاربران هشدار داده و فرآیندها را به درستی متوقف کنند. این هدف همچنین شامل تنظیم SysVinit runlevel یا systemd boot target پیش‌فرض است. همچنین شامل آگاهی از Upstart به عنوان جایگزینی برای SysVinit یا systemd است.

**حوزه‌های کلیدی دانش:**

* تنظیم runlevel یا boot target پیش‌فرض
* جابجایی بین runlevels / boot targets از جمله حالت تک کاربره (single user mode)
* خاموش کردن (Shutdown) و ری‌بوت (reboot) از خط فرمان
* هشدار به کاربران قبل از تعویض runlevels / boot targets یا سایر رویدادهای مهم سیستم
* متوقف کردن صحیح فرآیندها (processes)

**اصطلاحات و ابزارهای کاربردی:**

* /etc/inittab
* shutdown
* init
* /etc/init.d/
* telinit
* systemd
* systemctl
* /etc/systemd/
* /usr/lib/systemd/
* wall

در درس قبلی توالی بوت سیستم در لینوکس را توضیح دادیم، سپس با Sysv، Upstart و systemd به عنوان مدیران سرویس‌های مختلف در دنیای لینوکس آشنا شدیم. در این درس یاد می‌گیریم که چگونه با استفاده از این مدیران سرویس، کنترل سرویس‌های سیستم لینوکس خود را به دست بگیریم.

بیایید با SysV شروع کنیم. SysV چیزی نیست جز تعداد زیادی اسکریپت اجرایی که پس از فرآیند init اجرا می‌شوند. چگونه می‌توانیم تعریف کنیم که کدام سرویس باید هنگام شروع به کار کامپیوتر اجرا شود؟ خوشحالم که پرسیدید. به نظر می‌رسد که فایل‌های پیکربندی متنی حجیمی مورد نیاز باشد، اینجاست که **runlevels** وارد بازی می‌شوند.

### runlevels

یک runlevel یکی از حالت‌هایی است که یک سیستم‌عامل مبتنی بر یونیکس در آن اجرا می‌شود. هر runlevel دارای تعداد معینی از سرویس‌های متوقف شده یا شروع شده است که به کاربر اجازه می‌دهد رفتار ماشین را کنترل کند. runlevels از نیاز به ویرایش دستی چند فایل بزرگ جلوگیری می‌کنند.

به طور سنتی، **هفت** runlevel وجود دارد که از صفر تا شش شماره‌گذاری شده‌اند. و تفاوت‌هایی بین سیستم‌های مبتنی بر Debian و RedHat وجود دارد:

| Runlevel | Debian               | RedHat                       |
| -------- | -------------------- | ---------------------------- |
| **0**    | **Halt**             | **Halt**                     |
| **1**    | **Single User Mode** | **Single User Mode**         |
| **2**    | Full,Multi-User,GUI  | Multi-User, No Net           |
| **3**    | Nothing              | Multi-User, with Net, No GUI |
| **4**    | **Nothing**          | **Not used**                 |
| **5**    | Nothing              | Full,Multi-User,GUI          |
| **6**    | **Reboot**           | **Reboot**                   |

CentOS 5 آخرین نسخه‌ای بود که از SysV استفاده کرد و از CentOS 7 به بعد از Systemd استفاده می‌شود.

### runlevel

برای یافتن runlevel فعلی و قبلی، از دستور runlevel استفاده می‌شود:

```
[root@centos5-1 ~]# runlevel
N 5
```

در خروجی بالا، حرف ‘N’ نشان می‌دهد که runlevel از زمان بوت شدن سیستم تغییر نکرده است. و 5 runlevel فعلی است.

اکنون که درباره runlevelها می‌دانیم، چگونه می‌توانیم بین آن‌ها جابجا شویم؟ خب کافی است به 'init' بگویید که چه runlevelی می‌خواهید.

## telinit

telinit برای تغییر runlevel سیستم SysV استفاده می‌شود.

```
telinit  RUNLEVEL
```

بیایید آن را روی یک ماشین CentOS 5 امتحان کنیم:

```
[root@centos5-1 ~]# runlevel
N 5
[root@centos5-1 ~]# telinit 3
```

و سیستم به runlevel 3 می‌رود، بیایید به runlevel 5 برگردیم:

```
[root@centos5-1 ~]# telinit 5
```

## init

init (به عنوان یک دستور) یک مقداردهی اولیه کنترل فرآیند مانند telinit است.

```
[root@centos5-1 ~]# init 3
```

{% hint style="info" %}
#### telinit در مقابل init

telinit ابزار کوچکتری است که به init اطلاع می‌دهد چه زمانی نیاز به تغییر runlevel دارد. بنابراین می‌توانیم از "telinit" استفاده کنیم تا به init بگوییم (tell init) که نیاز به تعویض runlevel دارد. telinit در واقع به دستور init لینک شده است و امکان استفاده از دستور init به جای آن وجود دارد اما توصیه نمی‌شود.

```
[root@centos5-1 ~]# ls -l /sbin/telinit 
lrwxrwxrwx 1 root root 4 Aug 26 23:20 /sbin/telinit -> init
```
{% endhint %}

چندین راه برای تغییر runlevelها وجود دارد. برای ایجاد یک تغییر دائمی، می‌توانیم **/etc/inittab** را ویرایش کرده و سطح پیش‌فرضی را که در بالا دیدیم تغییر دهیم.

### /etc/inittab

پس از بوت شدن هسته لینوکس، برنامه init فایل /etc/inittab را می‌خواند تا **رفتار هر runlevel** را تعیین کند. مگر اینکه کاربر مقدار دیگری را به عنوان پارامتر بوت هسته تعیین کند، سیستم تلاش می‌کند وارد (شروع) runlevel پیش‌فرض شود (CentOS 5).

```
[root@centos5-1 ~]# cat /etc/inittab 
#
# inittab       This file describes how the INIT process should set up
#               the system in a certain run-level.
#
# Author:       Miquel van Smoorenburg, <miquels@drinkel.nl.mugnet.org>
#               Modified for RHS Linux by Marc Ewing and Donnie Barnes
#

# Default runlevel. The runlevels used by RHS are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
# 
id:5:initdefault:

# System initialization.
si::sysinit:/etc/rc.d/rc.sysinit

l0:0:wait:/etc/rc.d/rc 0
l1:1:wait:/etc/rc.d/rc 1
l2:2:wait:/etc/rc.d/rc 2
l3:3:wait:/etc/rc.d/rc 3
l4:4:wait:/etc/rc.d/rc 4
l5:5:wait:/etc/rc.d/rc 5
l6:6:wait:/etc/rc.d/rc 6

# Trap CTRL-ALT-DELETE
ca::ctrlaltdel:/sbin/shutdown -t3 -r now

# When our UPS tells us power has failed, assume we have a few minutes
# of power left.  Schedule a shutdown for 2 minutes from now.
# This does, of course, assume you have powerd installed and your
# UPS connected and working correctly.  
pf::powerfail:/sbin/shutdown -f -h +2 "Power Failure; System Shutting Down"

# If power was restored before the shutdown kicked in, cancel it.
pr:12345:powerokwait:/sbin/shutdown -c "Power Restored; Shutdown Cancelled"


# Run gettys in standard runlevels
1:2345:respawn:/sbin/mingetty tty1
2:2345:respawn:/sbin/mingetty tty2
3:2345:respawn:/sbin/mingetty tty3
4:2345:respawn:/sbin/mingetty tty4
5:2345:respawn:/sbin/mingetty tty5
6:2345:respawn:/sbin/mingetty tty6

# Run xdm in runlevel 5
x:5:respawn:/etc/X11/prefdm -nodaemon
```

runlevel پیش‌فرض از ورودی `id:` در **/etc/inittab** تعیین می‌شود. نحوه تنظیم runlevelها به طور پیش‌فرض و نحوه پیکربندی آن‌ها تا حدی به توزیع خاصی که در حال اجرا هستید بستگی دارد.

فرمت هر خط در فایل inittab به صورت زیر است:

`id:runlevel:action:process`

در اینجا شرح این فیلدها آمده است:

* **id (کد شناسایی)** – شامل توالی یک تا چهار کاراکتر است که عملکرد آن را شناسایی می‌کند.
* **runlevels** – runlevelهایی را لیست می‌کند که این ورودی برای آن‌ها اعمال می‌شود.
* **action** – کدهای خاص در این فیلد به init می‌گویند که چگونه با فرآیند برخورد کند. مقادیر ممکن عبارتند از: initdefault، sysinit، boot، bootwait، wait و respawn.
* **process** – دستور یا اسکریپتی را که باید اجرا شود تعریف می‌کند.

اکنون بیایید ببینیم SysV چگونه مفهوم run levelها را پیاده‌سازی می‌کند.

### /etc/init.d

دایرکتوری /etc/init.d شامل اسکریپت‌هایی است که توسط ابزارهای init سیستم V (SysVinit) استفاده می‌شوند.

```
[root@centos5-1 ~]# ls  /etc/init.d/
acpid               hidd           netconsole       rpcidmapd
anacron             httpd          netfs            rpcsvcgssd
atd                 ip6tables      netplugd         saslauthd
auditd              ipmi           network          sendmail
autofs              iptables       NetworkManager   single
avahi-daemon        irda           nfs              smartd
avahi-dnsconfd      irqbalance     nfslock          sshd
bluetooth           iscsi          nscd             svnserve
conman              iscsid         ntpd             syslog
cpuspeed            killall        oddjobd          vmware-tools
crond               krb524         pand             vmware-tools-thinprint
cups                kudzu          pcscd            vncserver
cups-config-daemon  ldap           portmap          wdaemon
dnsmasq             lvm2-monitor   psacct           wpa_supplicant
dund                mcstrans       rawdevices       xfs
firstboot           mdmonitor      rdisc            ypbind
functions           mdmpd          readahead_early  yum-updatesd
gpm                 messagebus     readahead_later
haldaemon           microcode_ctl  restorecond
halt                multipathd     rpcgssd
```

همانطور که گفتیم، در SysV، برنامه init اولین فرآیندی است که اجرا می‌شود و متعاقباً برخی از سرویس‌های زیرساختی شروع می‌شوند. فایل‌های موجود در /etc/init.d اسکریپت‌های شل (shell scripts) هستند که به دستورات start، stop، restart و (در صورت پشتیبانی) reload برای مدیریت یک سرویس خاص پاسخ می‌دهند. اما SysV چگونه تعیین می‌کند که کدام سرویس‌ها در داخل /etc/init.d باید بر اساس runlevel پیش‌فرض شروع یا متوقف شوند؟ بیایید یک تصویر ترسیم کنیم:

![](.gitbook/assets/customizsysv-SysVScripts.jpg)

### /etc/rc.d/

SysV از گروه‌بندی استفاده می‌کند. **اسکریپت‌های هر runlevel گروه‌بندی شده و در /etc/rc{runlevel}.d/ قرار می‌گیرند** که در آن runlevel همان شماره runlevel است.

از آنجایی که بسیاری از سرویس‌ها ممکن است در runlevelهای مختلف وجود داشته باشند، فایل‌های اسکریپت واقعی در /etc/init.d نگه داشته می‌شوند و /etc/rc{runlevel}.d/ فقط به موارد مورد نیاز اشاره (point) می‌کنند.

```
[root@centos5-1 etc]# ls | grep rc.
rc0.d
rc1.d
rc2.d
rc3.d
rc4.d
rc5.d
rc6.d
rc.d
rc.local
rc.sysinit
```

بیایید به عنوان مثال نگاهی به rc.5 بیندازیم:

```
[root@centos5-1 etc]# ls -l rc5.d/
total 288
lrwxrwxrwx 1 root root 17 Aug 26 23:21 K01dnsmasq -> ../init.d/dnsmasq
lrwxrwxrwx 1 root root 24 Aug 26 23:21 K02avahi-dnsconfd -> ../init.d/avahi-dnsconfd
lrwxrwxrwx 1 root root 24 Aug 26 23:23 K02NetworkManager -> ../init.d/NetworkManager
lrwxrwxrwx 1 root root 17 Aug 26 23:23 K02oddjobd -> ../init.d/oddjobd
lrwxrwxrwx 1 root root 16 Aug 26 23:20 K05conman -> ../init.d/conman
lrwxrwxrwx 1 root root 19 Aug 26 23:21 K05saslauthd -> ../init.d/saslauthd
lrwxrwxrwx 1 root root 17 Aug 26 23:20 K05wdaemon -> ../init.d/wdaemon
lrwxrwxrwx 1 root root 16 Aug 26 23:20 K10psacct -> ../init.d/psacct
lrwxrwxrwx 1 root root 15 Aug 26 23:21 K15httpd -> ../init.d/httpd
lrwxrwxrwx 1 root root 18 Aug 26 23:22 K15svnserve -> ../init.d/svnserve
lrwxrwxrwx 1 root root 13 Aug 26 23:23 K20nfs -> ../init.d/nfs
lrwxrwxrwx 1 root root 14 Aug 26 23:23 K24irda -> ../init.d/irda
<output has been truncated>
lrwxrwxrwx 1 root root 23 Aug 26 23:20 S00microcode_ctl -> ../init.d/microcode_ctl
lrwxrwxrwx 1 root root 22 Aug 26 23:28 S03vmware-tools -> ../init.d/vmware-tools
lrwxrwxrwx 1 root root 25 Aug 26 23:20 S04readahead_early -> ../init.d/readahead_early
lrwxrwxrwx 1 root root 15 Aug 26 23:23 S05kudzu -> ../init.d/kudzu
lrwxrwxrwx 1 root root 16 Aug 26 23:20 S07iscsid -> ../init.d/iscsid
lrwxrwxrwx 1 root root 19 Aug 26 23:19 S08ip6tables -> ../init.d/ip6tables
lrwxrwxrwx 1 root root 18 Aug 26 23:19 S08iptables -> ../init.d/iptables
lrwxrwxrwx 1 root root 18 Aug 26 23:21 S08mcstrans -> ../init.d/mcstrans
lrwxrwxrwx 1 root root 17 Aug 26 23:21 S10network -> ../init.d/network
lrwxrwxrwx 1 root root 16 Aug 26 23:20 S11auditd -> ../init.d/auditd
lrwxrwxrwx 1 root root 21 Aug 26 23:21 S12restorecond -> ../init.d/restorecond
lrwxrwxrwx 1 root root 16 Aug 26 23:21 S12syslog -> ../init.d/syslog
lrwxrwxrwx 1 root root 18 Aug 26 23:19 S13cpuspeed -> ../init.d/cpuspeed
lrwxrwxrwx 1 root root 20 Aug 26 23:21 S13irqbalance -> ../init.d/irqbalance
lrwxrwxrwx 1 root root 15 Aug 26 23:20 S13iscsi -> ../init.d/iscsi
lrwxrwxrwx 1 root root 17 Aug 26 23:21 S13portmap -> ../init.d/portmap
lrwxrwxrwx 1 root root 17 Aug 26 23:23 S14nfslock -> ../init.d/nfslock
lrwxrwxrwx 1 root root 19 Aug 26 23:21 S15mdmonitor -> ../init.d/mdmonitor
<output has been truncated>
```

هر اسکریپت در هر runlevel با عملکردهای شروع (startup) یا خاموش کردن (shutdown) خود اجرا می‌شود، بسته به اینکه آن runlevel در حال بالا رفتن (going up) یا پایین آمدن (going down) باشد. **S** به معنای اسکریپت **شروع‌کننده** (starting) و **K** نشان می‌دهد که یک اسکریپت **متوقف‌کننده** (killing) است. ترتیب اقدامات با اعداد تعریف می‌شود. از دستور cat استفاده کنید تا ببینید داخل آن چیست:

```
[root@centos5-1 rc5.d]# cat S55sshd
#!/bin/bash
#
# Init file for OpenSSH server daemon
#
# chkconfig: 2345 55 25
# description: OpenSSH server daemon
#
# processname: sshd
# config: /etc/ssh/ssh_host_key
# config: /etc/ssh/ssh_host_key.pub
# config: /etc/ssh/ssh_random_seed
# config: /etc/ssh/sshd_config
# pidfile: /var/run/sshd.pid

# source function library
. /etc/rc.d/init.d/functions

# pull in sysconfig settings
[ -f /etc/sysconfig/sshd ] && . /etc/sysconfig/sshd

RETVAL=0
prog="sshd"

# Some functions to make the below more readable
KEYGEN=/usr/bin/ssh-keygen
SSHD=/usr/sbin/sshd
RSA1_KEY=/etc/ssh/ssh_host_key
RSA_KEY=/etc/ssh/ssh_host_rsa_key
DSA_KEY=/etc/ssh/ssh_host_dsa_key
PID_FILE=/var/run/sshd.pid

runlevel=$(set -- $(runlevel); eval "echo \$$#" )

do_rsa1_keygen() {
        if [ ! -s $RSA1_KEY ]; then
                echo -n $"Generating SSH1 RSA host key: "
                rm -f $RSA1_KEY
                if $KEYGEN -q -t rsa1 -f $RSA1_KEY -C '' -N '' >&/dev/null; then
                        chmod 600 $RSA1_KEY
                        chmod 644 $RSA1_KEY.pub
                        if [ -x /sbin/restorecon ]; then
                            /sbin/restorecon $RSA1_KEY.pub
                        fi
                        success $"RSA1 key generation"
                        echo
                else
                        failure $"RSA1 key generation"
                        echo
                        exit 1
                fi
        fi
}

do_rsa_keygen() {
        if [ ! -s $RSA_KEY ]; then
                echo -n $"Generating SSH2 RSA host key: "
                rm -f $RSA_KEY
                if $KEYGEN -q -t rsa -f $RSA_KEY -C '' -N '' >&/dev/null; then
                        chmod 600 $RSA_KEY
                        chmod 644 $RSA_KEY.pub
                        if [ -x /sbin/restorecon ]; then
                            /sbin/restorecon $RSA_KEY.pub
                        fi
                        success $"RSA key generation"
                        echo
                else
                        failure $"RSA key generation"
                        echo
                        exit 1
                fi
        fi
}

do_dsa_keygen() {
        if [ ! -s $DSA_KEY ]; then
                echo -n $"Generating SSH2 DSA host key: "
                rm -f $DSA_KEY
                if $KEYGEN -q -t dsa -f $DSA_KEY -C '' -N '' >&/dev/null; then
                        chmod 600 $DSA_KEY
                        chmod 644 $DSA_KEY.pub
                        if [ -x /sbin/restorecon ]; then
                            /sbin/restorecon $DSA_KEY.pub
                        fi
                        success $"DSA key generation"
                        echo
                else
                        failure $"DSA key generation"
                        echo
                        exit 1
                fi
        fi
}

do_restart_sanity_check()
{
        $SSHD -t
        RETVAL=$?
        if [ ! "$RETVAL" = 0 ]; then
                failure $"Configuration file or keys are invalid"
                echo
        fi
}

start()
{
        # Create keys if necessary
        do_rsa1_keygen
        do_rsa_keygen
        do_dsa_keygen

        cp -af /etc/localtime /var/empty/sshd/etc

        echo -n $"Starting $prog: "
        $SSHD $OPTIONS && success || failure
        RETVAL=$?
        [ "$RETVAL" = 0 ] && touch /var/lock/subsys/sshd
        echo
}

stop()
{
        echo -n $"Stopping $prog: "
        if [ -n "`pidfileofproc $SSHD`" ] ; then
            killproc $SSHD
        else
            failure $"Stopping $prog"
        fi
        RETVAL=$?
        # if we are in halt or reboot runlevel kill all running sessions
        # so the TCP connections are closed cleanly
        if [ "x$runlevel" = x0 -o "x$runlevel" = x6 ] ; then
            killall $prog 2>/dev/null
        fi
        [ "$RETVAL" = 0 ] && rm -f /var/lock/subsys/sshd
        echo
}

reload()
{
        echo -n $"Reloading $prog: "
        if [ -n "`pidfileofproc $SSHD`" ] ; then
            killproc $SSHD -HUP
        else
            failure $"Reloading $prog"
        fi
        RETVAL=$?
        echo
}

case "$1" in
        start)
                start
                ;;
        stop)
                stop
                ;;
        restart)
                stop
                start
                ;;
        reload)
                reload
                ;;
        condrestart)
                if [ -f /var/lock/subsys/sshd ] ; then
                        do_restart_sanity_check
                        if [ "$RETVAL" = 0 ] ; then
                                stop
                                # avoid race
                                sleep 3
                                start
                        fi
                fi
                ;;
        status)
                status -p $PID_FILE openssh-daemon
                RETVAL=$?
                ;;
        *)
                echo $"Usage: $0 {start|stop|restart|reload|condrestart|status}"
                RETVAL=1
esac
exit $RETVAL
```

و تمام این مکان‌ها، مکان‌هایی بودند که SysV برای مدیریت اسکریپت‌ها و runlevelها استفاده می‌کند. در سیستم Sysv می‌توانیم سرویس‌ها را با دستور `service` و دستور `chkconfig` مدیریت کنیم تا تعیین کنیم سرویس‌ها چگونه و چه زمانی شروع شوند. بعداً در مورد آن‌ها صحبت خواهیم کرد.

{% hint style="info" %}
**rc.local**

اما در مورد /etc/rc.local چطور؟ این فایل پس از اجرای تمام اسکریپت‌های دیگر سطح init اجرا می‌شود، بنابراین قرار دادن دستورات مختلفی که می‌خواهید هنگام راه‌اندازی اجرا شوند در آن امن است. همچنین این مکان خوبی برای قرار دادن اسکریپت‌های "عیب‌یابی" (troubleshooting) است. **اما فراموش نکنید که rc.local ممکن است در Upstart و Systemd به درستی کار نکند.** قبل از استفاده در محیط عملیاتی (production)، آن را تست و جستجو کرده و پیکربندی لازم را انجام دهید.
{% endhint %}

بیایید به موضوع خود برگردیم و با معادل runlevelها در systemd آشنا شویم. اگرچه /etc/inittab هنوز در سیستم‌های Systemd وجود دارد اما بخشی از پیکربندی نیست، ما چیزی به نام "Targets" داریم.

### systemd targets

مانند runlevelها، حالت‌هایی در سیستم systemd وجود دارد که سیستم ما می‌تواند در آن‌ها اجرا شود، در systemd به runlevelها به عنوان "**targets**" اشاره می‌شود. "**targets**" **به عنوان مجموعه‌ای از سرویس‌ها توصیف می‌شوند**. به معادل‌ها نگاه کنید:

```
   ┌─────────┬───────────────────┐
   │Runlevel │ Target            │
   ├─────────┼───────────────────┤
   │0        │ poweroff.target   │
   ├─────────┼───────────────────┤
   │1        │ rescue.target     │
   ├─────────┼───────────────────┤
   │2, 3, 4  │ multi-user.target │
   ├─────────┼───────────────────┤
   │5        │ graphical.target  │
   ├─────────┼───────────────────┤
   │6        │ reboot.target     │
   └─────────┴───────────────────┘
```

برای جابجایی بین Boot Targets از ابزار systemctl استفاده می‌کنیم.

### systemctl

Systemctl یک ابزار systemd است که مسئول کنترل سیستم systemd و مدیر سرویس (service manager) است. این ابزار می‌تواند کارهای زیادی انجام دهد اما آنچه ما در اینجا نیاز داریم و از آن استفاده می‌کنیم، تغییر boot targets است.

برای مشاهده boot target فعلی از دستور `systemctl get-default` استفاده کنید:

```
[root@centos7-1 ~]# systemctl get-default 
graphical.target
```

برای تغییر target از دستور `systemctl isolate xxxxx.target` استفاده کنید، به عنوان مثال:

```
[root@centos7-1 ~]# systemctl isolate rescue.target
```

```
[root@centos7-1 ~]# systemctl isolate multi-user.target
```

برای تنظیم target پیش‌فرض، دستور `systemctl set-default xxxxxx.target` را اجرا کنید:

```
[root@centos7-1 ~]# systemctl set-default graphical.target 
Removed symlink /etc/systemd/system/default.target.
Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/graphical.target.
```

اما systemd چگونه می‌داند چه کاری انجام دهد و چگونه کارها را انجام دهد؟ ما چیزی به نام "**Unit**" داریم. **مفهوم "Unit Files" جایگزین اسکریپت‌های init SysV برای سرویس‌ها می‌شود.**

### systemd unit files

انواع مختلفی از unit fileها وجود دارد و بهترین راه برای توصیف unit fileها این است که بگوییم **"آن چیزی است که باید شروع شود"**. بله آن یک "چیز" است زیرا انواع مختلفی از unit fileها وجود دارد. **هر unit file یک فایل متنی ساده است که یک واحد (unit)، کاری که انجام می‌دهد، آنچه باید قبل یا بعد از آن اجرا شود و سایر جزئیات را توصیف می‌کند.**

Unit fileها می‌توانند در چند مکان مختلف در سیستم شما ذخیره شوند. systemd به این ترتیب به دنبال فایل‌های واحد سیستم می‌گردد:

1. **/etc/systemd/system:** دایرکتوری ذخیره unit fileهایی که یک سرویس را گسترش می‌دهند. این دایرکتوری نسبت به unit fileهای واقع در هر جای دیگر سیستم اولویت دارد.
2. **/run/systemd/system:** دایرکتوری مکان زمان اجرا (runtime) برای unit fileها است.
3. **/usr/lib/systemd/system:** دایرکتوری مکان پیش‌فرضی است که unit fileها توسط پکیج‌ها در آنجا نصب می‌شوند. Unit fileها در دایرکتوری پیش‌فرض نباید تغییر داده شوند.

**Unit fileهای موجود در دایرکتوری‌های قبلی، موارد بعدی را بازنویسی (override) می‌کنند.** بیایید نگاهی به آن‌ها بیندازیم:

### 1./etc/systemd/system

```
[root@centos7-1 ~]# ls -l /etc/systemd/system
total 4
drwxr-xr-x. 2 root root   31 Oct 28  2017 basic.target.wants
drwxr-xr-x. 2 root root   31 Oct 28  2017 bluetooth.target.wants
lrwxrwxrwx. 1 root root   41 Oct 28  2017 dbus-org.bluez.service -> /usr/lib/systemd/system/bluetooth.service
lrwxrwxrwx. 1 root root   41 Oct 28  2017 dbus-org.fedoraproject.FirewallD1.service -> /usr/lib/systemd/system/firewalld.service
lrwxrwxrwx. 1 root root   44 Oct 28  2017 dbus-org.freedesktop.Avahi.service -> /usr/lib/systemd/system/avahi-daemon.service
lrwxrwxrwx. 1 root root   44 Oct 28  2017 dbus-org.freedesktop.ModemManager1.service -> /usr/lib/systemd/system/ModemManager.service
lrwxrwxrwx. 1 root root   46 Oct 28  2017 dbus-org.freedesktop.NetworkManager.service -> /usr/lib/systemd/system/NetworkManager.service
lrwxrwxrwx. 1 root root   57 Oct 28  2017 dbus-org.freedesktop.nm-dispatcher.service -> /usr/lib/systemd/system/NetworkManager-dispatcher.service
lrwxrwxrwx. 1 root root   36 Oct 28  2017 default.target -> /lib/systemd/system/graphical.target
drwxr-xr-x. 2 root root   87 Oct 28  2017 default.target.wants
drwxr-xr-x. 2 root root   38 Oct 28  2017 dev-virtio\x2dports-org.qemu.guest_agent.0.device.wants
lrwxrwxrwx. 1 root root   35 Oct 28  2017 display-manager.service -> /usr/lib/systemd/system/gdm.service
drwxr-xr-x. 2 root root   32 Oct 28  2017 getty.target.wants
drwxr-xr-x. 2 root root   65 Oct 28  2017 graphical.target.wants
drwxr-xr-x. 2 root root 4096 Oct 28  2017 multi-user.target.wants
drwxr-xr-x. 2 root root   26 Oct 28  2017 printer.target.wants
drwxr-xr-x. 2 root root   31 Oct 28  2017 remote-fs.target.wants
drwxr-xr-x. 2 root root  188 Oct 28  2017 sockets.target.wants
drwxr-xr-x. 2 root root   36 Oct 28  2017 spice-vdagentd.target.wants
drwxr-xr-x. 2 root root   26 Oct 28  2017 sssd.service.d
drwxr-xr-x. 2 root root  169 Oct 28  2017 sysinit.target.wants
drwxr-xr-x. 2 root root   44 Oct 28  2017 system-update.target.wants
drwxr-xr-x. 2 root root   29 Oct 28  2017 vmtoolsd.service.requires
```

### 2./run/systemd/system 

```
[root@centos7-1 ~]# ls -l /run/systemd/system/
total 8
-rw-r--r-- 1 root root  17 Jan  2  2019 session-1.scope
drwxr-xr-x 2 root root 160 Jan  2  2019 session-1.scope.d
-rw-r--r-- 1 root root  17 Jan  2  2019 user-1000.slice
drwxr-xr-x 2 root root 120 Jan  2  2019 user-1000.slice.d
```

### 3./usr/lib/systemd/system

```
[root@centos7-1 ~]# ls  /usr/lib/systemd/system

<output has been trancuated>


cryptsetup-pre.target                    runlevel0.target
cryptsetup.target                        runlevel1.target
ctrl-alt-del.target                      runlevel1.target.wants
cups-browsed.service                     runlevel2.target
cups.path                                runlevel2.target.wants
cups.service                             runlevel3.target
cups.socket                              runlevel3.target.wants
dbus-org.freedesktop.hostname1.service   runlevel4.target
dbus-org.freedesktop.import1.service     runlevel4.target.wants
dbus-org.freedesktop.locale1.service     runlevel5.target
dbus-org.freedesktop.login1.service      runlevel5.target.wants
dbus-org.freedesktop.machine1.service    runlevel6.target

<output has been trancuated>

nfs-rquotad.service                      umount.target
nfs-secure.service                       upower.service
nfs-server.service                       usb_modeswitch@.service
nfs.service                              usbmuxd.service
nfs-utils.service                        user.slice
nss-lookup.target                        var-lib-nfs-rpc_pipefs.mount
nss-user-lookup.target                   vgauthd.service
ntpdate.service                          virt-guest-shutdown.target
ntpd.service                             virtlockd.service
numad.service                            virtlockd.socket
oddjobd.service                          virtlogd.service
packagekit-offline-update.service        virtlogd.socket
packagekit.service                       vmtoolsd.service
paths.target                             wacom-inputattach@.service
plymouth-halt.service                    wpa_supplicant.service
plymouth-kexec.service                   zram.service
plymouth-poweroff.service
```

بیایید یکی از unit fileهای target را دنبال کنیم. `ls -al *.target` را امتحان کنید تا تمام فایل‌های target را ببینید. به عنوان مثال _default.target_:

```
[root@centos7-1 ~]# ls -al /usr/lib/systemd/system/default.target
lrwxrwxrwx. 1 root root 16 Oct 28  2017 /usr/lib/systemd/system/default.target -> graphical.target
```

بیایید نگاهی به آن _graphical.target_ بیندازیم:

```
[root@centos7-1 ~]# cat  /usr/lib/systemd/system/graphical.target
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Graphical Interface
Documentation=man:systemd.special(7)
Requires=multi-user.target
Wants=display-manager.service
Conflicts=rescue.service rescue.target
After=multi-user.target rescue.service rescue.target display-manager.service
AllowIsolate=yes
```

و آنچه نیاز دارد _multi-user.target_ است، بیایید ببینیم:

```
[root@centos7-1 ~]# cat  /usr/lib/systemd/system/multi-user.target
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Multi-User System
Documentation=man:systemd.special(7)
Requires=basic.target
Conflicts=rescue.service rescue.target
After=basic.target rescue.service rescue.target
AllowIsolate=yes
```

و این به _basic.target _ نیاز دارد:

```
[root@centos7-1 ~]# cat  /usr/lib/systemd/system/basic.target
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Basic System
Documentation=man:systemd.special(7)

Requires=sysinit.target
After=sysinit.target
Wants=sockets.target timers.target paths.target slices.target
After=sockets.target paths.target slices.target
```

در نهایت _sysinit.target_ :

```
[root@centos7-1 ~]# cat  /usr/lib/systemd/system/sysinit.target
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=System Initialization
Documentation=man:systemd.special(7)
Conflicts=emergency.service emergency.target
Wants=local-fs.target swap.target
After=local-fs.target swap.target emergency.service emergency.target
```

و _sysinit.target_ به چیزی نیاز ندارد. به نظر می‌رسد که ما به بالاترین سطح هدف در درخت خود رسیده‌ایم و می‌توانیم به عقب برگردیم.

استفاده از unit fileها در کنار مفهوم Targets باعث می‌شود Systemd در مقایسه با SysV انعطاف‌پذیرتر باشد.

## wall

زمان‌هایی وجود دارد که چندین کاربر به یک کامپیوتر سرور متصل (log in) هستند و ما نیاز داریم، مثلاً سرور را برای انجام برخی کارهای نگهداری (maintenance) ری‌استارت کنیم. البته، **راه درست این است که به همه کسانی که متصل هستند** در مورد فعالیت نگهداری اطلاع دهیم.

wall (مخفف write to all) یک ابزار خط فرمان یونیکس است که محتویات یک فایل یا ورودی استاندارد را به تمام کاربران متصل نمایش می‌دهد. معمولاً توسط root برای ارسال پیام خاموش شدن به تمام کاربران درست قبل از poweroff استفاده می‌شود.

```
wall [-n] [-t timeout] [-g group] [message | file]
```

```
OPTIONS         
       -n, --nobanner
              Suppress the banner.

       -t, --timeout timeout
              Abandon the write attempt to the terminals after timeout
              seconds.  This timeout must be a positive integer.  The
              default value is 300 seconds, which is a legacy from the time
              when people ran terminals over modem lines.

       -g, --group group
              Limit printing message to members of group defined as a group
              argument.  The argument can be group name or GID.

       -V, --version
              Display version information and exit.

       -h, --help
              Display help text and exit.
```

بیایید ارسال پیام "we are going down" را امتحان کنیم:

```
[root@centos5-1 ~]# wall "we are going down"

Broadcast message from root@centos6-1 (pts/1) (Sun Nov 11 00:46:58 2018):

we are going down
```

و **user1** چه چیزی دریافت خواهد کرد:

```
[user1@centos5-1 ~]$ 
Broadcast message from root@centos6-1 (pts/1) (Sun Nov 11 00:46:58 2018):

we are going down
```

گزینه‌ها ممکن است در برخی توزیع‌های قدیمی کار نکنند.

## shutdown

shutdown - متوقف کردن (Halt)، خاموش کردن (power-off) یا ری‌بوت کردن ماشین

```
shutdown [OPTIONS...] [TIME] [WALL...]
```

```
OPTIONS         
       The following options are understood:

       --help
           Print a short help text and exit.

       -H, --halt
           Halt the machine.

       -P, --poweroff
           Power-off the machine (the default).

       -r, --reboot
           Reboot the machine.

       -h
           Equivalent to --poweroff, unless --halt is specified.

       -k
           Do not halt, power-off, reboot, just write wall message.

       --no-wall
           Do not send wall message before halt, power-off, reboot.

       -c
           Cancel a pending shutdown. This may be used cancel the effect of
           an invocation of shutdown with a time argument that is not "+0"
           or "now".
       -t sec
           Tell init to wait sec seconds between sending processes the warning 
           and the kill signal, before changing to another runlevel.
[TIME]
           The time argument specifies when to perform the shutdown operation.

[WALL]        
           A message to be sent to all users, along with the standard shutdown notification.
```

shutdown کار خود را با ارسال سیگنال به فرآیند init انجام می‌دهد و از آن می‌خواهد runlevel را تغییر دهد. با توجه به عنوان قبلی، runlevel 0 برای halt کردن سیستم، runlevel 6 برای ری‌بوت کردن سیستم و runlevel 1 برای قرار دادن سیستم در حالتی استفاده می‌شود که وظایف مدیریتی در آن قابل انجام است (single-user mode).

```
root@ubuntu16-1:~# shutdown -r -t 15 +5 "Server will restart in 5 minutes. Please save your work."
Shutdown scheduled for Sun 2018-11-11 03:19:56 PST, use 'shutdown -c' to cancel.
root@ubuntu16-1:~# shutdown -c
```

ما می‌توانیم یک رشته زمانی (که معمولاً برای ساعت/دقیقه به صورت “now” یا “hh:mm” است) را تعیین کنیم:

```
root@ubuntu16-1:~# shutdown -r 10:10 "Server will be rebooted at 10:10am"
Shutdown scheduled for Tue 2018-11-13 10:10:00 PST, use 'shutdown -c' to cancel.
root@ubuntu16-1:~# shutdown -c
```

#### 

{% hint style="info" %}
#### halt در مقابل poweroff ! این کمی تاریخی است

* halt قبل از ACPI (Advanced Configuration and Power Interface) استفاده می‌شد که امروزه برق را برای ما قطع می‌کند. این دستور سیستم را متوقف می‌کرد و سپس پیامی با مضمون "اکنون خاموش کردن برق بلامانع است" چاپ می‌کرد. در آن زمان به جای دکمه پاور کنترل شده توسط ACPI در کامپیوترهای مدرن، سوئیچ‌های فیزیکی روشن/خاموش وجود داشت.
* poweroff، طبیعتاً سیستم را متوقف کرده و سپس خاموش کردن برق توسط ACPI را فراخوانی می‌کند.

این روزها halt به اندازه کافی هوشمند است که اگر ACPI فعال باشد، به طور خودکار poweroff را فراخوانی کند. در واقع، آن‌ها اکنون از نظر عملکردی معادل هستند.
{% endhint %}

### reboot

دستور reboot می‌تواند برای خاموش کردن یا ری‌بوت کردن لینوکس استفاده شود.

```
[root@centos7-1 ~]# reboot --help
reboot [OPTIONS...] [ARG]

Reboot the system.

     --help      Show this help
     --halt      Halt the machine
  -p --poweroff  Switch off the machine
     --reboot    Reboot the machine
  -f --force     Force immediate halt/power-off/reboot
  -w --wtmp-only Don't halt/power-off/reboot, just write wtmp record
  -d --no-wtmp   Don't write wtmp record
     --no-wall   Don't send wall message before halt/power-off/reboot
```

برای ری‌بوت کردن لینوکس، فقط دستور reboot را مستقیماً بدون هیچ گزینه‌ای فراخوانی کنید.

```
[root@centos7-1 ~]# reboot
```

این کار باعث خاموش شدن و راه‌اندازی مجدد ایمن (graceful) ماشین می‌شود. این همان اتفاقی است که وقتی روی restart در منوی خود کلیک می‌کنید، می‌افتد.

گزینه `-f` ماشین را به اجبار ری‌بوت می‌کند. این مشابه فشار دادن دکمه پاور روی CPU است. هیچ فرآیند خاموش شدنی صورت نمی‌گیرد. سیستم فوراً ریست می‌شود.

### halt

دستور بعدی دستور halt است. این دستور می‌تواند یک سیستم را خاموش کند اما گزینه‌های دیگری نیز دارد:

```
[root@centos7-1 ~]# halt --help
halt [OPTIONS...]

Halt the system.

     --help      Show this help
     --halt      Halt the machine
  -p --poweroff  Switch off the machine
     --reboot    Reboot the machine
  -f --force     Force immediate halt/power-off/reboot
  -w --wtmp-only Don't halt/power-off/reboot, just write wtmp record
  -d --no-wtmp   Don't write wtmp record
     --no-wall   Don't send wall message before halt/power-off/reboot
```

دستور halt نیز دارای گزینه force است اما سعی کنید از آن استفاده نکنید، زیرا ممکن است سیستم شما را در حالتی ناپایدار (inconsistent) قرار دهد.

### poweroff

دستور دیگری دقیقاً مشابه دستور halt وجود دارد. این دستور همان کارها را انجام می‌دهد و همان گزینه‌ها را می‌گیرد.

```
[root@centos7-1 ~]# poweroff --help
poweroff [OPTIONS...]

Power off the system.

     --help      Show this help
     --halt      Halt the machine
  -p --poweroff  Switch off the machine
     --reboot    Reboot the machine
  -f --force     Force immediate halt/power-off/reboot
  -w --wtmp-only Don't halt/power-off/reboot, just write wtmp record
  -d --no-wtmp   Don't write wtmp record
     --no-wall   Don't send wall message before halt/power-off/reboot
```

و کار ما تمام شد.

.

.

.



Sources:

[https://www.linuxjournal.com/article/1274](https://www.linuxjournal.com/article/1274)

[https://en.wikipedia.org/wiki/Runlevel](https://en.wikipedia.org/wiki/Runlevel)

[https://www.geeksforgeeks.org/run-levels-linux/](https://www.geeksforgeeks.org/run-levels-linux/)

[https://www.ostechnix.com/check-runlevel-linux/](https://www.ostechnix.com/check-runlevel-linux/)

[https://geek-university.com/linux/etc-inittab/](https://geek-university.com/linux/etc-inittab/)``[https://developer.ibm.com/tutorials/l-lpic1-101-3/](https://developer.ibm.com/tutorials/l-lpic1-101-3/)

[https://www.liquidweb.com/kb/linux-runlevels-explained/](https://www.liquidweb.com/kb/linux-runlevels-explained/)

[https://linux.die.net/man/8/telinit](https://linux.die.net/man/8/telinit)

[https://unix.stackexchange.com/questions/434560/what-differences-it-will-make-if-i-use-telinit-6-instead-of-reboot-command](https://unix.stackexchange.com/questions/434560/what-differences-it-will-make-if-i-use-telinit-6-instead-of-reboot-command)

[https://askubuntu.com/questions/5039/what-is-the-difference-between-etc-init-and-etc-init-d](https://askubuntu.com/questions/5039/what-is-the-difference-between-etc-init-and-etc-init-d)

[https://www.systutorials.com/239880/change-systemd-boot-target-linux/](https://www.systutorials.com/239880/change-systemd-boot-target-linux/)

[https://www.tecmint.com/change-runlevels-targets-in-systemd/](https://www.tecmint.com/change-runlevels-targets-in-systemd/)

[https://fedoramagazine.org/systemd-getting-a-grip-on-units/](https://fedoramagazine.org/systemd-getting-a-grip-on-units/)

[https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/](https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/)

[https://www.howtoforge.com/linux-wall-command/](https://www.howtoforge.com/linux-wall-command/)

[https://en.wikipedia.org/wiki/Wall\_(Unix)](https://en.wikipedia.org/wiki/Wall_\(Unix\))

[http://man7.org/linux/man-pages/man1/wall.1.html](http://man7.org/linux/man-pages/man1/wall.1.html)

[http://man7.org/linux/man-pages/man8/shutdown.8.html](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://www.computerhope.com/unix/ushutdow.htm](https://www.computerhope.com/unix/ushutdow.htm)

[https://unix.stackexchange.com/questions/42572/is-halt-the-same-as-shutdown-h-and-poweroff-the-same-as-shutdown-p/42581](https://unix.stackexchange.com/questions/42572/is-halt-the-same-as-shutdown-h-and-poweroff-the-same-as-shutdown-p/42581)

[https://www.tecmint.com/shutdown-poweroff-halt-and-reboot-commands-in-linux/](https://www.tecmint.com/shutdown-poweroff-halt-and-reboot-commands-in-linux/)

[https://www.binarytides.com/linux-command-shutdown-reboot-restart-system/](https://www.binarytides.com/linux-command-shutdown-reboot-restart-system/)
