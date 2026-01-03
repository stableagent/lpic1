# 105.1 شخصی‌سازی و استفاده از محیط شل (shell environment)

**وزن:** ۴

**توضیحات:** داوطلبان باید قادر به شخصی‌سازی محیط‌های شل برای پاسخگویی به نیازهای کاربران باشند. داوطلبان باید بتوانند پروفایل‌های سراسری و کاربری را تغییر دهند.

**حوزه‌های کلیدی دانش:**

* تنظیم متغیرهای محیطی (مانند PATH) هنگام ورود به سیستم یا هنگام ایجاد یک شل جدید
* نوشتن توابع Bash برای توالی‌های پرکاربرد دستورات
* نگهداری دایرکتوری‌های اسکلت (skeleton) برای حساب‌های کاربری جدید
* تنظیم مسیر جستجوی دستور با دایرکتوری مناسب

**در ادامه لیست کوتاهی از فایل‌ها، اصطلاحات و ابزارهای کاربردی آورده شده است:**

* source
* /etc/bash.bashrc
* /etc/profile
* env
* export
* set
* unset
* \~/.bash_profile
* \~/.bash_login
* \~/.profile
* \~/.bashrc
* \~/.bash_logout
* function
* alias
* lists



ما قبلاً در بخش "103-1" در مورد متغیرهای محیطی (environment variables) صحبت کرده‌ایم. در این بخش می‌خواهیم در مورد پروفایل‌های کاربر و پروفایل سراسری سیستم، و نحوه تعامل آن‌ها هنگام ورود کاربر به سیستم صحبت کنیم.

### شل لاگین (Login shell) و شل غیر لاگین (Non login shell)

برنامه شل، برای مثال Bash، از مجموعه‌ای از اسکریپت‌های راه‌اندازی (startup scripts) برای ایجاد یک محیط استفاده می‌کند. هر اسکریپت کاربرد خاصی دارد و محیط لاگین را به شکلی متفاوت تحت تأثیر قرار می‌دهد. هر اسکریپت بعدی که اجرا می‌شود می‌تواند مقادیر اختصاص داده شده توسط اسکریپت‌های قبلی را بازنویسی (override) کند.

راه‌اندازی برای شل‌های Login و شل‌های Non login متفاوت پیکربندی شده است. 

1. **Login shells**: اگر یک شل یا ترمینال را باز کنید (یا به یکی سوئیچ کنید) و قبل از اینکه به شما پرامپت (prompt) بدهد، از شما بخواهد لاگین کنید (نام کاربری؟ رمز عبور؟)، این یک login shell است.
2. **Non login shells**: اگر از شما نخواهد که لاگین کنید (مانند _gnome-terminal_) و به شما اجازه دهد مستقیماً از آن استفاده کنید، این یک non-login shell (GUI) است.

![](.gitbook/assets/custshell-loginshelvsnon.jpg)

#### افزودن تنظیمات سراسری برای login shell

### /etc/profile

فایل `/etc/profile` شامل متغیرهای محیطی سراسری سیستم لینوکس و برنامه‌های راه‌اندازی است. این فایل توسط تمام کاربرانی که از شل‌های bash، ksh یا sh استفاده می‌کنند، به کار می‌رود. این فایل فقط برای login shell اجرا می‌شود.  

### /etc/profile.d

اگر نیاز دارید محیط لاگین را برای تمام کاربران سیستم خود شخصی‌سازی کنید، می‌توانید از `/etc/profile` استفاده کنید، اما از آنجایی که فایل‌های توزیع در `/etc` مانند `/etc/profile` ممکن است توسط به‌روزرسانی‌های سیستم تغییر کنند، بهتر است مستقیماً آن‌ها را ویرایش نکنید. در عوض، فایل‌های اضافی را در `/etc/profile.d` ایجاد کنید.

#### افزودن تنظیمات سراسری برای شل‌های non-login (تعاملی)

### /etc/bash.bashrc و  /etc/bashrc

می‌توانید از `/etc/bash.bashrc` (یا `/etc/bashrc`) [بسته به توزیع شما] برای افزودن تنظیمات سراسری و نام‌های مستعار (aliases) سراسری استفاده کنید.

#### تنظیمات مخصوص کاربر

### /home/user/.bash_profile  و  /home/user/.bashrc

 فایل‌های `~/.bash_profile` و `~/.bashrc` اسکریپت‌های شل هستند که شامل دستورات شل می‌باشند. این فایل‌ها در محتوای کاربر (user's context) هنگام باز شدن یک شل جدید یا ورود کاربر اجرا می‌شوند تا محیط آن‌ها به درستی تنظیم شود. همانطور که ذکر شد، `~/.bash_profile` برای شل‌های login و `~/.bashrc` برای شل‌های تعاملی non-login اجرا می‌شود.

 این بدان معناست که وقتی یک کاربر (از طریق نام کاربری و رمز عبور) به کنسول لاگین می‌کند (چه به صورت محلی یا از راه دور از طریق چیزی مانند SSH)، اسکریپت `~/.bash_profile` قبل از اینکه پرامپت دستور اولیه به کاربر برگردانده شود، اجرا می‌شود. پس از آن، هر بار که یک شل جدید باز می‌شود، اسکریپت `~/.bashrc` اجرا می‌گردد. 

> در بیشتر مواقع `PATH` و متغیرهای محیطی در `~/.bash_profile` قرار می‌گیرند و نام‌های مستعار (aliases) در `~/.bashrc` قرار می‌گیرند.

### نام‌های مستعار (Aliases)

در bash، می‌توانید برای دستورات، نام‌های مستعار (aliases) تعریف کنید. شما از aliases برای ارائه یک نام جایگزین برای یک دستور، ارائه پارامترهای پیش‌فرض برای دستور، یا گاهی اوقات برای ساختن یک دستور جدید یا پیچیده‌تر استفاده می‌کنید.

 شما نام‌های مستعار را با دستور `alias` تنظیم یا لیست می‌کنید و با دستور `unalias` آن‌ها را حذف می‌کنید.

```
### لیست کردن نام‌های مستعار (aliases)
root@ubuntu16-1:~# alias 
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'

### حذف alias با دستور unalias
root@ubuntu16-1:~# unalias ll
root@ubuntu16-1:~# ll
ll: command not found

### تنظیم یک alias
root@ubuntu16-1:~# alias ll='ls -alF'
root@ubuntu16-1:~# ll
total 472
...
```

برای دائمی کردن نام‌های مستعار برای کاربر، آن‌ها در `/home/user/.bashrc` تعریف می‌شوند:

```
...
# نام‌های مستعار ls بیشتر
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
...
```

>  یکی دیگر از کاربردهای رایج نام‌های مستعار برای کاربر root است. دستورات `cp` ،`rm` و `mv` معمولاً دارای alias هستند تا پارامتر `-i` را شامل شوند و به جلوگیری از تخریب تصادفی فایل‌ها کمک کنند.

### /etc/skel

شاید برایتان سوال شده باشد که فایل‌هایی مانند `~/.bash_profile` ،`~/.bashrc` یا `~/.bash_logout` چگونه در دایرکتوری خانگی شما ایجاد شده‌اند. این‌ها فایل‌های اسکلت (skeleton) هستند که از `/etc/skel` کپی شده‌اند.

```
root@ubuntu16-1:~# ls -1a /etc/skel/
.
..
.bash_logout
.bashrc
examples.desktop
.profile
```

### .bash_logout

فایل `.bash_logout` هر بار که یک login shell خارج می‌شود، خوانده و اجرا می‌گردد. این فایل هر زمان که لاگ‌اوت می‌کنید، صفحه را پاک می‌کند. بدون `.bash_logout` هر چیزی که روی آن کار می‌کردید ممکن است برای کاربر بعدی قابل مشاهده باشد!

```
root@ubuntu16-1:~# cat /etc/skel/.bash_logout 
# ~/.bash_logout: executed by bash(1) when login shell exits.

# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi
```

### . و source

 اگر اسکریپتی در یک شل فرزند (child shell) اجرا شود، هر متغیری که ممکن است export کند، هنگام بازگشت به والد (parent) از بین می‌رود.

```
root@ubuntu16-1:~# cat .profile 
# ~/.profile: executed by Bourne-compatible login shells.

if [ "$BASH" ]; then
  if [ -f ~/.bashrc ]; then
    . ~/.bashrc
  fi
fi

mesg n || true
```

 بنابراین اگر `.profile` اسکریپت `~/.bashrc` را اجرا می‌کند، چرا متغیرها و توابعی که از `~/.bashrc` صادر (export) می‌شوند از بین نمی‌روند؟ پاسخ این است که شما اسکریپت را با استفاده از دستور `source` (یا `.`) در محیط فعلی اجرا می‌کنید. 

### توابع شل (Shell functions)

نام‌های مستعار (Aliases) مفید هستند، اما اگر بخواهید پارامترها را مدیریت کنید چه اتفاقی می‌افتد؟ Aliases فقط کلمه اول را گسترش می‌دهند و هر چیز دیگری در خط فرمان به آن گسترش اضافه می‌شود. اگر می‌خواهید دستوری را با چند پارامتر اجرا کنید و سپس خروجی را به نوعی پردازش کنید، با یک alias شانسی نخواهید داشت. در این حالت، شما به یک تابع شل (shell function) نیاز دارید.

```
myfunc() {
    echo "hello $1"
}

# مشابه بالا (سینتکس جایگزین)
function myfunc() {
    echo "hello $1"
}
```

`$1` اولین آرگومان را برمی‌گرداند

```
root@ubuntu16-1:~# myfunc "payam"
hello payam
```

توابع شل نسبت به نام‌های مستعار چند مزیت دارند:

* می‌توانید پارامترها را مدیریت کنید.
* می‌توانید از ساختارهای برنامه‌نویسی مانند تست کردن و حلقه‌ها برای بهبود پردازش خود استفاده کنید.

{% hint style="info" %}
ما می‌توانیم از دستور `unset` برای از بین بردن تابع تعریف شده خود استفاده کنیم.
{% endhint %}

### لیست‌ها (lists)

شل از نوع متفاوتی از متغیر به نام متغیر آرایه‌ای (array variable) پشتیبانی می‌کند. این متغیر می‌تواند چندین مقدار را به طور همزمان نگه دارد. آرایه‌ها روشی برای گروه‌بندی مجموعه‌ای از متغیرها ارائه می‌دهند. به جای ایجاد یک نام جدید برای هر متغیر مورد نیاز، می‌توانید از یک متغیر آرایه‌ای واحد استفاده کنید که تمام متغیرهای دیگر را ذخیره می‌کند.

```
array_name=(value1 ... valuen)

### مثال:
root@ubuntu16-1:~# mylist=(lets learn linux)

### برای دسترسی به مقدار: {array_name[index]}

### مثال:
root@ubuntu16-1:~# echo ${mylist[2]}
linux
```

مثال دیگر:

```
root@ubuntu16-1:~# mylist2=("we are" "learning" "lpic 1-102 exam")
root@ubuntu16-1:~# echo ${mylist2[2]}
lpic 1-101 exam
```

به سینتکس (syntax) توجه کنید!

.

.

.

[http://howtolamp.com/articles/difference-between-login-and-non-login-shell/](http://howtolamp.com/articles/difference-between-login-and-non-login-shell/)

[https://askubuntu.com/questions/155865/what-are-login-and-non-login-shells](https://askubuntu.com/questions/155865/what-are-login-and-non-login-shells)

[https://www.cyberciti.biz/faq/set-environment-variable-linux/](https://www.cyberciti.biz/faq/set-environment-variable-linux/)

[https://developer.ibm.com/tutorials/l-lpic1-105-1/](https://developer.ibm.com/tutorials/l-lpic1-105-1/)

[https://jadi.gitbooks.io/lpic1/content/1051\_customize_and_use_the_shell_environment.html](https://jadi.gitbooks.io/lpic1/content/1051\_customize_and_use_the_shell_environment.html)

[https://www.tutorialspoint.com/unix/unix-using-arrays.htm](https://www.tutorialspoint.com/unix/unix-using-arrays.htm)

با تشکر ویژه از Shawn Powers.

.
