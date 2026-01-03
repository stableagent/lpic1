# 104.4. مدیریت disk quotas

**وزن:** ۱

**توضیحات:** داوطلبان باید قادر به مدیریت disk quotas (سهمیه‌بندی دیسک) برای کاربران باشند.

**حوزه‌های کلیدی دانش:**

* راه‌اندازی سهمیه‌بندی دیسک برای یک سیستم‌فایل
* ویرایش، بررسی و تولید گزارش‌های سهمیه‌بندی کاربر

**اصطلاحات و ابزارهای کاربردی:**

* quota
* edquota
* repquota
* quotaon

Quotas به ما اجازه می‌دهد میزان استفاده از دیسک را به تفکیک user یا group کنترل کنیم. Quota مانع می‌شود کاربران یا گروه‌ها بیش از سهمی که مجاز هستند از یک filesystem استفاده کنند یا آن را به طور کامل پر کنند. (در این مثال برای آزمون LPIC، از quota version 2 با kernel نسخه 2.4 به بالا استفاده می‌کنیم.)

\[در این درس quota را روی /dev/sdb1 با filesystem از نوع ext4 فعال و تنظیم می‌کنیم.]

#### بررسی وجود پکیج quota <a href="checking-for-the-quota-package" id="checking-for-the-quota-package"></a>

پکیج quota معمولاً به صورت پیش‌فرض روی نصب‌های دسکتاپ نصب نیست. با `dpkg` یا `rpm` بررسی کنید که این پکیج را دارید یا نه.

```
root@ubuntu16-1:~/mydisk# dpkg -l | grep quota
root@ubuntu16-1:~/mydisk# apt install quota
```

#### اضافه کردن پشتیبانی quota به /etc/fstab <a href="adding-quota-support-to-etc-fstab" id="adding-quota-support-to-etc-fstab"></a>

اولین قدم برای فعال کردن quota این است که بسته به اینکه می‌خواهیم user quota یا group quota داشته باشیم، گزینه‌های مناسب را به تعریف filesystem در فایل `/etc/fstab` اضافه کنیم.

| گزینه   | معنی |
| -------- | ------------------- |
| usrquota | فعال کردن user quota |
| grpquota | فعال کردن group quota |

برای فعال کردن quota روی sdb1 باید خطی را که قبلاً به `/etc/fstab` اضافه کرده‌ایم ویرایش کنیم (ubuntu 16.04):

```
UUID=03c28ca3-daa3-49c2-9bc4-b083c3b0957b /mnt/mydisk	auto defaults,usrquota,grpquota 	0 0
```

بعد از ویرایش `/etc/fstab` و اضافه کردن گزینه‌های quota، باید filesystemها را remount کنیم.

> mount -o remount /dev/sdb1

اطلاعات user quota در فایل `aquota.user` در ریشه filesystem نگهداری می‌شود و اطلاعات group quota هم به همین شکل در `aquota.group` ذخیره می‌شود.

بعد از remount کردن filesystem، باید فایل‌های quota را ایجاد و quota checking را فعال کنیم.

```
root@ubuntu16-1:~# cd /root/mydisk/
root@ubuntu16-1:/mnt/mydisk# ls
file1.txt  file2.txt  lost+found
```

#### quotacheck

دستور `quotacheck` quotaهای همه filesystemها را بررسی می‌کند و اگر فایل‌های `aquota.user` و `aquota.group` وجود نداشته باشند آن‌ها را ایجاد می‌کند. همچنین می‌تواند فایل‌های quota آسیب‌دیده را تعمیر کند.

| سوییچ quotacheck | معنی |
| ----------------- | ------------------------------------------------------------------------- |
| -a                | بررسی همه filesystemهای mount شده در `/etc/mtab` (به جز filesystemهای NFS) |
| -c                | نادیده گرفتن فایل‌های quota موجود؛ اسکن جدید انجام می‌دهد و نتیجه را روی دیسک می‌نویسد |
| -u                | بررسی user quota (پیش‌فرض) |
| -g                | بررسی group quota |
| -v                | خروجی verbose |

```
root@ubuntu16-1:/mnt/mydisk# quotacheck -acugv
quotacheck: Your kernel probably supports journaled quota but you are not using it. Consider switching to journaled quota to avoid running quotacheck after an unclean shutdown.
quotacheck: Scanning /dev/sdb1 [/root/mydisk] done
quotacheck: Cannot stat old user quota file /root/mydisk/aquota.user: No such file or directory. Usage will not be subtracted.
quotacheck: Cannot stat old group quota file /root/mydisk/aquota.group: No such file or directory. Usage will not be subtracted.
quotacheck: Cannot stat old user quota file /root/mydisk/aquota.user: No such file or directory. Usage will not be subtracted.
quotacheck: Cannot stat old group quota file /root/mydisk/aquota.group: No such file or directory. Usage will not be subtracted.
quotacheck: Checked 2 directories and 2 files
quotacheck: Old file not found.
quotacheck: Old file not found.

root@ubuntu16-1:/mnt/mydisk# ls
aquota.group  aquota.user  file1.txt  file2.txt  lost+found
```

فایل‌های `aquota.user` و `aquota.group` ایجاد شدند.

{% hint style="danger" %}
`aquota.user` و `aquota.group` هر دو فایل binary هستند و نباید به صورت دستی ویرایش شوند.

```
root@ubuntu16-1:/mnt/mydisk# cat aquota.group 
':	:	proot@ubuntu16-1:~/mydisk# ^C
```
{% endhint %}

#### روشن/خاموش کردن quota <a href="start-or-stop-quota-checking" id="start-or-stop-quota-checking"></a>

### quotaon

دستور `quotaon` disk quota را برای یک یا چند filesystem مشخص‌شده توسط پارامتر FileSystem فعال می‌کند. filesystem موردنظر باید در `/etc/filesystems` با quota تعریف شده باشد و همچنین باید mount شده باشد.

`quotaon` به دنبال فایل‌های `quota.user` و `quota.group` در ریشه filesystem مرتبط می‌گردد و اگر پیدا نکند خطا برمی‌گرداند.

```
oot@ubuntu16-1:/mnt/mydisk# ls
aquota.group  aquota.user  file1.txt  file2.txt  lost+found

root@ubuntu16-1:/mnt/mydisk# quotaon /dev/sdb1
#OR
root@ubuntu16-1:/mnt/mydisk# quotaon /root/mydisk
```

گزینه‌های رایج `-a`، `-g`، `-u` و `-v` همان معنیِ گزینه‌های متناظر در دستور `quotacheck` را دارند. همچنین اگر گزینه `-a` را مشخص نکنید، باید یک filesystem را مشخص کنید. اگر فقط می‌خواهید ببینید quota روشن است یا خاموش، از گزینه `-p` استفاده کنید.

> `quotaoff` به سیستم اعلام می‌کند که quota برای filesystemهای مشخص‌شده خاموش شود.

#### تنظیم محدودیت‌های quota

### edquota

Quotaها یا از طریق فایل‌های binary در ریشه filesystem کنترل می‌شوند یا از طریق metadata مربوط به filesystem. برای تنظیم quota برای یک user مشخص، از دستور `edquota` استفاده کنید. این دستور اطلاعات quota کاربر را از filesystemهای دارای quota استخراج می‌کند، یک فایل موقت می‌سازد و یک ویرایشگر باز می‌کند تا بتوانیم quotaها را تغییر دهیم.

* با گزینه `-u` (پیش‌فرض) برای یک یا چند نام کاربری.
* برای ویرایش group quota، از `-g` برای یک یا چند نام گروه استفاده کنید.

```
root@ubuntu16-1:/mnt/mydisk# edquota -u user1
```

```
Disk quotas for user user1 (uid 1001):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/sdb1                       0         0           0          0        0        3
```

`edquota` میزان مصرف فعلی من از blockهای 1K و inodeها را روی هر filesystemی که quota روی آن فعال است نمایش می‌دهد.

می‌توانید block limit را یک محدودیت تقریبی روی مقدار داده‌ای که کاربر می‌تواند ذخیره کند در نظر بگیرید و inode limit را محدودیتی روی تعداد فایل‌ها و دایرکتوری‌ها.

برای هر دو مورد block و inode، «soft limit» و «hard limit» هم داریم. در این مثال مقدار آن‌ها 0 است، یعنی هیچ محدودیتی اعمال نمی‌شود.

{% hint style="info" %}
**soft limit در برابر hard limit**

soft limit مقداری است که وقتی کاربر از آن عبور کند هشدار ایمیلی (email warning) درباره «over quota» دریافت می‌کند. hard limit مقداری است که کاربر اجازه ندارد از آن عبور کند.

#### دوره ارفاق (The grace period) <a href="the-grace-period" id="the-grace-period"></a>

userها یا groupها می‌توانند برای یک _grace period_ از soft limit عبور کنند؛ مقدار پیش‌فرض آن **هفت روز** است. بعد از پایان دوره ارفاق، soft limit مثل hard limit اعمال می‌شود. وقتی hard limit برسد، باید برخی فایل‌ها حذف شوند تا امکان ایجاد فایل‌های جدید وجود داشته باشد.

دوره‌های ارفاق را با `edquota -t` تنظیم می‌کنیم. دوباره وارد یک ویرایشگر خواهید شد:

```
Time units may be: days, hours, minutes, or seconds
  Filesystem             Block grace period     Inode grace period
  /dev/sdb1                     7days                  7days
```
{% endhint %}

حالا بیایید برای user1 روی /dev/sdb1 یک hard limit برای inodeها تنظیم کنیم:

```
root@ubuntu16-1:/mnt/mydisk# edquota -u user1
```

```
Disk quotas for user user1 (uid 1001):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/sdb1                         0          0          0          0        0        3
```

و حالا بررسی کنیم:

```
user1@ubuntu16-1:/mnt/mydisk$ ls
aquota.group  aquota.user  file1.txt  file2.txt  lost+found
user1@ubuntu16-1:/mnt/mydisk$ touch user1file1
user1@ubuntu16-1:/mnt/mydisk$ touch user1file2
user1@ubuntu16-1:/mnt/mydisk$ touch user1file3
user1@ubuntu16-1:/mnt/mydisk$ touch user1file4
touch: cannot touch 'user1file4': Disk quota exceeded
```

برای کپی کردن محدودیت‌های quota از یک user به user دیگر، از سوییچ `-p` استفاده کنید:

`edquota ‑p user1 user2 user2`

#### گزارش‌های quota

### quota

دستور `quota` بدون گزینه، quotaهای یک user را روی filesystemهایی نمایش می‌دهد که quota روی آن‌ها فعال شده است (برای آن user quota باید تنظیم شده باشد و کاربر هم باید فایل‌هایی روی آن filesystem داشته باشد):

```
root@ubuntu16-1:/mnt/mydisk# quota -u user1
Disk quotas for user user1 (uid 1001): 
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
      /dev/sdb1       0       0       0               3*      0       3        
```

> گزینه `-v` اطلاعات همه filesystemهایی را که quota روی آن‌ها فعال است نمایش می‌دهد.

بررسی quotaها به صورت تک‌به‌تک برای هر user چندان مفید نیست؛ بنابراین بهتر است از `repquota` برای تولید گزارش‌ها استفاده کنید.

### repquota

repquota مخفف report for quota است. این دستور میزان مصرف filesystem و اطلاعات quota را روی سیستم نمایش می‌دهد.

```
root@ubuntu16-1:/mnt/mydisk# repquota /dev/sdb1
*** Report for user quotas on device /dev/sdb1
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      --      28       0       0              4     0     6       
user1     --       0       0       0              3     0     3      
```

> گزینه `-a` گزارشی برای همه filesystemهای mount شده که quota روی آن‌ها فعال است تولید می‌کند. گزینه `-v` خروجی verboseتری می‌دهد.

تمام!

.

.

.

{% hint style="info" %}
### هشدار دادن به کاربران (Warning users) <a href="warning-users" id="warning-users"></a>

دستور `warnquota` برای ارسال هشدار ایمیلی به کاربرانی استفاده می‌شود که از quota عبور کرده‌اند. وقتی یک group از quota عبور کند، ایمیل به کاربری ارسال می‌شود که برای آن group در `/etc/quotagrpadmins` مشخص شده است. قالب ایمیل با فایل `/etc/warnquota.conf` کنترل می‌شود. فایل `/etc/quotatab` برای تبدیل نام‌هایی مثل `/dev/sdc6` به توضیح‌های کاربرپسندتر مثل “Shared EXT3 filesystem” استفاده می‌شود. معمولاً `warnquota` به صورت دوره‌ای به عنوان یک job در `cron` اجرا می‌شود (بعداً درباره cron صحبت می‌کنیم).
{% endhint %}

.

[https://developer.ibm.com/tutorials/l-lpic1-104-4/](https://developer.ibm.com/tutorials/l-lpic1-104-4/)

[https://www.ibm.com/support/knowledgecenter/ssw_aix\_71/q_commands/quotaon.html](https://www.ibm.com/support/knowledgecenter/ssw_aix\_71/q_commands/quotaon.html)

[https://linux.101hacks.com/unix/repquota/](https://linux.101hacks.com/unix/repquota/)

.
