# 107.1 مدیریت حساب‌های کاربری و گروهی و فایل‌های سیستمی مرتبط

## **107.1 مدیریت حساب‌های کاربری و گروهی و فایل‌های سیستمی مرتبط**

**وزن:** ۵

**توضیحات:** داوطلبان باید قادر به افزودن، حذف، تعلیق و تغییر حساب‌های کاربری باشند.

**حوزه‌های کلیدی دانش:**

* افزودن، تغییر و حذف کاربران و گروه‌ها
* مدیریت اطلاعات کاربر/گروه در پایگاه‌های داده رمز عبور/گروه
* ایجاد و مدیریت حساب‌های با اهداف خاص و محدود

**اصطلاحات و ابزارهای کاربردی:**

* /etc/passwd
* /etc/shadow
* /etc/group
* /etc/skel/
* chage
* getent
* groupadd
* groupdel
* groupmod
* passwd
* useradd
* userdel
* usermod

## تغییر رمز عبور

### passwd

دستور `passwd` رمز عبور حساب‌های کاربری را تغییر می‌دهد. یک کاربر معمولی فقط می‌تواند رمز عبور حساب خود را تغییر دهد، اما superuser می‌تواند رمز عبور هر حسابی را تغییر دهد.

```
user1@ubuntu16-1:~$ passwd 
Changing password for user1.
(current) UNIX password: 
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
```

۱. قبل از اینکه یک کاربر معمولی بتواند رمز عبور خود را تغییر دهد، ابتدا باید رمز عبور فعلی خود را برای تأیید وارد کند. (superuser هنگام تغییر رمز عبور کاربر دیگر، می‌تواند از این مرحله عبور کند.)
۲. پس از تأیید رمز عبور فعلی، `passwd` بررسی می‌کند که آیا کاربر در این زمان مجاز به تغییر رمز عبور خود هست یا خیر. سپس از کاربر دو بار درخواست رمز عبور جدید می‌شود.
۳. سپس، رمز عبور برای پیچیدگی تست می‌شود. رمزهای عبور باید حداقل شامل ۶ کاراکتر باشند.

کاربر root می‌تواند رمز عبور هر کاربری را به هر چیزی (حتی رمزهای ضعیف) بدون ارائه رمز عبور فعلی آن‌ها تغییر دهد:

```
root@ubuntu16-1:~# passwd user1
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
```

> گروه‌ها نیز می‌توانند رمز عبور داشته باشند که با دستور `gpasswd` تنظیم می‌شود، اما اصلاً مورد استفاده قرار نمی‌گیرد!

## کاربران و گروه‌ها

ما یاد گرفته‌ایم که لینوکس یک سیستم چندکاربره است. به یاد بیاورید که می‌توانیم با یک کاربر وارد شده و با استفاده از دستورات `su` یا `sudo` به کاربر دیگری تبدیل شویم. 

لینوکس همچنین مفهوم گروه‌ها را دارد. 

* هر کاربر به یک گروه اصلی (primary group) و احتمالاً گروه‌های اضافی (additional groups) تعلق دارد. 
* هر فایل متعلق به یک کاربر و یک گروه است.

ما نحوه ایجاد، حذف و مدیریت کاربران و گروه‌ها را یاد می‌گیریم.

## مدیریت کاربران 

### useradd

 ما با دستور `useradd` یک کاربر را به سیستم لینوکس اضافه می‌کنیم.

```
 useradd <options> <username_or_login>
```

| سوئیچ | توضیحات                                  |
| ------ | -------------------------------------------- |
| -d     | دایرکتوری خانگی حساب جدید            |
| -m     | ایجاد دایرکتوری خانگی کاربر             |
| -s     | شل لاگین حساب جدید               |
| -G     | افزودن به گروه‌های اضافی                     |
| -c     | کامنت، اغلب اوقات نام واقعی کاربر |

در بیشتر توزیع‌ها، `useradd` دایرکتوری خانگی را برای کاربر جدید ایجاد می‌کند، اما با استفاده از سوئیچ `-m` می‌توانیم از این موضوع مطمئن شویم. مثال (اوبونتو ۱۶):

```
root@ubuntu16-1:~# useradd -m -d /home/user3 -c "Dear user3" -s /bin/bash user3
```

### /etc/skel

{% hint style="info" %}
#### اسکلت دایرکتوری خانگی

هنگامی که یک کاربر جدید ایجاد می‌کنید و دایرکتوری خانگی جدیدی ساخته می‌شود، این دایرکتوری با چندین فایل و زیر‌دایرکتوری پر می‌شود که به طور پیش‌فرض از `/etc/skel` کپی می‌شوند.

```
root@ubuntu16-1:~# ls -a /etc/skel/
.  ..  .bash_logout  .bashrc  examples.desktop  .profile
```
{% endhint %}

### usermod <a href="the-home-directory-skeleton" id="the-home-directory-skeleton"></a>

 ما می‌توانیم از دستور `usermod` برای تغییر یک حساب کاربری استفاده کنیم. ما می‌توانیم از بیشتر گزینه‌هایی که با `useradd` استفاده می‌شود استفاده کنیم، با این تفاوت که نمی‌توانید یک دایرکتوری خانگی جدید برای کاربر ایجاد یا پر کنید.

```
usermod <options> <username_or_login>
```

| سوئیچ | توضیحات                                                                                 |
| ------ | ------------------------------------------------------------------------------------------- |
| -L     | قفل کردن حساب کاربری                                                                       |
| -U     | باز کردن قفل حساب کاربری                                                                     |
| -g     | اجبار به استفاده از GROUP به عنوان گروه اصلی جدید                                                        |
| -G     | لیست جدید گروه‌های اضافی (کاربر از تمام گروه‌های اضافی قبلی حذف خواهد شد) |
| -aG    | اضافه کردن کاربر به گروه‌های اضافی (بدون حذف او از سایر گروه‌ها)       |

```
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1003(user3) groups=1003(user3)

root@ubuntu16-1:~# usermod -g user1 user3
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1001(user1) groups=1001(user1),1003(user3)

root@ubuntu16-1:~# usermod -G user2 user3
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1001(user1) groups=1001(user1),1002(user2)

root@ubuntu16-1:~# usermod -aG payam user3
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1001(user1) groups=1001(user1),1000(payam),1002(user2)

### بیایید به تنظیمات پیش‌فرض برگردیم
root@ubuntu16-1:~# usermod -g user3 -G user3 user3
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1003(user3) groups=1003(user3)
```

### userdel

 ما می‌توانیم یک کاربر را با دستور `userdel` حذف کنیم.

```
userdel <options> <username_or_login>
```

دستور `userdel` به طور پیش‌فرض دایرکتوری خانگی کاربر را حذف نمی‌کند.

| سوئیچ | توضیحات                          |
| ------ | ------------------------------------ |
| -f     | اجبار به حذف فایل‌ها               |
| -r     | حذف دایرکتوری خانگی و ایمیل‌ها |

```
root@ubuntu16-1:~# userdel -f -r user3
userdel: user3 mail spool (/var/mail/user3) not found
```

### مدیریت گروه‌ها

 به طور مشابه، می‌توانیم گروه‌ها را با دستورات `groupadd` و `groupdel` اضافه یا حذف کنیم.

### groupadd 

```
groupadd [options] group
```

| سوئیچ | توضیحات                                                                             |
| ------ | --------------------------------------------------------------------------------------- |
| -g     | استفاده از GID برای گروه جدید                                                               |
| -f     | خروج موفقیت‌آمیز اگر گروه از قبل وجود داشته باشد، و لغو `-g` اگر GID قبلاً استفاده شده باشد |
| -p     | استفاده از این رمز عبور رمزگذاری شده برای گروه جدید                                           |

```
root@ubuntu16-1:~# groupadd -g 666  group1
root@ubuntu16-1:~# groupadd -g 666  group1
groupadd: group 'group1' already exists
root@ubuntu16-1:~# echo $?
9
root@ubuntu16-1:~# groupadd -f -g 666  group1
root@ubuntu16-1:~# echo $?
0

```

### groupmod

 هنگامی که نیاز به تغییر اطلاعات گروه دارید، از دستور `groupmod` استفاده کنید.

```
groupmod [options] GROUP
```

| سوئیچ | توضیحات           |
| ------ | --------------------- |
| -n     | تغییر نام گروه |
| -g     | تغییر شناسه گروه (GID)   |

```
root@ubuntu16-1:~# groupmod -n newgroup1 group1
root@ubuntu16-1:~# groupmod -g 888 newgroup1 
```

### groupdel

 در واقع، دستور `groupdel` برای حذف یک گروه فقط به نام گروه نیاز دارد و هیچ گزینه‌ای ندارد. شما نمی‌توانید گروهی را که گروه اصلی (primary group) یک کاربر است، حذف کنید.

```
root@ubuntu16-1:~# groupdel newgroup1
```

> نکته: اگر root گروهی را که دارای عضو است حذف کند، کاربران حذف نخواهند شد! آن‌ها فقط دیگر عضو آن گروه نخواهند بود.



هنگامی که دستور `useradd` را در ترمینال لینوکس اجرا می‌کنیم، کارهای اصلی زیر را انجام می‌دهد:

۱. فایل‌های `/etc/passwd` ،`/etc/shadow` ،`/etc/group` و `/etc/gshadow` را برای حساب کاربری جدید ویرایش می‌کند.
۲. یک دایرکتوری خانگی برای کاربر جدید ایجاد و پر می‌کند.
۳. مجوزها و مالکیت‌ها را برای دایرکتوری خانگی تنظیم می‌کند.

آن فایل‌ها چه هستند؟

### /etc/passwd

 فایل `/etc/passwd` فایل رمز عبوری است که شامل اطلاعات اولیه درباره کاربران است.

```
root@ubuntu16-1:~# tail /etc/passwd
pulse:x:117:124:PulseAudio daemon,,,:/var/run/pulse:/bin/false
rtkit:x:118:126:RealtimeKit,,,:/proc:/bin/false
saned:x:119:127::/var/lib/saned:/bin/false
usbmux:x:120:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
payam:x:1000:1000:ubuntu16.04.3-1,,,:/home/payam:/bin/bash
user1:x:1001:1001::/home/user1:
sshd:x:121:65534::/var/run/sshd:/usr/sbin/nologin
mysql:x:122:129:MySQL Server,,,:/nonexistent:/bin/false
user2:x:1002:1002::/home/user2:
postfix:x:123:130::/var/spool/postfix:/bin/false
```

این فایل برای هر کاربر در سیستم یک خط دارد. فرمت آن به این صورت است:

![](.gitbook/assets/usergroup-passwd.jpg)

۱. **Username**: باید بین ۱ تا ۳۲ کاراکتر باشد. 
۲. **Password** (در مورد آن بحث خواهد شد).
۳. **User ID (UID)**: به هر کاربر باید یک شناسه کاربر (UID) اختصاص داده شود. UID 0 (صفر) برای root رزرو شده است و UIDهای ۱-۹۹ برای سایر حساب‌های از پیش تعریف شده رزرو شده‌اند. همچنین UIDهای ۱۰۰-۹۹۹ توسط سیستم برای حساب‌ها/گروه‌های مدیریتی و سیستمی رزرو شده‌اند. 
۴. **Group ID (GID)**: شناسه گروه اصلی (ذخیره شده در فایل `/etc/group`). 
۵. **فیلد کامنت**: به شما امکان می‌دهد اطلاعات اضافی درباره کاربران مانند نام کامل کاربر، شماره تلفن و غیره را اضافه کنید. این فیلد توسط دستور `finger` استفاده می‌شود. 
۶. **Home directory** (دایرکتوری خانگی).
۷. **Command/shell**: مسیر مطلق یک دستور یا شل (`/bin/bash`). معمولاً این یک شل است، اما لزوماً نباید شل باشد.

> برخی از کاربران دارای شل `/sbin/nologin` هستند؛ آن‌ها در واقع حساب‌های سیستمی هستند که یک سرویس را اجرا می‌کنند و هیچ‌کس نمی‌تواند به صورت تعاملی با استفاده از آن‌ها لاگین کند. گاهی اوقات این فیلد روی `/bin/false` تنظیم شده است. 

هر کاربر باید دسترسی خواندن به `/etc/passwd` داشته باشد:

```
root@ubuntu16-1:~# ls -l /etc/passwd
-rw-r--r-- 1 root root 2469 Feb 12 02:53 /etc/passwd
```

در گذشته مکانی وجود داشت که تمام اطلاعات کاربران حتی رمز عبور کاربر در آن قرار داشت و تصور مشکل امنیتی که ایجاد می‌کرد چندان دشوار نیست. برای حل این مشکل، `/etc/shadow` اختراع شد. کاراکتر `x` نشان می‌دهد که رمز عبور رمزگذاری شده در فایل `/etc/shadow` ذخیره شده است.

### /etc/shadow

فایل `/etc/shadow` شامل رمزهای عبور رمزگذاری شده، به همراه اطلاعات انقضای رمز عبور و حساب کاربری است.

```
root@ubuntu16-1:~# ls -l /etc/shadow
-rw-r----- 1 root shadow 1609 Feb 12 02:53 /etc/shadow
```

بیایید محتویات آن را ببینیم:

```
root@ubuntu16-1:~# tail /etc/shadow
pulse:*:17379:0:99999:7:::
rtkit:*:17379:0:99999:7:::
saned:*:17379:0:99999:7:::
usbmux:*:17379:0:99999:7:::
payam:$1$jYgAdos4$Je8la0839ZRVgazhnBpDv1:17496:0:99999:7:::
user1:$6$c9PN.175$.t.CG0E0Gtr/trq4pqquSe1BemMjB6Zc3E0ExUOVufuTkPNe3BSRv3DyUuXFHPiAbEujzuSMCeMsCbpg8cV2j.:17749:0:99999:7:::
sshd:*:17749:0:99999:7:::
mysql:!:17867:0:99999:7:::
user2:$6$kN2DNYrP$XmM/3ONRnrTCuTTBxCwVBlVW9E4tVRc02JbRHPhwj128Q6aUIcUq4gxw2r74gopOs2J0HqNxuiBiqgAlkmuwV1:18290:0:99999:7:::
postfix:*:18300:0:99999:7:::
```

> نکته: `!!` به این معنی است که کاربر نمی‌تواند با هیچ رمز عبوری لاگین کند. بیشتر حساب‌های سرویس به این صورت هستند.

رمزهای عبور را می‌توان با یکی از چندین الگوریتم رمزگذاری، رمزگذاری کرد. سیستم‌های قدیمی از DES یا MD5 استفاده می‌کردند، اما سیستم‌های مدرن معمولاً از Blowfish، SHA-256 یا SHA-512 و احتمالاً MD5 استفاده می‌کنند. صرف نظر از الگوریتم رمزگذاری، رمزهای عبور "salt" می‌شوند تا دو رمز عبور یکسان، مقدار رمزگذاری شده یکسانی تولید نکنند.

![](.gitbook/assets/usergroup-shadow.jpg)

۱. **Username**: نام لاگین شماست. 
۲. **Password**: رمز عبور رمزگذاری شده شماست. رمز عبور باید حداقل ۸-۱۲ کاراکتر طول داشته باشد، شامل کاراکترهای خاص، ارقام، حروف کوچک و غیره. معمولاً فرمت رمز عبور به صورت `$id$salt$hashed` تنظیم می‌شود. `$id` الگوریتم مورد استفاده در GNU/Linux است: `$1$` برای MD5، `$2a$` برای Blowfish، `$2y$` برای Blowfish، `$5$` برای SHA-256 و `$6$` برای SHA-512 است. 
۳. **آخرین تغییر رمز عبور (lastchanged)**: تعداد روزهای سپری شده از ۱ ژانویه ۱۹۷۰ که رمز عبور آخرین بار تغییر کرده است. 
۴. **Minimum**: حداقل تعداد روزهای مورد نیاز بین تغییرات رمز عبور؛ یعنی تعداد روزهای باقی‌مانده قبل از اینکه کاربر مجاز به تغییر رمز عبور خود باشد. 
۵. **Maximum**: حداکثر تعداد روزهایی که رمز عبور معتبر است (پس از آن کاربر مجبور به تغییر رمز عبور خود می‌شود). 
۶. **Warn**: تعداد روزهای قبل از انقضای رمز عبور که به کاربر هشدار داده می‌شود باید رمز عبور خود را تغییر دهد. 
۷. **Inactive**: تعداد روزهای پس از انقضای رمز عبور که حساب کاربری غیرفعال می‌شود. 
۸. **Expire**: تعداد روزهای سپری شده از ۱ ژانویه ۱۹۷۰ که حساب غیرفعال شده است؛ یعنی یک تاریخ مطلق که مشخص می‌کند از چه زمانی دیگر نمی‌توان از لاگین استفاده کرد.

۶ فیلد آخر ویژگی‌های مربوط به طول عمر رمز عبور و قفل شدن حساب را فراهم می‌کنند. برای تنظیم طول عمر رمز عبور باید از دستور `chage` استفاده کنید.

{% hint style="info" %}
#### زمان Epoch

زمان یونیکس سیستمی برای توصیف یک نقطه در زمان است. این زمان، تعداد ثانیه‌های سپری شده از مبدأ یونیکس (Unix epoch)، یعنی ساعت ۰۰:۰۰:۰۰ UTC در ۱ ژانویه ۱۹۷۰، منهای ثانیه‌های کبیسه است.
{% endhint %}

### chage

 دستور **chage** برای مشاهده و تغییر اطلاعات انقضای رمز عبور کاربر استفاده می‌شود. این دستور زمانی استفاده می‌شود که قرار است لاگین برای مدتی محدود در اختیار کاربر قرار گیرد یا زمانی که لازم است رمز عبور لاگین هر از چند گاهی تغییر کند.

```
chage [options] LOGIN
```

```
Options:
  -d, --lastday LAST_DAY        set date of last password change to LAST_DAY
  -E, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
  -h, --help                    display this help message and exit
  -I, --inactive INACTIVE       set password inactive after expiration
                                to INACTIVE
  -l, --list                    show account aging information
  -m, --mindays MIN_DAYS        set minimum number of days before password
                                change to MIN_DAYS
  -M, --maxdays MAX_DAYS        set maximim number of days before password
                                change to MAX_DAYS
  -R, --root CHROOT_DIR         directory to chroot into
  -W, --warndays WARN_DAYS      set expiration warning days to WARN_DAYS

```

دستور `chage` بدون هیچ گزینه‌ای، امکان ویرایش تمام موارد را به صورت تعاملی فراهم می‌کند. بیایید گزینه `-l` را روی `user1` امتحان کنیم:

```
root@ubuntu16-1:~# chage -l user1
Last password change                    : Aug 06, 2018
Password expires                    : never
Password inactive                    : never
Account expires                        : never
Minimum number of days between password change        : 0
Maximum number of days between password change        : 99999
Number of days of warning before password expires    : 7
```

> `chage -d 0 user-name` کاربر را مجبور می‌کند در لاگین بعدی رمز عبور خود را تغییر دهد.

> `passwd` همچنین می‌تواند دوره اعتبار حساب را تغییر داده یا بازنشانی کند - اینکه چه مدت زمان می‌تواند قبل از انقضای رمز عبور و نیاز به تغییر آن سپری شود.

### /etc/group

 فایل `/etc/group` فایل گروه‌هاست که شامل اطلاعات اولیه درباره گروه‌ها و کاربرانی است که به آن‌ها تعلق دارند. این فایل برای هر گروه در سیستم یک خط دارد.

```
root@ubuntu16-1:~# tail /etc/group
rtkit:x:126:
saned:x:127:
payam:x:1000:
sambashare:x:128:payam
user1:x:1001:
mysql:x:129:
user2:x:1002:
postfix:x:130:
postdrop:x:131:
mysecuregroup:x:1003:
```

![](.gitbook/assets/usergroup-group.jpg)

۱. **group_name**: نام گروه است. اگر دستور `ls -l` را اجرا کنید، این نام را در فیلد گروه خواهید دید. 
۲. **Password**: به طور کلی رمز عبور استفاده نمی‌شود، بنابراین خالی است. این فیلد می‌تواند رمز عبور رمزگذاری شده را ذخیره کند که برای پیاده‌سازی گروه‌های دارای امتیاز (privileged groups) مفید است. 
۳. **Group ID (GID)**: به هر کاربر باید یک شناسه گروه اختصاص داده شود. می‌توانید این شماره را در فایل `/etc/passwd` خود ببینید. 
۴. **Group List**: لیستی از نام‌های کاربری افرادی است که عضو گروه هستند. نام‌های کاربری باید با کاما از هم جدا شوند.

```
root@ubuntu16-1:~# ls -l /etc/group
-rw-r--r-- 1 root root 1077 Feb 12 03:58 /etc/group
```

مانند فایل `/etc/passwd` ، فایل `/etc/group` نیز به دلایل امنیتی shadowed شده است و باید برای همگان قابل خواندن باشد، اما رمزهای عبور رمزگذاری شده نباید برای همه قابل خواندن باشند.

رمزهای عبور گروه‌ها در فایل `/etc/gshadow` ذخیره می‌شوند که فقط توسط root قابل خواندن است.

```
root@ubuntu16-1:~# ls -l /etc/gshadow
-rw-r----- 1 root shadow 902 Feb 12 03:58 /etc/gshadow

root@ubuntu16-1:~# tail /etc/gshadow
rtkit:!::
saned:!::
payam:!::
sambashare:!::payam
user1:!::
mysql:!::
user2:!::
postfix:!::
postdrop:!::
mysecuregroup:Aa12345::
```

> فرمت آن به این صورت است: 
>
> `نام گروه:رمز عبور رمزگذاری شده:مدیران گروه:اعضای گروه`

> ! : گروه‌ها می‌توانند رمز عبور داشته باشند اما در هیچ توزیعی هرگز استفاده نشده است!

### getent

 **getent** یک دستور لینوکس است که به کاربر کمک می‌کند ورودی‌ها را در تعدادی از فایل‌های متنی مهم که پایگاه داده نامیده می‌شوند، به دست آورد.

```
getent database [key ...]
```

ما به جای خواندن دستی `/etc/passwd` یا `/etc/group` ، از دستور `getent` برای پردازش اطلاعات گروه‌ها و کاربران استفاده کنیم.

```
root@ubuntu16-1:~# getent passwd payam
payam:x:1000:1000:ubuntu16.04.3-1,,,:/home/payam:/bin/bash

root@ubuntu16-1:~# getent group payam
payam:x:1000:
```

استفاده از دستور `id` را فراموش نکنید.

.

.

.

**جایزه:** دستورات و گزینه‌ها برای تغییر حساب‌های کاربری

| usermod | passwd          | chage           | هدف                                                                    |
| ------- | --------------- | --------------- | -------------------------------------------------------------------------- |
| -L      | -l(lowercase L) | N/A             | قفل کردن یا تعلیق حساب.                                               |
| -U      | -u              | N/A             | باز کردن قفل حساب.                                                        |
| N/A     | -d              | N/A             | غیرفعال کردن حساب با حذف رمز عبور                                 |
| -e      | -f              | -E              | تنظیم تاریخ انقضا برای یک حساب.                                    |
| N/A     | -n              | -m              | حداقل طول عمر رمز عبور به روز.                                     |
| N/A     | -X              | -M              | حداکثر طول عمر رمز عبور به روز.                                     |
| N/A     | -W              | -W              | تعداد روزهای هشدار قبل از اینکه رمز عبور باید تغییر کند.           |
| -f      | -i              | -I(uppercase i) | تعداد روزهای پس از انقضای رمز عبور تا غیرفعال شدن حساب. |
| N/A     | -S              | -l(lowercase L) | خروجی یک پیام کوتاه درباره وضعیت فعلی حساب.                   |

.

.

.

[https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/](https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/)

[https://www.computerhope.com/unix/upasswor.htm](https://www.computerhope.com/unix/upasswor.htm)

[https://jadi.gitbooks.io/lpic1/content/1071\_manage_user_and_group_accounts_and_related_system_files.html](https://jadi.gitbooks.io/lpic1/content/1071\_manage_user_and_group_accounts_and_related_system_files.html)

[https://askubuntu.com/questions/639990/what-is-the-group-id-of-this-group-name](https://askubuntu.com/questions/639990/what-is-the-group-id-of-this-group-name)

[https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/)

[https://www.cyberciti.biz/faq/understanding-etcshadow-file/](https://www.cyberciti.biz/faq/understanding-etcshadow-file/)

[https://en.wikipedia.org/wiki/Unix_time](https://en.wikipedia.org/wiki/Unix_time)

[https://www.cyberciti.biz/faq/understanding-etcgroup-file/](https://www.cyberciti.biz/faq/understanding-etcgroup-file/)

[https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Introduction_To_System_Administration/s3-acctsgrps-gshadow.html](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Introduction_To_System_Administration/s3-acctsgrps-gshadow.html)

[https://www.geeksforgeeks.org/chage-command-in-linux-with-examples/](https://www.geeksforgeeks.org/chage-command-in-linux-with-examples/)

.
