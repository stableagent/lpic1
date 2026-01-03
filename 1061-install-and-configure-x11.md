# 106.1 نصب و پیکربندی X11

## **106.1 نصب و پیکربندی X11**

**وزن:** ۲

**توضیحات:** داوطلبان باید قادر به نصب و پیکربندی X11 باشند.

**حوزه‌های کلیدی دانش:**

* تأیید اینکه کارت گرافیک و مانیتور توسط یک X server پشتیبانی می‌شوند
* آگاهی از X font server
* درک و دانش اولیه از فایل پیکربندی X Window

**اصطلاحات و ابزارهای کاربردی:**

* /etc/X11/xorg.conf
* xhost
* DISPLAY
* xwininfo
* xdpyinfo
* X

در روزگاری که کامپیوترها بسیار گران بودند و بین کاربران زیادی به اشتراک گذاشته می‌شدند، ترمینال‌های X راهی کم‌هزینه برای استفاده کاربران از منابع یک کامپیوتر واحد فراهم می‌کردند. امروزه کامپیوترها آنقدر قدرتمند شده‌اند که دیگر کسی به فکر اشتراک‌گذاری منابع نیست، اما درس‌های تاریخی وجود دارد که باید در مورد آن‌ها بدانید!

### X

سیستم پنجره‌ای X که اغلب با نام X شناخته می‌شود، یک سیستم پنجره‌ای (windowing system) برای ایستگاه‌های کاری گرافیکی است که در MIT توسعه یافته است. این سیستم بر اساس مدل کلاینت/سرور (client/server) کار می‌کند: مدل کلاینت/سرور در سیستم X برعکس مدل‌های کلاینت/سرور معمولی عمل می‌کند، جایی که کلاینت روی ماشین محلی اجرا شده و از سرور درخواست خدمات می‌کند. در سیستم X، سرور روی ماشین محلی اجرا می‌شود و نمایشگر و خدمات خود را در اختیار برنامه‌های کلاینت قرار می‌دهد. برنامه‌های کلاینت ممکن است محلی باشند یا از راه دور در شبکه‌های مختلف وجود داشته باشند، اما X server به صورت شفاف (transparent) ظاهر می‌شود.

![X11 display server protocol](.gitbook/assets/x-overviewall.jpg)

علاوه بر نمایش پنجره‌ها برای کلاینت‌ها (اپلیکیشن‌ها)، **X server** همچنین دستگاه‌های ورودی مانند کیبورد، ماوس و صفحات لمسی را مدیریت می‌کند.

{% hint style="info" %}
سرور **XOrg** پیاده‌سازی رایگان و متن‌باز سرور نمایش برای X Window System بود که توسط بنیاد X.Org مدیریت می‌شد. نام X11 به نسخه ۱۱ سیستم پنجره‌ای X اشاره دارد.
{% endhint %}

در بسیاری از توزیع‌های مدرن لینوکس، سرور Display manager هنوز وجود دارد، اما X Window با راه‌کارهای جدیدی مانند wayland جایگزین شده است. 

### /etc/X11/xorg.conf

فایل `xorg.conf` فایلی است که برای پیکربندی X.Org Server استفاده می‌شود. `xorg.conf` معمولاً در `/etc/X11/xorg.conf` قرار دارد اما در توزیع‌های مدرن لینوکس دیگر وجود ندارد، بنابراین ما از یک نمونه `xorg.conf` برای توضیح آن استفاده می‌کنیم. 

فایل پیکربندی `xorg.conf` به بخش‌هایی (sections) سازماندهی شده است که می‌توانند با هر ترتیبی مشخص شوند. فرمت کلی بخش‌ها به این صورت است:

> Section "SectionName" 
>
>        SectionEntry ... 
>
> EndSection

بیایید نگاهی سریع به مهم‌ترین آن‌ها بیندازیم:

* Files - مسیرهای فایل‌هایی مانند fontpath

```
Section "Files"
    FontPath    "/usr/share/X11/fonts/misc"
    FontPath    "/usr/share/X11/fonts/100dpi/:unscaled"
    FontPath    "/usr/share/X11/fonts/75dpi/:unscaled"
    FontPath    "/usr/share/X11/fonts/Type1"
    FontPath    "/usr/share/X11/fonts/100dpi"
    FontPath    "/usr/share/X11/fonts/75dpi"
    FontPath    "/var/lib/defoma/x-ttcidfont-conf.d/dirs/TrueType"
EndSection
```

* Module - تعیین می‌کند کدام ماژول‌ها بارگذاری شوند

```
Section "Module"
    Load    "bitmap"
    Load    "ddc"
    Load    "dri"
    Load    "extmod"
    Load    "freetype"
    Load    "glx"
    Load    "int10"
    Load    "type1"
    Load    "vbe"
EndSection
```

به عنوان مثال `glx` وظیفه جلوه‌های گرافیکی سه بعدی را بر عهده دارد.

* InputDevice - کیبورد و اشاره‌گر (ماوس)

```
Section "InputDevice"
    Identifier    "Generic Keyboard"
    Driver        "kbd"
    Option        "CoreKeyboard"
    Option        "XkbRules"    "xorg"
    Option        "XkbModel"    "pc105"
    Option        "XkbLayout"    "us"
EndSection

Section "InputDevice"
    Identifier    "Configured Mouse"
    Driver        "mouse"
    Option        "CorePointer"
    Option        "Device"        "/dev/input/mice"
    Option        "Protocol"        "ImPS/2"
    Option        "Emulate3Buttons"    "true"
    Option        "ZAxisMapping"        "4 5"
EndSection

Section "InputDevice"
    Identifier      "Synaptics Touchpad"
    Driver          "synaptics"
    Option          "SendCoreEvents"        "true"
    Option          "Device"                "/dev/psaux"
    Option          "Protocol"              "auto-dev"
    Option        "RightEdge"        "5000"
EndSection
```

این بخش‌های InputDevice برای هر دستگاه ورودی مانند تاچ‌پد، ماوس و کیبورد که ممکن است به سیستم خود متصل کرده باشید، پیکربندی شده‌اند.

* Monitor - شرح دستگاه نمایشگر

```
Section "Monitor"
    Identifier    "Generic Monitor"
    Option        "DPMS"
EndSection
```

* Device - شرح/اطلاعات کارت گرافیک

```
Section "Device"
    Identifier    "ATI Technologies, Inc. Radeon Mobility 7500 (M7 LW)"
    Driver        "radeon"
    BusID        "PCI:1:0:0"
    Option        "DynamicClocks"    "on"

    Option        "CRT2HSync"    "30-80"
    Option        "CRT2VRefresh"    "59-75"

      Option        "MetaModes"    "1024x768 800x600 640x480 1024x768+1280x1024"

    Option  "XAANoOffscreenPixmaps"    "true"
EndSection

```

* Screen - یک آداپتور ویدئو را به یک مانیتور متصل می‌کند

```
Section "Screen"
    Identifier    "Screen0"
    Device        "Screen0 ATI Technologies, Inc. Radeon Mobility 7500 (M7 LW)"
    Monitor        "Generic Monitor"
    DefaultDepth    24
    SubSection "Display"
        Depth        1
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        4
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        8
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        15
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        16
        Modes        "1024x768"
    EndSubSection
    SubSection "Display"
        Depth        24
        Modes        "1024x768"
    EndSubSection
EndSection
```

* ServerLayout - یک یا چند صفحه نمایش را با یک یا چند دستگاه ورودی مرتبط می‌کند

```
Section "ServerLayout"
    Identifier    "DefaultLayout"
    Screen        "Default Screen"
    InputDevice    "Generic Keyboard"
    InputDevice    "Configured Mouse"
    InputDevice    "Synaptics Touchpad"
EndSection
```

### xwininfo

ممکن است موقعیت‌هایی پیش بیاید که در آن‌ها نیاز داشته باشیم اطلاعات دقیقی درباره پنجره یک اپلیکیشن در سیستم لینوکس خود به دست آوریم. برای مثال، ممکن است نیاز داشته باشیم اندازه و موقعیت پنجره را بدانیم. 

دستور `xwininfo` ابزاری است که در این مورد به ما کمک می‌کند. این در واقع یک ابزار اطلاعات پنجره برای X (یا سیستم X-Windows) است. بسته به اینکه کدام گزینه‌ها انتخاب شده باشند، اطلاعات مختلفی درباره آن پنجره می‌دهد؛ اطلاعاتی مانند اندازه، موقعیت، رنگ، عمق و غیره.

```
root@ubuntu16-1:~# xwininfo

xwininfo: Please select the window about which you
          would like information by clicking the
          mouse in that window.

xwininfo: Window id: 0x320000a "root@ubuntu16-1: ~"

  Absolute upper-left X:  65
  Absolute upper-left Y:  52
  Relative upper-left X:  0
  Relative upper-left Y:  0
  Width: 732
  Height: 410
  Depth: 32
  Visual: 0x2a0
  Visual Class: TrueColor
  Border width: 0
  Class: InputOutput
  Colormap: 0x3200009 (not installed)
  Bit Gravity State: NorthWestGravity
  Window Gravity State: NorthWestGravity
  Backing Store State: NotUseful
  Save Under State: no
  Map State: IsViewable
  Override Redirect State: no
  Corners:  +65+52  -3+52  -3-138  +65-138
  -geometry 80x24--7+14
```

### xdpyinfo

دستور `xdpyinfo` ابزاری برای نمایش اطلاعات درباره یک X server است.

```
root@ubuntu16-1:~# xdpyinfo 
name of display:    :0
version number:    11.0
vendor string:    The X.Org Foundation
vendor release number:    11906000
X.Org version: 1.19.6
maximum request size:  16777212 bytes
motion buffer size:  256
bitmap unit, bit order, padding:    32, LSBFirst, 32
image byte order:    LSBFirst
number of supported pixmap formats:    7
supported pixmap formats:
    depth 1, bits_per_pixel 1, scanline_pad 32
    depth 4, bits_per_pixel 8, scanline_pad 32
    depth 8, bits_per_pixel 8, scanline_pad 32
    depth 15, bits_per_pixel 16, scanline_pad 32
    depth 16, bits_per_pixel 16, scanline_pad 32
    depth 24, bits_per_pixel 32, scanline_pad 32
    depth 32, bits_per_pixel 32, scanline_pad 32
keycode range:    minimum 8, maximum 255
focus:  window 0x320000b, revert to Parent
number of extensions:    29
    BIG-REQUESTS
    Composite
    DAMAGE
...
```

### xhost

همانطور که گفتیم، **X** به گونه‌ای طراحی شده است که نسبت به شبکه شفاف باشد، به طوری که یک X server بتواند پنجره‌ها را از منابع محلی یا اپلیکیشن‌های تحت شبکه نمایش دهد.

دستور اصلی برای اجرای این فعالیت‌های شبکه `xhost` است - برنامه کنترل دسترسی سرور برای X. معمولاً دسترسی از راه دور غیرفعال است، زیرا یک خطر امنیتی محسوب می‌شود. اما اگر نیاز دارید یک اپلیکیشن گرافیکی (GUI) را روی یک کامپیوتر از راه دور اجرا کنید و گرافیک آن روی صفحه نمایش خودتان ظاهر شود، می‌توان از `xhost` برای اجازه دادن به کامپیوتر از راه دور استفاده کرد. بیایید شروع کنیم:

* دستور `xhost` بدون هیچ گزینه‌ای، وضعیت دسترسی را به ما می‌گوید:

```
root@ubuntu16-1:~# xhost
access control enabled, only authorized clients can connect
SI:localuser:user1
```

* `xhost +`: کنترل دسترسی را خاموش می‌کند (تمام هاست‌های راه دور به X server دسترسی خواهند داشت)
*  `xhost -`: کنترل دسترسی را دوباره روشن می‌کند.

```
root@ubuntu16-1:~# xhost +
access control disabled, clients can connect from any host
root@ubuntu16-1:~# xhost -
access control enabled, only authorized clients can connect
```

* `xhost + hostname`: نام هاست را به لیست کنترل دسترسی X server اضافه می‌کند. 
* `xhost - hostname`: نام هاست را از لیست کنترل دسترسی X server حذف می‌کند.

```
root@ubuntu16-1:~# xhost +172.16.43.136
172.16.43.136 being added to access control list

root@ubuntu16-1:~# xhost 
access control enabled, only authorized clients can connect
INET:172.16.43.136    (no nameserver response within 5 seconds)
SI:localuser:user1

root@ubuntu16-1:~# xhost -172.16.43.136
172.16.43.136 being removed from access control list
```

برنامه `xhost` برای افزودن و حذف نام‌های کاربری به لیست مجاز برای اتصال به X server استفاده می‌شود:

* `xhost +si:localuser:some_user`: به "some_user" اجازه دسترسی به X کاربر فعلی (localuser) را می‌دهد.
*  `xhost -si:localuser:some_user`: دسترسی "some_user" را لغو می‌کند.

```
root@ubuntu16-1:~# xhost +si:localuser:payam
localuser:payam being added to access control list

root@ubuntu16-1:~# xhost
access control disabled, clients can connect from any host
SI:localuser:payam
SI:localuser:user1

root@ubuntu16-1:~# xhost -si:localuser:payam
localuser:payam being removed from access control list
```

### DISPLAY

کلمه جادویی در سیستم X window، واژه `DISPLAY` است. یک نمایشگر (به زبان ساده) شامل موارد زیر است:

* یک کیبورد
* یک ماوس
* و یک صفحه نمایش

یک `DISPLAY` توسط برنامه X server مدیریت می‌شود. سرور قابلیت‌های نمایش را به سایر برنامه‌هایی که به آن متصل می‌شوند ارائه می‌دهد. سرور راه دور از طریق تعریف متغیر محیطی `DISPLAY` می‌داند که باید ترافیک شبکه X را به کجا هدایت کند که به طور کلی به یک X Display server واقع در کامپیوتر محلی شما اشاره دارد.

```
root@ubuntu16-1:~# echo $DISPLAY
:0
```

مقدار متغیر محیطی display به صورت زیر است: **`hostname:D.S`**

که در آن: 

* **hostname**: نام کامپیوتری است که X server روی آن اجرا می‌شود. حذف hostname به معنای localhost است. 
* **D**: یک شماره ترتیبی است (معمولاً ۰). اگر چندین نمایشگر به یک کامپیوتر متصل باشند، می‌تواند متفاوت باشد. 
* **S**: شماره صفحه نمایش است. یک نمایشگر در واقع می‌تواند چندین صفحه داشته باشد. معمولاً فقط یک صفحه وجود دارد که ۰ مقدار پیش‌فرض آن است.

> `:0.0` یعنی ما در مورد اولین صفحه نمایش متصل به اولین نمایشگر شما در localhost صحبت می‌کنیم.

ما می‌توانیم محیط `DISPLAY` را تغییر دهیم و خروجی گرافیکی خود را به ماشین دیگری متصل کنیم.

```
root@ubuntu16-1:~# export DISPLAY=172.16.43.136:0
root@ubuntu16-1:~# xeyes 
```

 در این حالت اگر یک برنامه گرافیکی اجرا شود، خروجی آن (پنجره‌ها) در ماشین دیگری نشان داده خواهد شد.

> هنگام استفاده از دستور `ssh` در لینوکس، می‌توان از گزینه `-X` برای تعیین X11 forwarding استفاده کرد.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-106-1/](https://developer.ibm.com/tutorials/l-lpic1-106-1/)

[https://kb.iu.edu/d/adnu](https://kb.iu.edu/d/adnu)

[https://commons.wikimedia.org/wiki/File:X11\_display_server_protocol.svg](https://commons.wikimedia.org/wiki/File:X11\_display_server_protocol.svg)

[https://en.wikipedia.org/wiki/X.Org_Server](https://en.wikipedia.org/wiki/X.Org_Server)

[https://mg.pov.lt/xorg.conf](https://mg.pov.lt/xorg.conf)

[https://www.faqforge.com/linux/fetch-detailed-information-application-window-linux/](https://www.faqforge.com/linux/fetch-detailed-information-application-window-linux/) [https://www.x.org/releases/X11R7.7/doc/man/man1/xwininfo.1.xhtml](https://www.x.org/releases/X11R7.7/doc/man/man1/xwininfo.1.xhtml) [https://linux.die.net/man/1/xdpyinfo](https://linux.die.net/man/1/xdpyinfo) [https://www.x.org/releases/X11R7.7/doc/man/man1/xdpyinfo.1.xhtml](https://www.x.org/releases/X11R7.7/doc/man/man1/xdpyinfo.1.xhtml) [https://www.lifewire.com/linux-command-xhost-4093456](https://www.lifewire.com/linux-command-xhost-4093456) [https://beamtic.com/xhost-linux](https://beamtic.com/xhost-linux) [https://linux.die.net/man/1/xhost](https://linux.die.net/man/1/xhost) [https://askubuntu.com/questions/432255/what-is-the-display-environment-variable](https://askubuntu.com/questions/432255/what-is-the-display-environment-variable)

.
