# 103.1 کار در خط فرمان (command line)

### **103.1 کار در خط فرمان (command line)**

**وزن:** ۴

**توضیحات:** داوطلبان باید قادر به تعامل با شل‌ها (shells) و دستورات با استفاده از خط فرمان باشند. این هدف شل Bash را فرض می‌کند.

**حوزه‌های کلیدی دانش:**

* استفاده از دستورات تک شل و توالی‌های دستوری تک خطی برای انجام وظایف اساسی در خط فرمان
* استفاده و اصلاح محیط شل از جمله تعریف، ارجاع و صادر کردن (export) متغیرهای محیطی (environment variables)
* استفاده و ویرایش تاریخچه دستورات (command history)
* فراخوانی دستورات داخل و خارج از مسیر (path) تعریف شده

**اصطلاحات و ابزارهای کاربردی:**

* bash
* echo
* env
* export
* pwd
* set
* unset
* man
* uname
* history
* .bash_history

**"ترمینال" (Terminal) چیست؟**

برنامه‌ای است که شبیه‌ساز ترمینال (terminal emulator) نامیده می‌شود. این برنامه‌ای است که یک پنجره را باز می‌کند و به شما اجازه می‌دهد با **شل (shell)** تعامل داشته باشید. مجموعه‌ای از شبیه‌سازهای ترمینال مختلف وجود دارد که می‌توانیم استفاده کنیم. اکثر توزیع‌های لینوکس چندین مورد را ارائه می‌دهند، مانند: gnome-terminal، konsole، xterm و غیره. به آن کنسول (Console) نیز گفته می‌شود.

**"شل" (The Shell) چیست؟**

به زبان ساده، شل برنامه‌ای است که دستورات را از کیبورد می‌گیرد و برای اجرا به سیستم‌عامل می‌دهد. در گذشته، این تنها رابط کاربری موجود در سیستم‌های شبه یونیکس مانند لینوکس بود. امروزه ما علاوه بر رابط‌های خط فرمان (CLI) مانند شل، رابط‌های کاربری گرافیکی (GUI) نیز داریم.

![](.gitbook/assets/commandintro-shell.jpg)

**"bash" چیست؟**

در اکثر سیستم‌های لینوکس برنامه‌ای به نام **bash به عنوان برنامه شل** عمل می‌کند. bash مخفف **Bourne Again SHell** است، نسخه‌ای بهبود یافته از برنامه اصلی شل یونیکس، sh، که توسط Steve Bourne نوشته شده است.

علاوه بر این، برنامه‌های شل دیگری نیز وجود دارند که می‌توانند در یک سیستم لینوکس نصب شوند. این‌ها شامل: ksh، tcsh و zsh هستند.

```
[root@centos7-1 ~]# ls -ls /bin/sh
0 lrwxrwxrwx. 1 root root 4 Oct 28  2017 /bin/sh -> bash
```

توجه: از نظر تاریخی، شل اصلی Bourne در /bin/sh از **$** به عنوان اعلان (prompt) معمولی و از **#** برای اعلان کاربر root استفاده می‌کرد. این کار تشخیص اینکه آیا به عنوان superuser در حال اجرا هستید یا خیر را بسیار آسان می‌کرد.

```
[root@centos7-1 ~]# 
[root@centos7-1 ~]# su - user1
Last login: Mon Dec 10 11:44:47 EST 2018 on pts/0
[user1@centos7-1 ~]$ 
[user1@centos7-1 ~]$ su - root
Password: 
Last login: Wed Dec 12 14:34:32 EST 2018 on pts/0
[root@centos7-1 ~]# 
```

**ورودی استاندارد، خروجی استاندارد و خطای استاندارد (Standard Input, Output, Error)**

به طور کلی، یک دستور (یک برنامه):

* داده‌ها را برای پردازش از ورودی استاندارد یا **stdin** می‌گیرد (پیش‌فرض: کیبورد).
* داده‌های پردازش شده را به خروجی استاندارد یا **stdout** برمی‌گرداند (پیش‌فرض: صفحه نمایش).
* اگر اجرای برنامه باعث بروز خطا شود، پیام‌های خطا به خطای استاندارد یا **stderr** فرستاده می‌شوند (پیش‌فرض: صفحه نمایش).

![](.gitbook/assets/commandintro-InputOutput.jpg)

### echo

echo یکی از رایج‌ترین و پرکاربردترین دستورات داخلی (built-in) برای شل‌های bash و C در لینوکس است که معمولاً در زبان‌های اسکریپت‌نویسی و فایل‌های دسته‌ای (batch files) برای نمایش یک خط متن/رشته در خروجی استاندارد یا یک فایل استفاده می‌شود.

The syntax for echo is: `echo [option(s)] [string(s)]`

مثال : ورود یک خط متن و نمایش در خروجی استاندارد:

```
[root@centos7-1 ~]# echo  Lets start learning Linux
Lets start learning Linux
```

دستور echo چند گزینه دارد. به طور معمول، echo یک کاراکتر خط جدید را به انتهای خروجی اضافه می‌کند. از گزینه `-n` برای جلوگیری از این کار استفاده کنید:

```
[root@centos7-1 ~]# echo -n Lets start learning Linux
Lets start learning Linux[root@centos7-1 ~]# 
```

#### کاراکترهای خاص یا متا کاراکترها (Meta Characters)

چه چیزی یک کاراکتر را خاص می‌کند؟ اگر معنایی فراتر از معنای تحت‌اللفظی خود داشته باشد، یک متا-معنا (meta-meaning)، آنگاه ما به آن به عنوان یک کاراکتر خاص یا metacharacters اشاره می‌کنیم مانند: **``| & ; ( ) < > [ ] { } * ! ? ` ' " $ \ / # ``**

**علامت پوند (#)**

هر چیزی که بعد از علامت پوند (#) نوشته شود توسط شل نادیده گرفته می‌شود. این برای نوشتن کامنت (comment) شل مفید است، اما تاثیری بر اجرای دستور یا بسط (expansion) شل ندارد.

```
[root@centos7-1 temp]# ls # ls /etc
zip-3.0-11.el7.x86_64.rpm  zip.cpio
```

**بک‌اسلش انتهای خط \\**

خطوطی که به بک‌اسلش (backslash) ختم می‌شوند در خط بعدی ادامه می‌یابند. شل کاراکتر خط جدید را تفسیر نمی‌کند و تا زمانی که یک خط جدید بدون بک‌اسلش مشاهده نشود، منتظر بسط شل و اجرای دستور می‌ماند.

```
[root@centos7-1 ~]# echo first line second line \
> 2nd line \
> 3rd line
first line second line 2nd line 3rd line
```

**گریز دادن (escaping) کاراکترهای خاص**

کاراکتر بک‌اسلش استفاده از کاراکترهای کنترلی را بدون اینکه شل آن‌ها را تفسیر کند، امکان‌پذیر می‌سازد؛ به این کار **گریز دادن کاراکترها (escaping characters)** می‌گویند.

```
[root@centos7-1 ~]# echo hello | by
bash: by: command not found...
[root@centos7-1 ~]# echo hello \| by
hello | by
[root@centos7-1 ~]# echo hello ; by
hello
bash: by: command not found...
[root@centos7-1 ~]# echo hello \; by
hello ; by
```

{% hint style="info" %}
 **نقل‌قول دوتایی (Double quotes) " " **

قرار دادن کاراکترها در نقل‌قول دوتایی (`"`) مقدار تحت‌اللفظی تمام کاراکترهای داخل نقل‌قول را حفظ می‌کند، \[_به استثنای `$`, `` ` ``, `\`, و زمانی که بسط تاریخچه فعال باشد، `! `_ . _کاراکترهای `$` و `` ` `` معنای خاص خود را در داخل نقل‌قول دوتایی حفظ می‌کنند_`].`

```
[root@centos7-1 ~]# echo "hello | by"
hello | by 
[root@centos7-1 ~]# echo "hello ; by" 
hello ; by
```
{% endhint %}

بیایید به گزینه‌های دیگر دستور echo نگاهی بیندازیم:

```
       -n     do not output the trailing newline
       -e     enable interpretation of backslash escapes
       -E     disable interpretation of backslash escapes (default)
       --help display this help and exit
       --version
              output version information and exit

       If -e is in effect, the following sequences are recognized:
               \b     backspace
               \\     backslash
               \n     new line
               \r     carriage return
               \t     horizontal tab
               \v     vertical tab
```

از گزینه `-e` برای فعال کردن معنای خاص برخی کاراکترهای **گریز داده شده (escaped)** با بک‌اسلش استفاده کنید.

| توالی گریز (Escape sequence) | عملکرد |
| --------------- | ------------------------------------------------------ |
| \a              | هشدار (زنگ) |
| \b              | Backspace |
| \c              | حذف خط جدید انتهایی (عملکردی مشابه گزینه -n) |
| \f              | Form feed (پاک کردن صفحه در نمایشگر ویدئویی) |
| \n              | خط جدید (New line) |
| \r              | Carriage return |
| \t              | تب افقی (Horizontal tab) |
| \v              | تب عمودی (vertical tab) |

Some examples:

```
[root@centos7-1 ~]# echo -e "Lets \vstart \vlearning \vLinux"
Lets 
     start 
           learning 
                    Linux
[root@centos7-1 ~]# echo -e "Lets \tstart \tlearning \tLinux"
Lets     start     learning     Linux
```

یکی از کاربردهای دستور echo به دست آوردن مقادیر متغیر با دستور `echo $VARIABLENAME` است. آن را خواهیم دید.

```
[root@centos7-1 ~]# echo $LD_LIBRARY_PATH

```

### **عملگرهای کنترلی (Control Operators)**

برخی متاکاراکترها یا جفت متاکاراکترها نیز به عنوان عملگرهای کنترلی عمل می‌کنند: **`|| && & ; ;; | ()`**

Some of these control operators allow you to create sequences or lists of commands.

#### زنجیره‌سازی دستورات لینوکس (Linux command chaining)

گاهی اوقات می‌خواهیم چندین دستور را به صورت پیاپی یا همزمان اجرا کنیم. این کار به ما اجازه می‌دهد فرآیندی را خودکار کنیم که شامل چندین دستور است که ممکن است به نتیجه دستور قبلی وابسته باشند یا نباشند.

**زنجیره‌سازی دستورات لینوکس** روشی برای ترکیب چندین دستور مختلف است به طوری که هر یک از آن‌ها بر اساس عملگری که آن‌ها را جدا می‌کند، بتوانند به صورت پیاپی اجرا شوند. بخش مهم زنجیره‌سازی، عملگرهایی (operators) هستند که برای ترکیب آن‌ها استفاده می‌کنیم. این عملگرها نحوه اجرای دستورات را تعیین می‌کنند.

بیایید به برخی از عملگرهایی که بیشتر برای ترکیب دستورات مختلف استفاده می‌شوند نگاهی بیندازیم:

**سمی-کولون** (**;**) : دستورات بعدی بدون توجه به وضعیت خروج (exit status) دستوری که قبل از آن است اجرا خواهند شد.

```
[root@centos7-1 ~]# ls /root/ ; ls /home/
anaconda-ks.cfg              mydb.ldif                      usr
htop-2.2.0-1.el7.x86_64.rpm  set.txt                        zip-3.0-11.el7.x86_64.rpm
initial-setup-ks.cfg         telnet-0.17-64.el7.x86_64.rpm  zip.cpio
managersou.ldif              temp
ldapadm  pabros  payam  user1  user2
```

**AND منطقی** (**&&**) : دستوری که پس از این عملگر می‌آید، تنها در صورتی اجرا می‌شود که دستور قبلی با موفقیت اجرا شده باشد.

```
[root@centos7-1 ~]# true && ls /home/
ldapadm  pabros  payam  user1  user2
```

**OR منطقی** (**||**) : دستوری که در ادامه می‌آید تنها در صورتی اجرا می‌شود که دستور قبلی شکست بخورد.

```
[root@centos7-1 ~]# false || ls /home/
ldapadm  pabros  payam  user1  user2
```

## متغیرهای محیطی (Environment Variables)

متغیرهای محیطی برخی متغیرهای خاص هستند که در شل تعریف شده‌اند و برنامه‌ها هنگام اجرا به آن‌ها نیاز دارند. آن‌ها می‌توانند توسط سیستم یا توسط کاربر تعریف شوند.

System defined variables are those which are set by system and are used by system level programs. 

The whole concept of setting and un-setting environment variables revolves around some set of files and few commands and different shells.

an environment variable can be in three types:

**1. Local Environment Variable** : موردی که برای جلسه (session) فعلی تعریف شده است. این متغیرهای محیطی فقط تا پایان جلسه فعلی باقی می‌مانند، خواه جلسه ورود از راه دور باشد یا جلسه ترمینال محلی. این متغیرها در هیچ فایل پیکربندی مشخص نشده‌اند و با استفاده از مجموعه خاصی از دستورات ایجاد و حذف می‌شوند.

**2. User Environment Variable** : این‌ها متغیرهایی هستند که برای یک کاربر خاص تعریف شده‌اند و هر بار که کاربر با استفاده از یک جلسه ترمینال محلی وارد سیستم می‌شود یا از راه دور متصل می‌شود، بارگذاری می‌شوند. این متغیرها معمولاً در برخی فایل‌های پیکربندی که در دایرکتوری خانگی کاربر موجود است، تنظیم و از آنجا بارگذاری می‌شوند.

**3. System wide Environment Variables** : این‌ها متغیرهای محیطی هستند که در سراسر سیستم، یعنی برای تمام کاربران موجود در آن سیستم، در دسترس هستند. این متغیرها در فایل‌ها و دایرکتوری‌های پیکربندی سراسری سیستم در /etc موجود هستند. این متغیرها هر بار که سیستم روشن می‌شود و هر کاربری به صورت محلی یا از راه دور وارد می‌شود، بارگذاری می‌شوند.

### env

به طور پیش‌فرض، دستور "env" تمام متغیرهای محیطی فعلی را لیست می‌کند.

```
[root@centos7-1 ~]# env
XDG_VTNR=1
XDG_SESSION_ID=1
HOSTNAME=centos7-1
SHELL=/bin/bash
TERM=xterm-256color
HISTSIZE=1000
USER=root
LS_COLORS=rs=0:di=38;5;27:ln=38;5;51:mh=44;38;5;15:pi=40;38;5;11:so=38;5;13:do=38;5;5:bd=48;5;232;38;5;11:cd=48;5;232;38;5;3:or=48;5;232;38;5;9:mi=05;48;5;232;38;5;15:su=48;5;196;38;5;15:sg=48;5;11;38;5;16:ca=48;5;196;38;5;226:tw=48;5;10;38;5;16:ow=48;5;10;38;5;21:st=48;5;21;38;5;15:ex=38;5;34:*.tar=38;5;9:*.tgz=38;5;9:*.arc=38;5;9:*.arj=38;5;9:*.taz=38;5;9:*.lha=38;5;9:*.lz4=38;5;9:*.lzh=38;5;9:*.lzma=38;5;9:*.tlz=38;5;9:*.txz=38;5;9:*.tzo=38;5;9:*.t7z=38;5;9:*.zip=38;5;9:*.z=38;5;9:*.Z=38;5;9:*.dz=38;5;9:*.gz=38;5;9:*.lrz=38;5;9:*.lz=38;5;9:*.lzo=38;5;9:*.xz=38;5;9:*.bz2=38;5;9:*.bz=38;5;9:*.tbz=38;5;9:*.tbz2=38;5;9:*.tz=38;5;9:*.deb=38;5;9:*.rpm=38;5;9:*.jar=38;5;9:*.war=38;5;9:*.ear=38;5;9:*.sar=38;5;9:*.rar=38;5;9:*.alz=38;5;9:*.ace=38;5;9:*.zoo=38;5;9:*.cpio=38;5;9:*.7z=38;5;9:*.rz=38;5;9:*.cab=38;5;9:*.jpg=38;5;13:*.jpeg=38;5;13:*.gif=38;5;13:*.bmp=38;5;13:*.pbm=38;5;13:*.pgm=38;5;13:*.ppm=38;5;13:*.tga=38;5;13:*.xbm=38;5;13:*.xpm=38;5;13:*.tif=38;5;13:*.tiff=38;5;13:*.png=38;5;13:*.svg=38;5;13:*.svgz=38;5;13:*.mng=38;5;13:*.pcx=38;5;13:*.mov=38;5;13:*.mpg=38;5;13:*.mpeg=38;5;13:*.m2v=38;5;13:*.mkv=38;5;13:*.webm=38;5;13:*.ogm=38;5;13:*.mp4=38;5;13:*.m4v=38;5;13:*.mp4v=38;5;13:*.vob=38;5;13:*.qt=38;5;13:*.nuv=38;5;13:*.wmv=38;5;13:*.asf=38;5;13:*.rm=38;5;13:*.rmvb=38;5;13:*.flc=38;5;13:*.avi=38;5;13:*.fli=38;5;13:*.flv=38;5;13:*.gl=38;5;13:*.dl=38;5;13:*.xcf=38;5;13:*.xwd=38;5;13:*.yuv=38;5;13:*.cgm=38;5;13:*.emf=38;5;13:*.axv=38;5;13:*.anx=38;5;13:*.ogv=38;5;13:*.ogx=38;5;13:*.aac=38;5;45:*.au=38;5;45:*.flac=38;5;45:*.mid=38;5;45:*.midi=38;5;45:*.mka=38;5;45:*.mp3=38;5;45:*.mpc=38;5;45:*.ogg=38;5;45:*.ra=38;5;45:*.wav=38;5;45:*.axa=38;5;45:*.oga=38;5;45:*.spx=38;5;45:*.xspf=38;5;45:
MAIL=/var/spool/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
PWD=/root
LANG=en_US.UTF-8
HISTCONTROL=ignoredups
SHLVL=1
XDG_SEAT=seat0
HOME=/root
LOGNAME=root
XDG_DATA_DIRS=/root/.local/share/flatpak/exports/share/:/var/lib/flatpak/exports/share/:/usr/local/share/:/usr/share/
LESSOPEN=||/usr/bin/lesspipe.sh %s
_=/bin/env
OLDPWD=/etc
```

این متغیرها کار با شل را آسان‌تر می‌کنند. برخی از مهم‌ترین آن‌ها عبارتند از:

* **HOSTNAME**: نام کامپیوتر شما.
* **SHELL**: این متغیر شلی را توصیف می‌کند که هر دستوری را که تایپ می‌کنید تفسیر می‌کند. در بیشتر موارد، این به طور پیش‌فرض bash خواهد بود، اما اگر گزینه‌های دیگر را ترجیح می‌دهید، می‌توان مقادیر دیگری را تنظیم کرد.
* **TERM**: این نوع ترمینالی را که باید هنگام اجرای شل شبیه‌سازی شود مشخص می‌کند. ترمینال‌های سخت‌افزاری مختلف را می‌توان برای نیازهای عملیاتی مختلف شبیه‌سازی کرد. معمولاً نیازی نیست نگران این موضوع باشیم.
* **USER**: کاربر فعلی که وارد سیستم شده است.
* **LS_COLORS**: این متغیر کدهای رنگی را تعریف می‌کند که برای اضافه کردن خروجی رنگی اختیاری به دستور ls استفاده می‌شود. این برای تشخیص انواع مختلف فایل و ارائه اطلاعات بیشتر به کاربر در یک نگاه استفاده می‌شود.
* **MAIL**: مسیر صندوق پستی کاربر فعلی.
* **PATH**: لیستی از دایرکتوری‌ها که سیستم هنگام جستجوی دستورات آن‌ها را بررسی می‌کند. وقتی کاربر دستوری را تایپ می‌کند، سیستم دایرکتوری‌ها را به این ترتیب برای فایل اجرایی بررسی می‌کند.

```
[root@centos7-1 ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
[root@centos7-1 ~]# which firefox
/bin/firefox
[root@centos7-1 ~]# type firefox
firefox is /bin/firefox
[root@centos7-1 ~]# whereis firefox
firefox: /usr/bin/firefox /usr/lib64/firefox /etc/firefox /usr/share/man/man1/firefox.1.gz
```

> برای اجرای سایر اسکریپت‌ها یا دستورات می‌توانیم آن‌ها را به path اضافه کنیم، یا از مسیر کامل یا مسیر نسبی (. و ..) استفاده کنیم.
>
> PATH=$PATH:/tmp/mybin

* **PWD**: دایرکتوری کاری فعلی (current working directory).

> ما از دستور pwd برای دیدن آن استفاده می‌کنیم:
>
> ```
> [root@centos7-1 ~]# pwd
> /root
> [root@centos7-1 ~]# cd /etc/
> [root@centos7-1 etc]# pwd
> /etc
> ```

* **LANG**: تنظیمات زبان و بومی‌سازی فعلی، از جمله کدگذاری کاراکترها.
* **HOME**: دایرکتوری خانگی کاربر فعلی.
* **\_**: آخرین دستور اجرا شده قبلی.
* **OLDPWD**: دایرکتوری کاری قبلی. این توسط شل نگه داشته می‌شود تا با اجرای دستور cd - به دایرکتوری قبلی خود بازگردید.

{% hint style="info" %}
کد خروج (exit code) دستور قبلی در متغیر شل $? ذخیره می‌شود. در واقع $? یک پارامتر شل است و نه یک متغیر، زیرا نمی‌توانید مقداری را به $? اختصاص دهید.

`[root@centos7-1 ~]# touch my.txt `

`[root@centos7-1 ~]# echo $?`

` 0`
{% endhint %}

برای دیدن مقدار یک متغیر از `echo $VARIABLENAME` استفاده کنید:

```
[root@centos7-1 ~]# echo $MAIL
/var/spool/mail/root
[root@centos7-1 ~]# echo $USER
root
[root@centos7-1 ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
```

برای تنظیم یک متغیر محلی (Local Variable)، یک متغیر محلی مانند VAR1 ایجاد کرده و آن را به هر مقداری تنظیم می‌کنیم (از نظر تاریخی توصیه می‌شود برای متغیرها از حروف بزرگ استفاده کنید اما شما آزاد هستید از هر حالتی که می‌خواهید استفاده کنید):

```
[root@centos7-1 ~]# VAR1="Lets Learn Linux"
[root@centos7-1 ~]# echo $VAR1
Lets Learn Linux
```

به طور پیش‌فرض متغیرها محلی هستند، بنابراین اگر یک زیر-شل (sub shell) جدید را شروع کنیم چه اتفاقی برای متغیر ما می‌افتد؟

```
[root@centos7-1 ~]# VAR1="Lets learn Linux"
[root@centos7-1 ~]# echo $VAR1
Lets learn Linux
[root@centos7-1 ~]# bash
[root@centos7-1 ~]# echo $VAR1
```

It shows nothing. Why?

**فرآیند فرزند در مقابل والد (Child vs Parent process)**

هر فرآیندی می‌تواند همزمان یک فرآیند والد و فرزند باشد. تنها استثنا فرآیند init است که همیشه با PID شماره 1 مشخص می‌شود. بنابراین، init والد تمام فرآیندهای در حال اجرا در سیستم لینوکس شماست.

![](.gitbook/assets/commandintro-parentchild.jpg)

Any process created will normally have a parent process from which it was created and will be considered as a child of this parent process.\
`echo $$` print a PID for a current shell

```
[root@centos7-1 ~]# echo $$
24370
[root@centos7-1 ~]# bash
[root@centos7-1 ~]# echo $$
24652
[root@centos7-1 ~]# exit
exit
[root@centos7-1 ~]# echo $$
24370
```

هنگام ایجاد یک فرآیند فرزند جدید (در مثال ما یک زیر-شل)، یک دستور export به سادگی اطمینان حاصل می‌کند که هر متغیر صادر شده در فرآیند والد در فرآیند فرزند در دسترس است.

ما می‌توانیم یک شل را با استفاده از دستور exit یا به سادگی با فشردن کلیدهای ctrl+d خاتمه دهیم.

{% hint style="info" %}
**( )** : پرانتزها واقعاً دستور را در یک زیر-شل قرار می‌دهند. دستور (exit) را امتحان کنید.
{% endhint %}

### export

دستور export یکی از دستورات داخلی شل bash است، یک متغیر محیطی را علامت‌گذاری می‌کند تا به فرآیندهای فرزند صادر شود، به طوری که فرزند آن‌ها را به ارث ببرد.

```
[root@centos7-1 ~]# VAR2="Linux is Fun"
[root@centos7-1 ~]# echo $VAR2
Linux is Fun
[root@centos7-1 ~]# export VAR2
[root@centos7-1 ~]# echo $$
24328
[root@centos7-1 ~]# bash
[root@centos7-1 ~]# echo $$
24722
[root@centos7-1 ~]# echo $VAR2
Linux is Fun
```

Syntax:`export [-fn] [name[=value] ...] or export -p`

استفاده از دستور export نسبتاً ساده است زیرا دارای سینتکس مستقیمی با تنها سه گزینه دستوری موجود است.

```
-p    لیست تمام نام‌هایی که در شل فعلی صادر شده‌اند
-n    حذف نام‌ها از لیست صادرات (export list)
-f    نام‌ها به عنوان تابع (function) صادر می‌شوند
```

مثال ما را دنبال کنید:

```
[root@centos7-1 ~]# export -p | grep -i var2
declare -x VAR2="Linux is Fun"
[root@centos7-1 ~]# export -n VAR2
[root@centos7-1 ~]# export -p | grep -i var2
[root@centos7-1 ~]#
```

### set

`set` یک دستور داخلی شل است که (اگر هیچ گزینه یا آرگومانی ارائه نشود) تمام متغیرهای شل، نه فقط متغیرهای محیطی، و همچنین توابع شل را نمایش می‌دهد که همان چیزی است که در انتهای لیست مشاهده می‌کنید.

```
[root@centos7-1 ~]# set
ABRT_DEBUG_LOG=/dev/null
BASH=/bin/bash
BASHOPTS=checkwinsize:cmdhist:expand_aliases:extglob:extquote:force_fignore:histappend:interactive_comments:login_shell:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_COMPLETION_COMPAT_DIR=/etc/bash_completion.d
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="4" [1]="2" [2]="46" [3]="2" [4]="release" [5]="x86_64-redhat-linux-gnu")
BASH_VERSION='4.2.46(2)-release'
COLUMNS=87
DIRSTACK=()
DISPLAY=:0
EUID=0
FINAL_LIST=
GLUSTER_BARRIER_OPTIONS=$'\n        {enable},\n        {disable}\n'
...
<output has been truncated>
```

> **set در مقابل env**
>
> خلاصه داستان: set می‌تواند متغیرهای محلی شل را ببیند، env نمی‌تواند.
>
> همانطور که اشاره کردیم، شل‌ها می‌توانند 2 نوع متغیر داشته باشند: محلی (local) که فقط از شل فعلی قابل دسترسی هستند، و متغیرهای محیطی (صادر شده) که به هر برنامه اجرا شده منتقل می‌شوند.
>
> از آنجایی که set یک دستور داخلی شل است، متغیرهای محلی شل (از جمله توابع شل) را نیز می‌بیند. از سوی دیگر، env یک فایل اجرایی مستقل است؛ این دستور فقط متغیرهایی را می‌بیند که شل به آن منتقل می‌کند، یعنی متغیرهای محیطی.

در bash مانند هر شل شبیه به Bourne، دستور set برای تنظیم گزینه‌ها (تنظیمات پیکربندی شل مانند -f, -C, -o noclobber...) و پارامترهای موقعیتی ($1, $2...).

```
set [--abefhkmnptuvxBCEHPT] [-o option-name] [argument …]
set [+abefhkmnptuvxBCEHPT] [+o option-name] [argument …]
```

از علامت‌های - و + برای فعال و غیرفعال کردن گزینه‌ها استفاده کنید. `help set | less` را امتحان کنید.

برای دیدن گزینه‌های فعلی شل از `echo $-` استفاده کنید:

```
[root@centos7-1 ~]# echo $-
himuBH
```

برای مثال -u با متغیرهای تنظیم نشده (unset) در هنگام جایگزینی به عنوان یک خطا رفتار می‌کند:

```
root@ubuntu16-1:~# echo $-
himBH
root@ubuntu16-1:~# echo $VAR1

root@ubuntu16-1:~# set -u
root@ubuntu16-1:~# echo $-
himuBH
root@ubuntu16-1:~# echo $VAR1
-su: VAR1: unbound variable
root@ubuntu16-1:~# set +u
root@ubuntu16-1:~# echo $-
himBH
```

ما می‌توانیم از `set -o` بدون هیچ گزینه‌ای برای دریافت وضعیت فعلی گزینه‌های شل استفاده کنیم:

```
 [root@centos7-1 ~]# set -o
allexport          off
braceexpand        on
emacs              on
errexit            off
errtrace           off
functrace          off
hashall            on
histexpand         on
history            on
ignoreeof          off
interactive-comments    on
keyword            off
monitor            on
noclobber          off
noexec             off
noglob             off
nolog              off
notify             off
nounset            off
onecmd             off
physical           off
pipefail           off
posix              off
privileged         off
verbose            off
vi                 off
xtrace             off
```

set +o گزینه مشخص شده را خاموش کرده و set -o آن را روشن می‌کند. برای مثال بیایید ذخیره‌سازی تاریخچه (History) را با گزینه -o غیرفعال و فعال کنیم:

```
[root@centos7-1 ~]# set +o history
[root@centos7-1 ~]# set -o | grep history
history            off
[root@centos7-1 ~]# set -o history
[root@centos7-1 ~]# set -o | grep history
history            on
```

یکی از گزینه‌های مهمی که ممکن است در آزمون از شما پرسیده شود noclobber است، گزینه noclobber از بازنویسی فایل‌های موجود با عملگر < جلوگیری می‌کند (در درس‌های بعدی بحث خواهد شد).

دستور set برای تنظیم متغیرها نیست. اشتباه نکنید!

```
[root@centos7-1 ~]# set VAR4="My Forth Variable"
[root@centos7-1 ~]# echo $VAR4

[root@centos7-1 ~]#
```

Set گزینه‌های زیادی دارد که برای تغییر رفتار bash استفاده می‌شوند.

### unset

دستور unset برای حذف (unset) موقت هر متغیر محیطی محلی استفاده می‌شود:

```
[root@centos7-1 ~]# VAR5="Linux Linux Linux"
[root@centos7-1 ~]# echo $VAR5
Linux Linux Linux
[root@centos7-1 ~]# unset VAR5
[root@centos7-1 ~]# echo $VAR5
bash: VAR5: unbound variable
[root@centos7-1 ~]# unset PATH
[root@centos7-1 ~]# ls
bash: ls: No such file or directory
```

{% hint style="success" %}
بنابراین `set` برای set کردن (مقداردهی متغیر) نیست، اما `unset` برای unset کردن است.
{% endhint %}

{% hint style="info" %}
ما همچنین می‌توانیم از `env VARNAME=VALUE` و `env -u VARNAME` برای تنظیم و حذف یک متغیر استفاده کنیم.
{% endhint %}

## درک تاریخچه Bash (Bash History)

تاریخچه تمام دستوراتی که توسط همه کاربران اجرا می‌شوند ذخیره می‌شود. تاریخچه هم در ram و هم در فایلی به نام _.bash_history_ نگهداری می‌شود. تاریخچه‌ای که در ram نگهداری می‌شود، با دستور `history` و متغیرهای محیطی مدیریت می‌شود. مهم است که توجه داشته باشید تاریخچه موجود در ram تنها پس از خروج کاربر از جلسه خود (log out) در فایل نوشته می‌شود.

### history

دستور history محتوای فعلی لیست تاریخچه Bash را در حافظه برای جلسه فعلی نشان می‌دهد.

```
[root@centos7-1 ~]# history 
    1  dhclient -r
    2  dhclient
    3  seastatus
    4  sestatus 
    5  vim /etc/sysconfig/selinux 
    6  setenforce 0
    7  sestatus 
    8  reboot
    9  cd /var/lib/ldap/
   10  ls
   11  ls -la
   12  ls -lrth
   13  ping 8.8.8.8
   14  dhclient -r
   15  shcli
   16  dhclient
   17  ping 8.8.8.8
   18  sestatus 
   19  yum install openldap openldap-servers openldap-clients.x86_64
   20  systemctl status slapd.service
<output has been trancuated>
```

چه تعداد خط در نسخه RAM لیست تاریخچه ذخیره می‌شود؟ این توسط متغیر HISTSIZE تعریف می‌شود.

* **HISTSIZE** : این متغیر حاوی حداکثر تعداد خطوطی است که می‌توانند در نسخه موجود در ram تاریخچه قرار بگیرند.

```
[root@centos7-1 ~]# env | grep -i histsize
HISTSIZE=1000
```

و این مقدار 1000 خط خواهد بود! برای دیدن مثلاً فقط 5 خط آخر از `history 5` استفاده کنید.

**تکرار سریع دستور قبلی (3 روش)**

1. از **کلید جهت‌نمای بالا** برای مشاهده دستور قبلی استفاده کرده و برای اجرای آن enter را فشار دهید.
2. **عبارت !! را تایپ کرده و enter را فشار دهید** (یا **!!number**).
3. **فشردن Control+P** دستور قبلی را نمایش می‌دهد، برای اجرا enter را بزنید.

**Search the history(3 methods)**

1. **Control+R: **Press Control+R and type the keyword.
2. **!string** Refers to the most recent command starting with string.
3. **!?string?** Refers to the most recent command containing string (the ending ? is optional).

**Clear all the previous history : **Use history -c option.

### **~/.bash_history**

هنگامی که کاربر شل را می‌بندد، Bash لیست تاریخچه خود را با اضافه کردن ورودی‌های موجود به فایل مخفی **~/.bash_history** او در دیسک ذخیره می‌کند. **~/.bash_history **توسط برخی متغیرها کنترل می‌شود:

* **HISTFILE    **این متغیر حاوی مکان فایل تاریخچه است. هنگامی که از bash خارج می‌شوید، محتویات دستور history در این فایل نوشته می‌شود.
*   **HISTFILESIZE    **The variable contains the maximum number of lines that may be in the history file. When bash writes to the history file, the oldest commands that go over this maximum number will be deleted.

     it acts the same as HISTSIZE.
* **HISTCONTROL    **This variable contains instructions for what should be ignored when added to the history file. has four settings:

```
نام تنظیم (Setting Name)    اثر تنظیم (Setting Effect)
Ignorespace    هنگامی که از این تنظیم استفاده می‌شود، هر خطی که با یک یا چند فاصله شروع شود به تاریخچه اضافه نخواهد شد.
Ignoredups    هنگامی که از این تنظیم استفاده می‌شود، خطوطی که با ورودی قبلی در تاریخچه مطابقت دارند ذخیره نمی‌شوند.
Ignoreboth    هنگامی که از این تنظیم استفاده می‌شود، هر دو شرایط ignoredups و ignorespace را شامل می‌شود.
تعریف نشده (Not Defined)    اگر HISTCONTROL تعریف نشده باشد، هیچ شرطی قبل از ورود خط به تاریخچه اعمال نخواهد شد.
```

برای دیدن فایل مخفی **.bash_history** باید از ls با سوئیچ `-a` استفاده کنیم:

```
[root@centos7-1 ~]# ls -la | grep  .bash_history
-rw-------.  1 root root   9779 Dec  9 14:29 .bash_history
```

What about other users?

```
[root@centos7-1 ~]# find /home/ -iname .bash_history
/home/payam/.bash_history
/home/ldapadm/.bash_history
/home/user1/.bash_history
/home/user2/.bash_history
```

به عنوان مثال بیایید بفهمیم user1 چه کاری انجام داده است:

```
[root@centos7-1 ~]# cat /home/user1/.bash_history
ls
exit
```

او کاری انجام نداده است:). **.bash_history** به طور پیش‌فرض 500 یا 1000 دستور مورد استفاده را نگه می‌دارد. بنابراین می‌توانیم به کاری که مدت‌ها پیش انجام داده‌ایم برگردیم. history -c آن را پاک نمی‌کند و پس از خروج کاربر باقی می‌ماند، بنابراین اگر آن را دوست ندارید باید خودتان آن را حذف کنید.

### uname

دستور uname بدون هیچ سوئیچی اطلاعات سیستم را چاپ می‌کند:

```
[root@centos7-1 ~]# uname
Linux
```

Its syntax is like `uname [OPTION] ...` .For example`-a, --all`Prints all information:

```
[root@centos7-1 ~]# uname -a
Linux centos7-1 3.10.0-693.el7.x86_64 #1 SMP Tue Aug 22 21:09:27 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```

If -a (--all) is specified, the information is printed in the following order of individual options:

```
-s, --kernel-name    نام هسته (kernel name) را چاپ می‌کند.
-n, --nodename    نام میزبان گره شبکه (network node hostname) را چاپ می‌کند.
-r, --kernel-release    نسخه انتشار هسته (kernel release) را چاپ می‌کند.
-v, --kernel-version    نسخه هسته (kernel version) را چاپ می‌کند.
-m, --machine    نام سخت‌افزار ماشین را چاپ می‌کند.
-p, --processor    نوع پردازنده یا "unknown" را چاپ می‌کند.
-i, --hardware-platform    پلتفرم سخت‌افزاری یا "unknown" را چاپ می‌کند.
-o, --operating-system    سیستم‌عامل را چاپ می‌کند.
--help    نمایش پیام راهنما و خروج.
--version    نمایش اطلاعات نسخه و خروج.
```

Processor information (-p) and Hardware-Platform(-i) are omitted if they are unknown.

### man

در سیستم‌عامل‌های شبه یونیکس، یک man page (مخفف manual page) مستنداتی برای یک برنامه/ابزار/برنامه کاربردی مبتنی بر ترمینال (که معمولاً به عنوان دستور یا command شناخته می‌شود) است. این شامل موارد زیر است:

* **نام دستور**
* **سینتکس استفاده از آن**
* **توضیحات (description)**
* **گزینه‌های موجود**
* **نویسنده (author)**
* **کپی‌رایت (copyright)**
* **دستورات مرتبط و غیره.**

To read a manual page for a Unix command, a user can type:

```
man <command_name>
```

For example try `man man` :

![](.gitbook/assets/commandintro-manman.jpg)

به طور پیش‌فرض، man معمولاً از یک برنامه پیجر (pager) ترمینال مانند **more** یا **less** برای نمایش خروجی خود استفاده می‌کند.

دفترچه راهنما به طور کلی به هشت یا نه بخش شماره‌گذاری شده تقسیم می‌شود که به صورت زیر سازماندهی شده‌اند:

1. **برنامه‌های اجرایی یا دستورات شل**
2. **فراخوانی‌های سیستم (System calls)** (توابع ارائه شده توسط هسته)
3. **فراخوانی‌های کتابخانه (Library calls)** (توابع در کتابخانه‌های برنامه)
4. **فایل‌های خاص** (معمولاً در /dev یافت می‌شوند)
5. **فرمت‌های فایل و قراردادها** مثلاً /etc/passwd
6. **بازی‌ها**
7. **متفرقه (Miscellaneous)** (شامل پکیج‌های ماکرو و قراردادها)، به عنوان مثال man(7)، groff(7)
8. **دستورات مدیریت سیستم** (معمولاً فقط برای root)
9. **روتین‌های هسته (Kernel routines)** \[غیر استاندارد]

برای اینکه man صفحه راهنما را از بخش‌های خاصی نمایش دهد، از `man [section-num] [command/tool name]` استفاده کنید:

```
man 1 echo
```

man صفحات راهنمای مورد نظر را در حافظه‌های پنهان دیتابیس ایندکس جستجو می‌کند. بنابراین نیازی به حفظ کردن شماره بخش‌ها برای ورودی‌های راهنما نیست.

#### mandb

mandb برای مقداردهی اولیه یا به‌روزرسانی دستی کش‌های دیتابیس ایندکس که معمولاً توسط man نگهداری می‌شوند، استفاده می‌شود. کش‌ها حاوی اطلاعات مربوط به وضعیت فعلی سیستم صفحات راهنما هستند و اطلاعات ذخیره شده در آن‌ها توسط ابزارهای man-db برای افزایش سرعت و عملکرد آن‌ها استفاده می‌شود.

بررسی یکپارچگی کش در سیستم‌هایی با صفحات راهنمای زیاد می‌تواند کند باشد، بنابراین mandb به طور پیش‌فرض انجام نمی‌شود و مدیران سیستم ممکن است بخواهند هر هفته یا چند وقت یکبار mandb را اجرا کنند تا کش‌های دیتابیس را تازه نگه دارند.

```
[root@centos7-1 ~]# mandb
Purging old database entries in /usr/share/man...
mandb: warning: /usr/share/man/man8/fsck.fat.8.manpage-fix.gz: ignoring bogus filename
Processing manual pages under /usr/share/man...
Purging old database entries in /usr/share/man/hu...
Processing manual pages under /usr/share/man/hu...
Purging old database entries in /usr/share/man/de...
Processing manual pages under /usr/share/man/de...
Purging old database entries in /usr/share/man/ja...

...

0 man subdirectories contained newer manual pages.
0 manual pages were added.
0 stray cats were added.
0 old database entries were purged.
```

**مفیدترین گزینه‌های دستور man:**

`-f, --whatis` نمایش یک توضیح کوتاه از صفحه راهنما

```
[root@centos7-1 ~]# man -f ls
ls (1)               - list directory contents
ls (1p)              - list directory contents
```

`-w, --where, --location` صفحات راهنما را واقعاً نمایش نمی‌دهد، بلکه مسیر فایل‌های منبع را چاپ می‌کند

```
[root@centos7-1 ~]# man -w ls
/usr/share/man/man1/ls.1.gz
```

`-k, --apropos` معادل apropos. توضیحات کوتاه صفحات راهنما را برای کلمات کلیدی جستجو کرده و هرگونه مطابقت را نمایش می‌دهد.

```
[root@centos7-1 ~]# man -k echo
echo (1)             - display a line of text
echo (1p)            - write arguments to standard output
fcping (8)           - Fibre Channel Ping (ELS ECHO) tool
l2ping (1)           - Send L2CAP echo request and receive answer
lessecho (1)         - expand metacharacters
pam_echo (8)         - PAM module for printing text messages
ping (8)             - send ICMP ECHO_REQUEST to network hosts
ping6 (8)            - send ICMP ECHO_REQUEST to network hosts
```

### apropos

دستور apropos برای جستجو و نمایش یک توضیح کوتاه از man page یک دستور/برنامه به صورت زیر استفاده می‌شود.

```
[root@centos7-1 ~]# apropos echo
echo (1)             - display a line of text
echo (1p)            - write arguments to standard output
fcping (8)           - Fibre Channel Ping (ELS ECHO) tool
l2ping (1)           - Send L2CAP echo request and receive answer
lessecho (1)         - expand metacharacters
pam_echo (8)         - PAM module for printing text messages
ping (8)             - send ICMP ECHO_REQUEST to network hosts
ping6 (8)            - send ICMP ECHO_REQUEST to network hosts
```

That is all!

.

.

.

sources:

[http://linuxcommand.org/lc3\_lts0010.php](http://linuxcommand.org/lc3\_lts0010.php)

[https://stackoverflow.com/questions/6697753/difference-between-single-and-double-quotes-in-bash](https://stackoverflow.com/questions/6697753/difference-between-single-and-double-quotes-in-bash)

[https://www.w3resource.com/linux-system-administration/control-operators.php](https://www.w3resource.com/linux-system-administration/control-operators.php)

[https://www.tecmint.com/set-unset-environment-variables-in-linux/](https://www.tecmint.com/set-unset-environment-variables-in-linux/)

[https://unix.stackexchange.com/questions/291729/why-is-the-default-symbol-for-a-user-shell-and-the-default-symbol-for-a-root](https://unix.stackexchange.com/questions/291729/why-is-the-default-symbol-for-a-user-shell-and-the-default-symbol-for-a-root)

[http://labor-liber.org/en/gnu-linux/introduction/index.php?diapo=input_output](http://labor-liber.org/en/gnu-linux/introduction/index.php?diapo=input_output)

[http://www.lostsaloon.com/technology/how-to-chain-commands-in-linux-command-line-with-examples/](http://www.lostsaloon.com/technology/how-to-chain-commands-in-linux-command-line-with-examples/)

[https://www.tecmint.com/chaining-operators-in-linux-with-practical-examples/](https://www.tecmint.com/chaining-operators-in-linux-with-practical-examples/)

[https://www.tecmint.com/echo-command-in-linux/](https://www.tecmint.com/echo-command-in-linux/)

[https://developer.ibm.com/tutorials/l-lpic1-103-1/](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://ss64.com/bash/syntax-quoting.html](https://ss64.com/bash/syntax-quoting.html)

[https://linuxconfig.org/learning-linux-commands-export](https://linuxconfig.org/learning-linux-commands-export)

[https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps](https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps)

[http://www.symkat.com/understanding-bash-history](http://www.symkat.com/understanding-bash-history)

[https://www.tecmint.com/history-command-examples/](https://www.tecmint.com/history-command-examples/)

[https://www.thegeekstuff.com/2008/08/15-examples-to-master-linux-command-line-history](https://www.thegeekstuff.com/2008/08/15-examples-to-master-linux-command-line-history)

[https://www.computerhope.com/unix/uuname.htm](https://www.computerhope.com/unix/uuname.htm)

[https://en.wikipedia.org/wiki/Man_page](https://en.wikipedia.org/wiki/Man_page)

[https://www.tecmint.com/view-colored-man-pages-in-linux/](https://www.tecmint.com/view-colored-man-pages-in-linux/)

[https://www.techonthenet.com/linux/commands/man.php](https://www.techonthenet.com/linux/commands/man.php)

[http://man7.org/linux/man-pages/man8/mandb.8.html](http://man7.org/linux/man-pages/man8/mandb.8.html)

[https://bash.cyberciti.biz/guide/Shopt](https://bash.cyberciti.biz/guide/Shopt)
