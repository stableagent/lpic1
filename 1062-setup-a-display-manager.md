# 106.2 راه‌اندازی یک Display Manager

**وزن:** ۱

**توضیحات:** داوطلبان باید قادر به توصیف ویژگی‌های اساسی و پیکربندی display manager از نوع LightDM باشند. این هدف شامل آگاهی از display managerهای XDM (X Display Manger) ،GDM (Gnome Display Manager) و KDM (KDE Display Manager) است.

**حوزه‌های کلیدی دانش**:

* پیکربندی اولیه LightDM
* روشن یا خاموش کردن display manager
* تغییر متن خوش‌آمدگویی (greeting) در display manager
* آگاهی از XDM، KDM و GDM

**اصطلاحات و ابزارهای کاربردی:**

* lightdm
* /etc/lightdm/



یک محیط دسکتاپ (desktop environment) لینوکس، مجموعه‌ای از اپلیکیشن‌هاست که برای کار کردن خوب با یکدیگر و ارائه یک تجربه کاربری منسجم طراحی شده‌اند. یک محیط دسکتاپ معمولاً با یک مدیر ورود (login manager) همراه است. مدیر ورود همچنین به نام greeter یا display manager نیز شناخته می‌شود.

#### Display Manager چیست؟

مدیر نمایش (display manager) بخشی از کد است که صفحه ورود گرافیکی (GUI login screen) را برای دسکتاپ لینوکس شما فراهم می‌کند. پس از ورود به یک دسکتاپ گرافیکی، display manager کنترل را به window manager واگذار می‌کند.

> Kernel -> X -> DisplayManager -> Desktop

 هنگامی که از دسکتاپ خارج (log out) می‌شوید، کنترل دوباره به display manager داده می‌شود تا صفحه ورود را نمایش دهد و منتظر ورود دیگری بماند.

![](.gitbook/assets/displaymanager-centos7gdm.jpg)

چندین display manager وجود دارد - برخی با دسکتاپ‌های مربوط به خود ارائه می‌شوند در حالی که برخی دیگر خیر. 

 هر یک از display managerها را می‌توان برای صفحه ورود خود استفاده کرد، بدون توجه به اینکه از کدام دسکتاپ استفاده می‌کنید. این نشان‌دهنده انعطاف‌پذیری لینوکس و کدهای ماژولار و خوب نوشته شده است.

| دسکتاپ | Display Manager | توضیحات                                                |
| ------- | --------------- | ---------------------------------------------------- |
| GNOME   | GDM             | GNOME Display Manager                                |
| KDE     | KDM             | KDE Display Manager (up through Fedora 20)           |
| KDE     | SDDM            | Simple Desktop Display Manager (Fedora 21 and above) |
| LXDE    | LXDM            | LXDE Display Manager                                 |
|         | XDM             | Default X Window System Display Manager              |
|         | LightDM         | Lightweight Display Manage                           |

برای یافتن display manager پیش‌فرض می‌توانید این دستورات را امتحان کنید:

```
cat /etc/X11/default-display-manager 
###OR
systemctl status display-manager
```

{% hint style="success" %}
#### نصب و سوئیچ به lightdm

در این درس ما از CentOS7 استفاده می‌کنیم که به طور پیش‌فرض از gdm display manager استفاده می‌کند. بیایید با استفاده از دستورات زیر به lightdm سوئیچ کنیم و آن را بررسی کنیم:

```
yum install -y epel-release 
yum search lightdm
yum install -y lightdm.x86_64 
vim /etc/lightdm/lightdm.conf ### change enabled=true 
systemctl disable gdm.service 
systemctl enable lightdm.service 
reboot
```

اگر سرویس را ریبوت کنید، سیستم شما کرش (crash) خواهد کرد.
{% endhint %}

![lightdm greeter session](.gitbook/assets/displaymanager-centos7lightdm.jpg)

### LightDM

LightDM یک X display manager رایگان و متن‌باز است که هدف آن سبک بودن، سریع بودن، توسعه‌پذیر بودن و پشتیبانی از چندین دسکتاپ است.

>  **LightDM** مدیر نمایشی بود که در اوبونتو تا نسخه 16.04 LTS اجرا می‌شد، در حالی که در نسخه‌های بعدی اوبونتو با GDM جایگزین شد.

### /etc/lightdm

تنظیمات LightDM در دایرکتوری `/etc/lightdm` قرار دارد:

```
[root@centos7-1 ~]# ls -l /etc/lightdm/
total 20
-rw-r--r-- 1 root root   40 Nov 27  2017 keys.conf
-rw-r--r-- 1 root root 7009 Feb 10 01:16 lightdm.conf
drwxr-xr-x 2 root root    6 Nov 27  2017 lightdm.conf.d
-rw-r--r-- 1 root root 1325 Aug  4  2015 lightdm-gtk-greeter.conf
-rw-r--r-- 1 root root  452 Nov 27  2017 users.conf
```

بیایید برخی از تنظیمات داخل `/etc/lightdm/lightdm.conf` را ببینیم:

```
...
[Seat:*]

#autologin-user=
#allow-user-switching=true
#allow-guest=true
#greeter-session=example-gtk-gnome
#user-session=default

...
```

در برخی از توزیع‌ها (مانند اوبونتو) فایل‌های پیکربندی در دایرکتوری `lightdm.conf.d` قرار دارند:

```
[root@centos7-1 ~]# ls -l /etc/lightdm/lightdm.conf.d/
total 0
```

#### تغییر نشست greeter:

به عنوان مثال بیایید یک نشست greeter دیگر برای lightdm نصب کرده و آن را آزمایش کنیم:

```
[root@centos7-1 lightdm]# yum search lightdm | grep greeter
lightdm-autologin-greeter.noarch : Autologin greeter using LightDM
slick-greeter.x86_64 : A slick-looking LightDM greeter

[root@centos7-1 lightdm]# yum install -y slick-greeter.x86_64

[root@centos7-1 lightdm]# ls -lrth
total 24K
-rw-r--r-- 1 root root 1.3K Aug  4  2015 lightdm-gtk-greeter.conf
-rw-r--r-- 1 root root  452 Nov 27  2017 users.conf
-rw-r--r-- 1 root root   40 Nov 27  2017 keys.conf
drwxr-xr-x 2 root root    6 Nov 27  2017 lightdm.conf.d
-rw-r--r-- 1 root root 1.2K Dec 29  2017 slick-greeter.conf
-rw-r--r-- 1 root root 6.9K Feb 10 01:16 lightdm.conf

```

سپس باید `lightdm.conf` را ویرایش کرده و خط زیر را تغییر دهیم:

```
greeter-session=slick-greeter
```

با استفاده از `systemctl restart lightdm` سرویس را ری‌استارت کرده و نتیجه را ببینید:

![slick greeter for lightdm](.gitbook/assets/displaymanager-slickgreeter.jpg)

#### کنترل Display managerها

نصب و سوئیچ بین Display Managerهای مختلف بسیار آسان است. همانطور که در CentOS دیدیم، می‌توانیم DM جدید را از طریق دستورات `yum` یا `apt` نصب کنیم. سپس ممکن است برخی تغییرات در فایل‌های پیکربندی لازم باشد، به عنوان مثال در `/etc/lightdm/lightdm.conf`. و در نهایت باید DM قبلی را غیرفعال و DM جدید را با دستورات `systemctl enable/disable lightdm` فعال کرده و سیستم را `reboot` کنیم.

برای غیرفعال کردن Display Manager و رفتن به حالت متنی (text mode)، این موضوع به توزیع شما بستگی دارد! می‌توانیم از دستور `telinit` استفاده کنیم یا هدف پیش‌فرض (default target) را از طریق دستور `systemctl set-default` تنظیم کنیم.



.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-106-2/](https://developer.ibm.com/tutorials/l-lpic1-106-2/)

[https://opensource.com/article/16/12/yearbook-best-couple-2016-display-manager-and-window-manager](https://opensource.com/article/16/12/yearbook-best-couple-2016-display-manager-and-window-manager)

[https://en.wikipedia.org/wiki/LightDM](https://en.wikipedia.org/wiki/LightDM)

[https://wiki.ubuntu.com/LightDM](https://wiki.ubuntu.com/LightDM)

.
