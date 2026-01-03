# 103.2 پردازش text streams با استفاده از filters

## **103.2 پردازش text streams با استفاده از filters**

**وزن:** 3

**توضیحات:** داوطلبان باید قادر به اعمال فیلترها (filters) روی جریان‌های متنی (text streams) باشند.

**حوزه‌های کلیدی دانش:**

ارسال فایل‌های متنی و جریان‌های خروجی از طریق فیلترهای ابزار متن برای اصلاح خروجی با استفاده از دستورات استاندارد UNIX موجود در پکیج GNU textutils

**اصطلاحات و ابزارهای کاربردی:**

* cat
* cut
* expand
* fmt
* head
* join
* less
* nl
* od
* paste
* pr
* sed
* sort
* split
* tail
* tr
* unexpand
* uniq
* wc

همه چیز در لینوکس حول جریان‌های داده (streams of data) — به ویژه جریان‌های متنی — می‌چرخد.

### جریان‌ها (streams)

یک جریان (stream) چیزی نیست جز توالی از بایت‌ها که از یک فایل، دستگاه یا برنامه به دیگری منتقل می‌شود.

ورودی و خروجی در محیط لینوکس بین سه جریان توزیع می‌شود (که در واقع فایل‌های خاصی هستند).

این جریان‌ها عبارتند از:

* **جریان ورودی استاندارد (stdin)**، که ورودی را برای دستورات فراهم می‌کند.
* **جریان خروجی استاندارد (stdout)**، که خروجی دستورات را نمایش می‌دهد.
* **جریان خطای استاندارد (stderr)**، که خروجی خطا از دستورات را نمایش می‌دهد.

جریان‌ها همچنین شماره‌گذاری شده‌اند: **stdin (0)**، **stdout (1)**، **stderr (2)**.

![](.gitbook/assets/processtxt-streams.jpg)

### پایپ کردن (piping) با |

پایپینگ مکانیزمی برای ارسال داده از یک برنامه به برنامه دیگر است. عملگری که ما استفاده می‌کنیم ( | ) است (در بیشتر کیبوردها بالای کلید بک‌اسلش `\` یافت می‌شود). کاری که این عملگر انجام می‌دهد این است که خروجی برنامه سمت چپ را به عنوان ورودی به برنامه سمت راست می‌فرستد.

```
command1 | command2
```

هر یک از دستورات می‌تواند گزینه‌ها یا آرگومان‌هایی داشته باشد. ما همچنین می‌توانیم از | برای هدایت خروجی دستور دوم در خط لوله (pipeline) به دستور سوم و غیره استفاده کنیم.

```
command 1 | command 2 | command3 | command 4 | ...
```

ساختن خط لوله‌های طولانی از دستوراتی که هر کدام قابلیت محدودی دارند، یک روش رایج در لینوکس و یونیکس برای انجام وظایف است.

```
[root@centos7-1 ~]# dmesg | less
```

### تغییر مسیر (Redirection)

لینوکس شامل دستورات تغییر مسیر برای هر جریان است. ما می‌توانیم از `>` برای هدایت جریان خروجی (بیشتر به یک فایل) استفاده کنیم.

```
[root@centos7-1 temp]# ls -1
test.txt
unexpanded.txt
zip-3.0-11.el7.x86_64.rpm
zip.cpio

[root@centos7-1 temp]# ls -1 > list.txt

[root@centos7-1 temp]# cat list.txt 
list.txt
test.txt
unexpanded.txt
zip-3.0-11.el7.x86_64.rpm
zip.cpio
```

> "|" در مقابل ">"
>
> تفاوت بین > (عملگر تغییر مسیر) و | (عملگر خط لوله یا پایپ) این است که در حالی که > یک دستور را به یک فایل متصل می‌کند، | خروجی یک دستور را به دستور دیگر متصل می‌کند.

### فیلتر کردن متن (Text filtering)

فیلتر کردن متن فرآیند گرفتن یک جریان ورودی از متن و انجام برخی تبدیل‌ها روی متن قبل از ارسال آن به یک جریان خروجی است.

## cat

دستور **cat** (کوتاه شده "**concatenate**") یکی از پرکاربردترین دستورات در سیستم‌عامل‌های شبه لینوکس/یونیکس است. دستور cat به ما اجازه می‌دهد فایل‌های تکی یا چندگانه ایجاد کنیم، محتوای فایل را مشاهده کنیم، فایل‌ها را به هم متصل کنیم و خروجی را در ترمینال یا فایل‌ها هدایت کنیم.

```
cat [OPTION] [FILE]...
```

ساده‌ترین کاربرد cat نمایش محتوای یک فایل است:

```
[root@centos7-1 ~]# cat file1
This is 1st line of file1.

This is 3rd line of file1.
```

این دستور می‌تواند محتویات چندین فایل را نشان دهد:

```
[root@centos7-1 ~]# cat file1 file2
This is 1st line of file1.

This is 3rd line of file1.
This is 1st line of file1.
This is 2nd line of file2.

This is 4th line of file2.
```

دستور cat همچنین برای متصل کردن تعدادی فایل به یکدیگر استفاده می‌شود:

```
[root@centos7-1 ~]# cat file1 file2 > newfile
[root@centos7-1 ~]# cat newfile 
This is 1st line of file1.

This is 3rd line of file1.
This is 1st line of file2.
This is 2nd line of file2.

This is 4th line of file2.
```

ایجاد یک فایل جدید با cat:

```
[root@centos7-1 ~]# cat > newfile2
This is my second new file with input redirection
Ctrl+d
[root@centos7-1 ~]# cat newfile2
This is my second new file with input redirection
```

> "-" یک خط تیره (که به تنهایی استفاده می‌شود) عموماً نشان می‌دهد که ورودی به جای یک فایل نام‌گذاری شده، از stdin گرفته می‌شود:
>
> ```
> [root@centos7-1 ~]# cat file1 - file2
> This is 1st line of file1.
>
> This is 3rd line of file1.
> THIS IS MY INPUT 
> Ctrl+d
> This is 1st line of file2.
> This is 2nd line of file2.
>
> This is 4th line of file2.
> ```

List of cat command options:

```
  -A, --show-all           equivalent to -vET
  -b, --number-nonblank    number nonempty output lines, overrides -n
  -e                       equivalent to -vE
  -E, --show-ends          display $ at end of each line
  -n, --number             number all output lines
  -s, --squeeze-blank      suppress repeated empty output lines
  -t                       equivalent to -vT
  -T, --show-tabs          display TAB characters as ^I
  -u                       (ignored)
  -v, --show-nonprinting   use
```

حالا برعکس cat چیست؟ بله، آن ‘tac‘ است. `tac` یک دستور در لینوکس است، خودتان آن را امتحان کنید.

## od

دستور od (مخفف Octal dump) در لینوکس برای نمایش محتویات یک فایل در فرمت‌های مختلف استفاده می‌شود که فرمت اکتال (octal) پیش‌فرض است.
این دستور به ویژه هنگام دیباگ کردن اسکریپت‌های لینوکس برای تغییرات یا کاراکترهای ناخواسته مفید است.

```
od [OPTION]... [FILE]...
```

به عنوان مثال:

```
[root@centos7-1 ~]# cat testod.txt 
1
2
3
4
5
[root@centos7-1 ~]# od testod.txt 
0000000 005061 005062 005063 005064 005065
0000012
```

With `-t` option we can select output format and display it. (Traditional format specifications may be intermixed):

```
-a same as -t a, select named characters, ignoring high-order bit
-b same as -t o1, select octal bytes
-c same as -t c, select printable characters or backslash escapes
-d same as -t u2, select unsigned decimal 2-byte units
-f same as -t fF, select floats
-i same as -t dI, select decimal ints
-l same as -t dL, select decimal longs
-o same as -t o2, select octal 2-byte units
-s same as -t d2, select decimal 2-byte units
-x same as -t x2, select hexadecimal 2-byte units
```

example:

```
[root@centos7-1 ~]# od -ta testod.txt 
0000000   1  nl   2  nl   3  nl   4  nl   5  nl
0000012
[root@centos7-1 ~]# od -tc testod.txt 
0000000   1  \n   2  \n   3  \n   4  \n   5  \n
0000012
```

`-A` Option displays the contents of input in different format by concatenation some special character (offsets).

* **Hexadecimal (using -x along with -A)**
* **Octal (using -o along with -A)**
* **Decimal (using -d along with -A)**

```
[root@centos7-1 ~]# od -Ax -c testod.txt 
000000   1  \n   2  \n   3  \n   4  \n   5  \n
00000a
[root@centos7-1 ~]# od -Ao -c testod.txt 
0000000   1  \n   2  \n   3  \n   4  \n   5  \n
0000012
[root@centos7-1 ~]# od -Ad -c testod.txt 
0000000   1  \n   2  \n   3  \n   4  \n   5  \n
0000010
```

`-An` Option displays the contents of input in character format but with no offset information:

```
[root@centos7-1 ~]# od -An -c testod.txt 
   1  \n   2  \n   3  \n   4  \n   5  \n
```

## expand and unexpand

دستور **expand** برای تبدیل تب‌ها (tabs) در فایل‌ها به فضاها (spaces) استفاده می‌شود.

```
 expand [OPTION]... [FILE]...
```

بیایید آن را امتحان کنیم:

```
[root@centos7-1 ~]# cat test.txt 
this    is    my    test    file.
[root@centos7-1 ~]# od -tc -An test.txt 
   t   h   i   s  \t   i   s  \t   m   y  \t   t   e   s   t  \t
   f   i   l   e   .  \n
[root@centos7-1 ~]# expand test.txt > expanded.txt
[root@centos7-1 ~]# od -tc -An expanded.txt 
   t   h   i   s                   i   s                        
   m   y                           t   e   s   t                
   f   i   l   e   .  \n
```

به طور پیش‌فرض، expand تب‌ها را به تعداد فضاهای مربوطه تبدیل می‌کند. اما امکان تغییر تعداد فضاها با استفاده از گزینه خط فرمان -t (– – tabs=N) وجود دارد. این گزینه از ما می‌خواهد تعداد جدید فضاها (N) را که می‌خواهیم تب‌ها به آن تبدیل شوند وارد کنیم.

```
[root@centos7-1 ~]# expand -t1 test.txt > expanded2.txt
[root@centos7-1 ~]# od -tc -An expanded2.txt 
   t   h   i   s       i   s       m   y       t   e   s   t    
   f   i   l   e   .  \n
```

گزینه‌های دستور expand:

```
  -i, --initial       do not convert tabs after non blanks
  -t, --tabs=NUMBER   have tabs NUMBER characters apart, not 8
  -t, --tabs=LIST     use comma separated list of explicit tab positions
      --help     display this help and exit
      --version  output version information and exit
```

دستور **unexpand** برای تبدیل کاراکترهای فضا (blanks) به تب در هر فایل استفاده می‌شود (unexpand حداقل به دو فضا نیاز دارد).

```
unexpand [OPTION]... [FILE]...
```

بیایید معکوس عمل کنیم:

```
[root@centos7-1 ~]# unexpand expanded.txt > unexpanded.txt
[root@centos7-1 ~]# od -tc -An unexpanded.txt 
   t   h   i   s                   i   s                        
   m   y                           t   e   s   t                
   f   i   l   e   .  \n
```

unexpand بدون هیچ گزینه‌ای فقط فضاهای ابتدایی را تبدیل می‌کند!!! گزینه `-a` تمام فضاها را به جای فقط فضاهای ابتدایی تبدیل می‌کند:

> unexpand فقط فضاهای دوتایی و بیشتر را به تب تبدیل می‌کند، فضاهای تکی را تبدیل نمی‌کند!

```
[root@centos7-1 ~]# unexpand -a expanded.txt > unexpanded2.txt
[root@centos7-1 ~]# od -tc -An unexpanded2.txt 
   t   h   i   s  \t   i   s  \t   m   y  \t   t   e   s   t  \t
   f   i   l   e   .  \n
```

گزینه‌های دستور unexpand:

```
  -a, --all        convert all blanks, instead of just initial blanks
      --first-only  convert only leading sequences of blanks (overrides -a)
  -t, --tabs=N     have tabs N characters apart instead of 8 (enables -a)
  -t, --tabs=LIST  use comma separated LIST of tab positions (enables -a)
      --help     display this help and exit
      --version  output version information and exit
```

## tr command

tr مخفف **translate** است. ابزار tr ورودی استاندارد را با جایگزینی یا حذف کاراکترهای انتخاب شده در خروجی استاندارد کپی می‌کند. سینتکس دستور tr به این صورت است:

```
tr [option] set1 [set2]
```

بیایید حروف کوچک را به حروف بزرگ تبدیل کنیم:

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr abcdefghijklmnopqrstuvwxyz ABCDEFGHIJKLMNOPQRSTUVWXYZ
THIS IS  FOR   TEST 123
```

دستور زیر نیز حروف کوچک را به حروف بزرگ تبدیل می‌کند:

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr [:lower:] [:upper:]
THIS IS  FOR   TEST 123
```

تبدیل فضا (white-space) به تب (tab):

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr [:space:] '\t'
this    is        for            test    123
```

اگر دو یا چند فضا به طور مداوم وجود داشته باشد، دستور قبلی هر فضا را به یک تب تبدیل می‌کند. می‌توانیم از گزینه `-s` برای فشرده‌سازی (squeeze) تکرار کاراکترها استفاده کنیم:

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr -s [:space:] '\t'
this    is    for    test    123
```

گزینه `-d` می‌تواند برای حذف کاراکترهای مشخص شده استفاده شود:

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr -d 't'
his is  for   es 123
[root@centos7-1 ~]# echo "this is  for   test 123" | tr -d [:digit:]
this is  for   test 
```

ما مجموعه‌ها را با استفاده از گزینه `-c` متمم می‌کنیم. به عنوان مثال، برای حذف تمام کاراکترها به جز ارقام، می‌توانید از دستور زیر استفاده کنید:

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr -dc [:digit:]
123
```

tr گزینه‌ها و مجموعه‌های زیادی دارد، برای اطلاعات بیشتر tr --help را امتحان کنید.

## pr

دستور pr برای قالب‌بندی فایل‌ها جهت چاپ استفاده می‌شود. هدر (header) پیش‌فرض شامل نام فایل و تاریخ و زمان ایجاد فایل، به همراه شماره صفحه و دو خط پاورقی (footer) خالی است.

```
[root@centos7-1 ~]# cat note.txt 
hi 
this is my note file.
linux is an operating system.
learn linux.

[root@centos7-1 ~]# pr note.txt 


2018-12-19 11:25                     note.txt                     Page 1


hi 
this is my note file.
linux is an operating system.
learn linux.
```

**توجه:** وقتی خروجی از چندین فایل یا جریان ورودی استاندارد ایجاد می‌شود، به جای نام فایل و تاریخ ایجاد، از تاریخ و زمان فعلی استفاده می‌شود.

ما می‌توانیم فایل‌ها را در ستون‌های کنار هم چاپ کنیم و بسیاری از جنبه‌های قالب‌بندی را از طریق گزینه‌ها کنترل کنیم.

```
[root@centos7-1 ~]# rpm -qa | pr --columns=2 -l 15
2018-12-19 11:35                                                  Page 1


NetworkManager-team-1.8.0-9.el7.x86 setserial-2.17-33.el7.x86_64
cdparanoia-libs-10.2-17.el7.x86_64  mozilla-filesystem-1.9-11.el7.x86_6
gtkmm30-3.22.0-1.el7.x86_64         hypervkvpd-0-0.30.20161211git.el7.x
python-configshell-1.1.fb23-3.el7.n less-458-9.el7.x86_64
khmeros-base-fonts-5.0-17.el7.noarc libgsf-1.14.26-7.el7.x86_64
liberation-fonts-common-1.07.2-15.e mariadb-libs-5.5.56-2.el7.x86_64
kexec-tools-2.0.14-17.el7.x86_64    rootfiles-8.1-11.el7.noarch
iptables-1.4.21-18.0.1.el7.centos.x langtable-data-0.0.31-3.el7.noarch
gnome-dictionary-libs-3.20.0-1.el7. libcgroup-tools-0.41-13.el7.x86_64
gd-2.0.35-26.el7.x86_64             perl-Pod-Usage-1.63-3.el7.noarch
```

`--column` تعداد ستون‌های ایجاد شده در خروجی را تعیین می‌کند. `-l` طول صفحه را مشخص می‌کند (پیش‌فرض 66 خط است). طبق معمول، برای جزئیات به صفحه **man** مراجعه کنید.

## nl

nl یک دستور لینوکس برای شماره‌گذاری خطوط فایل‌ها است، فایل‌ها را در خروجی استاندارد کپی کرده و شماره خطوط را به ابتدای آن‌ها اضافه می‌کند.

```
nl [OPTION]... [FILE]...
```

```
[root@centos7-1 ~]# nl note.txt 
     1    hi 
     2    this is my note file.
     3    linux is an operating system.
```

`-n Format` از مقدار متغیر Format به عنوان فرمت شماره‌گذاری خط استفاده می‌کند. فرمت‌های شناخته شده عبارتند از:

* ln : چپ‌چین، حذف صفرهای ابتدایی
* rn : راست‌چین، حذف صفرهای ابتدایی (پیش‌فرض)
* rz: راست‌چین، نگه داشتن صفرهای ابتدایی

```
[root@centos7-1 ~]# nl -nln note.txt 
1         hi 
2         this is my note file.
3         linux is an operating system.

[root@centos7-1 ~]# nl -nrn note.txt       #default
     1    hi 
     2    this is my note file.
     3    linux is an operating system.

[root@centos7-1 ~]# nl -nrz note.txt 
000001    hi 
000002    this is my note file.
000003    linux is an operating system.
```

> به طور پیش‌فرض nl از روی خطوط خالی می‌پرد و به آن‌ها شماره نمی‌دهد، از سوئیچ -ba برای اختصاص شماره به آن‌ها استفاده کنید.

سایر گزینه‌های nl:

```
  -b, --body-numbering=STYLE      use STYLE for numbering body lines
  -d, --section-delimiter=CC      use CC for separating logical pages
  -f, --footer-numbering=STYLE    use STYLE for numbering footer lines
  -h, --header-numbering=STYLE    use STYLE for numbering header lines
  -i, --line-increment=NUMBER     line number increment at each line
  -l, --join-blank-lines=NUMBER   group of NUMBER empty lines counted as one
  -n, --number-format=FORMAT      insert line numbers according to FORMAT
  -p, --no-renumber               do not reset line numbers at logical pages
  -s, --number-separator=STRING   add STRING after (possible) line number
  -v, --starting-line-number=NUMBER  first line number on each logical page
  -w, --number-width=NUMBER       use NUMBER columns for line numbers
      --help     display this help and exit
      --version  output version information and exit
```

`cat -n filename` همان کاری را انجام می‌دهد که دستور `nl` انجام می‌دهد.

## fmt

fmt یک فرمت‌کننده متن ساده و بهینه است، پاراگراف‌ها را در فایل مشخص شده بازنویسی کرده و نتایج را در خروجی استاندارد چاپ می‌کند.

```
fmt [-WIDTH] [OPTION]... [FILE]...
```

```
[root@centos7-1 ~]# cat note.txt 
hi 
this is my note file.
linux is an operating system.
learn linux.

[root@centos7-1 ~]# fmt note.txt 
hi this is my note file.  linux is an operating system.  learn linux.
```

به طور پیش‌فرض fmt عرض ستون را روی 75 تنظیم می‌کند. این مقدار را می‌توان با گزینه -w , --width=WIDTH تغییر داد.

```
[root@centos7-1 ~]# fmt -w 12  note.txt 
hi this
is my
note file.
linux is an
operating
system.
learn
linux.
```

گزینه‌های دستور fmt:

```
  -c, --crown-margin        preserve indentation of first two lines
  -p, --prefix=STRING       reformat only lines beginning with STRING,
                              reattaching the prefix to reformatted lines
  -s, --split-only          split long lines, but do not refill
  -t, --tagged-paragraph    indentation of first line different from second
  -u, --uniform-spacing     one space between words, two after sentences
  -w, --width=WIDTH         maximum line width (default of 75 columns)
  -g, --goal=WIDTH          goal width (default of 93% of width)
      --help     display this help and exit
      --version  output version information and exit
```

## sort and uniq

**Sort** یک برنامه لینوکس است که برای چاپ خطوط فایل‌های متنی ورودی و اتصال تمام فایل‌ها به ترتیب مرتب شده استفاده می‌شود. دستور sort از فضای خالی به عنوان جداکننده فیلد و از کل فایل ورودی به عنوان کلید مرتب‌سازی استفاده می‌کند. مهم است توجه داشته باشید که دستور sort واقعاً فایل‌ها را مرتب نمی‌کند بلکه فقط خروجی مرتب شده را چاپ می‌کند، مگر اینکه خروجی را تغییر مسیر دهید.

```
sort [OPTION]... [FILE]...
```

```
[root@centos7-1 ~]# cat 1.txt 
D 1
d 1
c 2
C 2
A 3
B 4
f 14
[root@centos7-1 ~]# sort 1.txt 
A 3
B 4
c 2
C 2
d 1
D 1
f 14
```

اگر فایلی دارای کلمات/خطوطی باشد که با هر دو کاراکتر بزرگ و کوچک شروع می‌شوند، sort آن‌هایی را که با حروف بزرگ هستند در بالا نمایش می‌دهد. با این حال، می‌توانیم این رفتار را با استفاده از گزینه خط فرمان -f تغییر دهیم:

```
[root@centos7-1 ~]# sort -f 1.txt 
A 3
B 4
C 2
c 2
D 1
d 1
f 14
```

گزینه `-n` محتویات را به صورت عددی مرتب می‌کند. همچنین می‌توانیم یک فایل را بر اساس ستون **"n"** با گزینه `-k`n مرتب کنیم:

```
[root@centos7-1 ~]# sort  -n -k2 1.txt 
d 1
D 1
c 2
C 2
A 3
B 4
f 14
```

از `-r` برای معکوس کردن نتیجه مقایسه‌ها استفاده کنید. سایر گزینه‌های دستور sort:

```
  -b, --ignore-leading-blanks  ignore leading blanks
  -d, --dictionary-order      consider only blanks and alphanumeric characters
  -f, --ignore-case           fold lower case to upper case characters
  -g, --general-numeric-sort  compare according to general numerical value
  -i, --ignore-nonprinting    consider only printable characters
  -M, --month-sort            compare (unknown) < 'JAN' < ... < 'DEC'
  -h, --human-numeric-sort    compare human readable numbers (e.g., 2K 1G)
  -n, --numeric-sort          compare according to string numerical value
  -R, --random-sort           sort by random hash of keys
      --random-source=FILE    get random bytes from FILE
  -r, --reverse               reverse the result of comparisons
      --sort=WORD             sort according to WORD:
                                general-numeric -g, human-numeric -h, month -M,
                                numeric -n, random -R, version -V
  -V, --version-sort          natural sort of (version) numbers within text
```

Sort می‌تواند محتویات دو فایل را در خروجی استاندارد در یک مرحله مرتب کند! `sort 1.txt 2.txt`

دستور **uniq** برای گزارش یا حذف خطوط تکراری استفاده می‌شود، خطوط را از ورودی استاندارد فیلتر کرده و نتیجه را در خروجی استاندارد می‌نویسد.

```
[root@centos7-1 ~]# cat assets.txt 
motherboard
motherboard
cpu
cpu
ram
ram
ram
ram
monitor
monitor
hdd
ssd
mouse
keyboard
keyboard
[root@centos7-1 ~]# uniq assets.txt 
motherboard
cpu
ram
monitor
hdd
ssd
mouse
keyboard
```

از -c برای نمایش تعداد تکرارها برای هر خط استفاده کنید:

```
[root@centos7-1 ~]# uniq -c assets.txt 
      2 motherboard
      2 cpu
      4 ram
      2 monitor
      1 hdd
      1 ssd
      1 mouse
      2 keyboard
```

گزینه `-d` فقط خطوط تکراری را نمایش می‌دهد و برعکس `-u` فقط خطوط منحصر به فرد را نشان می‌دهد:

```
[root@centos7-1 ~]# uniq -d assets.txt 
motherboard
cpu
ram
monitor
keyboard
[root@centos7-1 ~]# uniq -u assets.txt 
hdd
ssd
mouse
```

برای دیدن تمام خطوط تکراری `-D` را امتحان کنید. سایر گزینه‌ها از uniq --help:

```
  -c, --count           prefix lines by the number of occurrences
  -d, --repeated        only print duplicate lines, one for each group
  -D, --all-repeated[=METHOD]  print all duplicate lines
                          groups can be delimited with an empty line
                          METHOD={none(default),prepend,separate}
  -f, --skip-fields=N   avoid comparing the first N fields
      --group[=METHOD]  show all items, separating groups with an empty line
                          METHOD={separate(default),prepend,append,both}
  -i, --ignore-case     ignore differences in case when comparing
  -s, --skip-chars=N    avoid comparing the first N characters
  -u, --unique          only print unique lines
  -z, --zero-terminated  end lines with 0 byte, not newline
  -w, --check-chars=N   compare no more than N characters in lines
      --help     display this help and exit
      --version  output version information and exit
```

## split

دستور split برای تقسیم یا شکستن یک فایل به قطعات استفاده می‌شود.

```
 split [options] filename prefix
```

* filename را با نام فایل بزرگی که می‌خواهید تقسیم کنید جایگزین کنید.
* prefix را با نامی که می‌خواهید به فایل‌های خروجی کوچک بدهید جایگزین کنید.
* ما می‌توانیم [options] را حذف کنیم یا آن را با یکی از موارد زیر جایگزین کنیم:
* `-l linenumber`
* `-b bytes`

اگر از گزینه -l (L کوچک) استفاده کنیم، line number را با تعداد خطوطی که می‌خواهیم در هر یک از فایل‌های کوچکتر باشد جایگزین می‌کنیم (پیش‌فرض 1,000 است).

```
[root@centos7-1 split]# ls
my7lines.txt
[root@centos7-1 split]# cat my7lines.txt 
this is 1st line.
this is 2nd line.
this is 3rd line.
this is 4th line
this is 5th line.
this is 6th line.
this is 7th line.

[root@centos7-1 split]# split -l 2 my7lines.txt 

[root@centos7-1 split]# ls
my7lines.txt  xaa  xab  xac  xad

[root@centos7-1 split]# echo "xaa";cat xaa;echo "xab";cat  xab;echo "xac";cat xac;echo "xad"; cat  xad
xaa
this is 1st line.
this is 2nd line.
xab
this is 3rd line.
this is 4th line
xac
this is 5th line.
this is 6th line.
xad
this is 7th line.
```

دستور split به هر فایل خروجی که ایجاد می‌کند نام prefix را به همراه پسوندی در انتها می‌دهد که ترتیب آن را نشان می‌دهد. به طور پیش‌فرض، دستور split عبارت aa را به اولین فایل خروجی اضافه می‌کند و از طریق الفبا تا zz برای فایل‌های بعدی پیش می‌رود. اگر یک پیشوند (prefix) مشخص نکنید، اکثر سیستم‌ها از x استفاده می‌کنند.

اگر از گزینه -b استفاده کنیم، bytes را با تعداد بایت‌هایی که می‌خواهید در هر یک از فایل‌های کوچکتر باشد جایگزین کنید.

```
[root@centos7-1 split2]# du -ah
51M    ./dsl-4.11.rc2.iso
51M    .

[root@centos7-1 split2]# split -b 10MB dsl-4.11.rc2.iso 
[root@centos7-1 split2]# ls 
dsl-4.11.rc2.iso  xaa  xab  xac  xad  xae  xaf
[root@centos7-1 split2]# du -ah
51M    ./dsl-4.11.rc2.iso
9.6M    ./xaa
9.6M    ./xab
9.6M    ./xac
9.6M    ./xad
9.6M    ./xae
2.7M    ./xaf
101M    .
```

برخی گزینه‌های دیگر عبارتند از:

```
  -a, --suffix-length=N   generate suffixes of length N (default 2)
      --additional-suffix=SUFFIX  append an additional SUFFIX to file names
  -b, --bytes=SIZE        put SIZE bytes per output file
  -C, --line-bytes=SIZE   put at most SIZE bytes of lines per output file
  -d, --numeric-suffixes[=FROM]  use numeric suffixes instead of alphabetic;
                                   FROM changes the start value (default 0)
  -e, --elide-empty-files  do not generate empty output files with '-n'
      --filter=COMMAND    write to shell COMMAND; file name is $FILE
  -l, --lines=NUMBER      put NUMBER lines per output file
  -n, --number=CHUNKS     generate CHUNKS output files; see explanation below
  -u, --unbuffered        immediately copy input to output with '-n r/...'
      --verbose           print a diagnostic just before each
                            output file is opened
      --help     display this help and exit
      --version  output version information and exit

SIZE is an integer and optional unit (example: 10M is 10*1024*1024).  Units
are K, M, G, T, P, E, Z, Y (powers of 1024) or KB, MB, ... (powers of 1000).
```

برای متصل کردن فایل‌های تقسیم شده از `cat x* > orginalfile` استفاده کنید.

## wc

دستور wc (مخفف word count) برای پیدا کردن تعداد خطوط جدید، تعداد کلمات، بایت‌ها و کاراکترها در یک فایل استفاده می‌شود.

```
wc [options] filenames
```

یک مثال پایه از دستور WC:

```
[root@centos7-1 ~]# wc /etc/inittab 
 17  80 511 /etc/inittab
```

سه عدد نشان داده شده در زیر عبارتند از **17** (تعداد **خطوط**)، **80** (تعداد **کلمات** _[به طور پیش‌فرض با فاصله جدا شده‌اند]_) و **511** (تعداد **بایت‌ها**) فایل.

options:

```
  -c, --bytes            print the byte counts
  -m, --chars            print the character counts
  -l, --lines            print the newline counts
      --files0-from=F    read input from the files specified by
                           NUL-terminated names in file F;
                           If F is - then read names from standard input
  -L, --max-line-length  print the length of the longest line
  -w, --words            print the word counts
      --help     display this help and exit
      --version  output version information and exit
```

## دستورات head و tail  commands:

## ![](.gitbook/assets/processtxt-headtail.jpg)

## head

دستور head ده خط اول هر فایل داده شده را می‌خواند.

```
head [options] [file(s)]
```

به عنوان مثال بیایید به فایل /var/log/yum.log نگاهی بیندازیم:

```
[root@centos7-1 ~]# head /var/log/yum.log 
Aug 26 04:48:25 Updated: openldap-2.4.44-15.el7_5.x86_64
Aug 26 04:48:25 Installed: openldap-clients-2.4.44-15.el7_5.x86_64
Aug 26 04:48:27 Installed: openldap-servers-2.4.44-15.el7_5.x86_64
Oct 13 03:38:41 Installed: perl-Data-Dumper-2.145-3.el7.x86_64
Oct 13 03:38:41 Installed: perl-Net-Daemon-0.48-5.el7.noarch
Oct 13 03:38:41 Installed: perl-Digest-1.17-245.el7.noarch
Oct 13 03:38:41 Installed: perl-Digest-MD5-2.52-3.el7.x86_64
Oct 13 03:38:41 Installed: 7:squid-migration-script-3.5.20-12.el7.x86_64
Oct 13 03:38:41 Installed: 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64
Oct 13 03:38:42 Installed: libecap-1.0.0-1.el7.x86_64
```

برای بازیابی تعداد دلخواه خطوط از گزینه‌های -n\<number> یا ساده -\<number> استفاده کنید:

```
[root@centos7-1 ~]# head -n 2 /var/log/yum.log 
Aug 26 04:48:25 Updated: openldap-2.4.44-15.el7_5.x86_64
Aug 26 04:48:25 Installed: openldap-clients-2.4.44-15.el7_5.x86_64
[root@centos7-1 ~]# head -2 /var/log/yum.log 
Aug 26 04:48:25 Updated: openldap-2.4.44-15.el7_5.x86_64
Aug 26 04:48:25 Installed: openldap-clients-2.4.44-15.el7_5.x86_64
```

گزینه‌ها از`head --help` :

```
  -c, --bytes=[-]K         print the first K bytes of each file;
                             with the leading '-', print all but the last
                             K bytes of each file
  -n, --lines=[-]K         print the first K lines instead of the first 10;
                             with the leading '-', print all but the last
                             K lines of each file
  -q, --quiet, --silent    never print headers giving file names
  -v, --verbose            always print headers giving file names
      --help     display this help and exit
      --version  output version information and exit

K may have a multiplier suffix:
b 512, kB 1000, K 1024, MB 1000*1000, M 1024*1024,
GB 1000*1000*1000, G 1024*1024*1024, and so on for T, P, E, Z, Y.
```

## tail

دستور tail ده خط آخر هر فایل متنی را نمایش می‌دهد.

```
tail [options] [filenames]
```

مشابه دستور head در بالا، دستور tail نیز از گزینه‌های -n برای تعداد خطوط و n برای تعداد کاراکترها پشتیبانی می‌کند.

```
[root@centos7-1 ~]# tail -n 5 /var/log/yum.log 
Dec 05 10:02:37 Updated: firefox-60.3.0-1.el7.centos.x86_64
Dec 05 10:02:37 Updated: nss-tools-3.36.0-7.el7_5.x86_64
Dec 05 10:02:37 Updated: 1:dbus-x11-1.10.24-7.el7.x86_64
Dec 08 11:54:14 Installed: zip-3.0-11.el7.x86_64
Dec 08 13:10:52 Installed: vsftpd-3.0.2-22.el7.x86_64
```

گزینه `-f` باعث می‌شود tail در یک حلقه همیشگی بماند و داده‌های جدید را در انتهای فایل(ها) بررسی کند. وقتی داده‌های جدید ظاهر می‌شوند، چاپ می‌شوند. این گزینه برای فایل‌های لاگ عالی عمل می‌کند و به ما اجازه می‌دهد ببینیم چه اتفاقی در حال رخ دادن است:

```
[root@centos7-1 ~]# tail -f /var/log/dmesg
[   19.126805] AES CTR mode by8 optimization enabled
[   19.129902] ppdev: user-space parallel port driver
[   19.155269] alg: No test for __gcm-aes-aesni (__driver-gcm-aes-aesni)
[   19.160414] Adding 4063228k swap on /dev/mapper/centos-swap.  Priority:-1 extents:1 across:4063228k FS
[   19.176446] alg: No test for crc32 (crc32-pclmul)
[   19.220329] XFS (sda1): Mounting V5 Filesystem
[   19.288818] XFS (sda1): Ending clean mount
[   21.853831] floppy0: no floppy controllers found
[   21.854029] work still pending
[   22.082648] type=1305 audit(1543863215.542:3): audit_pid=681 old=0 auid=4294967295 ses=4294967295 res=1
```

options:

```
  -c, --bytes=K            output the last K bytes; or use -c +K to output
                             bytes starting with the Kth of each file
  -f, --follow[={name|descriptor}]
                           output appended data as the file grows;
                             an absent option argument means 'descriptor'
  -F                       same as --follow=name --retry
  -n, --lines=K            output the last K lines, instead of the last 10;
                             or use -n +K to output starting with the Kth
      --max-unchanged-stats=N
                           with --follow=name, reopen a FILE which has not
                             changed size after N (default 5) iterations
                             to see if it has been unlinked or renamed
                             (this is the usual case of rotated log files);
                             with inotify, this option is rarely useful
      --pid=PID            with -f, terminate after process ID, PID dies
  -q, --quiet, --silent    never output headers giving file names
      --retry              keep trying to open a file if it is inaccessible
  -s, --sleep-interval=N   with -f, sleep for approximately N seconds
                             (default 1.0) between iterations;
                             with inotify and --pid=P, check process P at
                             least once every N seconds
  -v, --verbose            always output headers giving file names
      --help     display this help and exit
      --version  output version information and exit
```

## less

دستور less به شما اجازه می‌دهد محتویات یک فایل را مشاهده کرده و در فایل پیمایش کنید.

```
[root@centos7-1 ~]# dmesg |less
```

```
[    0.000000] BIOS-e820: [mem 0x00000000bfeff000-0x00000000bfefffff] ACPI NVS
[    0.000000] BIOS-e820: [mem 0x00000000bff00000-0x00000000bfffffff] usable
[    0.000000] BIOS-e820: [mem 0x00000000f0000000-0x00000000f7ffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fec00000-0x00000000fec0ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fee00000-0x00000000fee00fff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fffe0000-0x00000000ffffffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000100000000-0x000000013fffffff] usable
:
```

به طور پیش‌فرض تنها راه خروج از دستور less فشردن کلید q است. برای تغییر این رفتار و خروج خودکار از فایل هنگام رسیدن به انتهای فایل، از گزینه `-e` یا `-E` استفاده کنید. `less -e /var/log/auth.log` یا `less -E /var/log/auth.log`

* برای باز کردن یک فایل در اولین وقوع یک الگو از سینتکس زیر استفاده کنید:

`less +/sshd /var/log/auth.log`

* برای اضافه کردن خودکار محتوای فایلی که در دستور less باز شده است، از ترکیب کلیدهای Shift+f استفاده کنید یا less را با سینتکس زیر اجرا کنید:

`less +F /var/log/messages`

این کار باعث می‌شود less در حالت تعاملی (زنده) اجرا شود و محتوای جدید را در حین نوشتن داده‌های جدید در فایل نمایش دهد. این رفتار مشابه دستور tail -f است. برای خروج از حالت زنده کافی است کلیدهای `Ctrl+c` را فشار دهید.

**نکته**: در ترکیب با یک الگو، می‌توانید فایل لاگ را به صورت تعاملی با فشردن کلید `Shift+f` در حین مطابقت با یک کلمه کلیدی مشاهده کنید.

> **less در مقابل more**
>
> دستور less مشابه more است، تفاوت اصلی بین more و less این است که دستور less سریع‌تر است زیرا کل فایل را به یکباره بارگذاری نمی‌کند و اجازه پیمایش در فایل را با استفاده از کلیدهای page up/down می‌دهد.
>
> چه تصمیم بگیرید از more استفاده کنید و چه از less، که یک انتخاب شخصی است، به یاد داشته باشید که less با ویژگی‌های بیشتر، برتر است (less is more with more features).

## cut

دستور cut در UNIX یک ابزار خط فرمان برای بریدن بخش‌هایی از هر خط از فایل‌ها و نوشتن نتیجه در خروجی استاندارد است. این دستور می‌تواند برای بریدن بخش‌هایی از یک خط بر اساس **موقعیت** بایت، **کاراکتر** و **جداکننده** (delimiter) استفاده شود.

```
cut OPTION... [FILE]...
```

**بریدن بر اساس موقعیت بایت:**

```
[root@centos7-1 ~]# echo "linux" | cut -b 1
l
[root@centos7-1 ~]# echo "linux" | cut -b 1,5
lx
[root@centos7-1 ~]# echo "linux" | cut -b 1-4
linu
```

**بریدن بر اساس کاراکتر:**

```
[root@centos7-1 ~]# echo '♣foobar' | cut -c 1,7
♣r
[root@centos7-1 ~]# echo '♣foobar' | cut -c 5-7
bar
```

**بریدن بر اساس یک جداکننده (delimiter):**

![](.gitbook/assets/processtxt-cut.jpg)

برای بریدن با استفاده از یک جداکننده از گزینه -d استفاده کنید. این گزینه معمولاً در ترکیب با گزینه -f برای مشخص کردن فیلدی که باید بریده شود استفاده می‌شود. مثال‌ها:

```
[root@centos7-1 ~]# cut 1.txt -d: -f1
1
2
3
4
[root@centos7-1 ~]# cut 1.txt -d: -f2
a,w
b,x
c,y
d,z
[root@centos7-1 ~]# cut 1.txt -d, -f1
1:a
2:b
3:c
4:d
[root@centos7-1 ~]# cut 1.txt -d, -f2
w
x
y
z
```

cut گزینه‌های زیادی دارد:

```
  -b, --bytes=LIST        select only these bytes
  -c, --characters=LIST   select only these characters
  -d, --delimiter=DELIM   use DELIM instead of TAB for field delimiter
  -f, --fields=LIST       select only these fields;  also print any line
                            that contains no delimiter character, unless
                            the -s option is specified
  -n                      with -b: don't split multibyte characters
      --complement        complement the set of selected bytes, characters
                            or fields
  -s, --only-delimited    do not print lines not containing delimiters
      --output-delimiter=STRING  use STRING as the output delimiter
                            the default is to use the input delimiter
      --help     display this help and exit
      --version  output version information and exit
```

## paste

دستور paste خطوط متناظر چندین فایل را در کنار هم نمایش می‌دهد.

```
paste [OPTION]... [FILE]...
```

![](.gitbook/assets/processtxt-paste.jpg)

```
[root@centos7-1 ~]# paste 1.txt 2.txt 
a    e
b    f
c    g
d    h
```

paste خطوطی را می‌نویسد که شامل خطوط متناظر متوالی از هر فایل است که با تب از هم جدا شده‌اند. برای اعمال دونقطه (:) به عنوان کاراکتر جداکننده به جای تب، از گزینه -d استفاده کنید:

```
[root@centos7-1 ~]# paste -d: 1.txt 2.txt 
a:e
b:f
c:g
d:h
```

گزینه‌های دستور paste:

```
  -d, --delimiters=LIST   reuse characters from LIST instead of TABs
  -s, --serial            paste one file at a time instead of in parallel
      --help     display this help and exit
      --version  output version information and exit
```

## join

خطوط دو فایل را که دارای یک فیلد داده مشترک هستند، به هم متصل می‌کند.

> هنگام **استفاده از `join`**، فایل‌های **ورودی باید فقط بر اساس فیلد اتصال مرتب شده باشند**، در غیر این صورت ممکن است هشدار مشاهده کنید.

```
join [OPTION]... FILE1 FILE2
```

![](.gitbook/assets/processtxt-join.jpg)

```
[root@centos7-1 ~]# join 1.txt 2.txt 
1 a w
4 d z
5 e q
```

به طور پیش‌فرض، دستور join فقط خطوط قابل جفت شدن (pairable) را چاپ می‌کند. خطوط جفت نشده در خروجی حذف می‌شوند. با این حال، اگر بخواهیم، می‌توانیم با استفاده از گزینه خط فرمان -a همچنان آن‌ها را در خروجی داشته باشیم. این گزینه از شما می‌خواهد شماره فایل را بدهید تا ابزار بداند در مورد کدام فایل صحبت می‌کنید.

```
[root@centos7-1 ~]# join 1.txt 2.txt -a 1
1 a w
2 b
4 d z
5 e q
[root@centos7-1 ~]# join 1.txt 2.txt -a 2
1 a w
3 y
4 d z
5 e q
```

برای چاپ خطوط جفت نشده (یعنی حذف خطوط جفت شده در خروجی)، از گزینه خط فرمان -v استفاده کنید. این گزینه دقیقاً همانطور که -a کار می‌کند عمل می‌کند.

```
[root@centos7-1 ~]# join 1.txt 2.txt -v 1
2 b
[root@centos7-1 ~]# join 1.txt 2.txt -v 2
3 y
```

join خطوط فایل‌ها را بر اساس یک فیلد مشترک ترکیب می‌کند که به طور پیش‌فرض اولین فیلد است. با این حال، اگر بخواهیم، می‌توانیم فیلد متفاوتی را برای هر فایل با استفاده از گزینه‌های خط فرمان -1 و -2 مشخص کنیم. به عنوان مثال `join -1 2 -2 2 file1 file2` از ستون دوم هر خط استفاده می‌کند. گزینه‌های دستور join:

```
  -a FILENUM        also print unpairable lines from file FILENUM, where
                      FILENUM is 1 or 2, corresponding to FILE1 or FILE2
  -e EMPTY          replace missing input fields with EMPTY
  -i, --ignore-case  ignore differences in case when comparing fields
  -j FIELD          equivalent to '-1 FIELD -2 FIELD'
  -o FORMAT         obey FORMAT while constructing output line
  -t CHAR           use CHAR as input and output field separator
  -v FILENUM        like -a FILENUM, but suppress joined output lines
  -1 FIELD          join on this FIELD of file 1
  -2 FIELD          join on this FIELD of file 2
  --check-order     check that the input is correctly sorted, even
                      if all input lines are pairable
  --nocheck-order   do not check that the input is correctly sorted
  --header          treat the first line in each file as field headers,
                      print them without trying to pair them
  -z, --zero-terminated     end lines with 0 byte, not newline
      --help     display this help and exit
      --version  output version information and exit
```

## sed

نام **Sed** کوتاه شده **stream editor** است.
یک ویرایشگر جریان (stream editor) برای انجام تبدیل‌های متنی اساسی روی یک جریان ورودی (یک فایل یا ورودی از یک پایپ) استفاده می‌شود. sed از regular expressions استفاده می‌کند و اساسی‌ترین (و محبوب‌ترین) کاربرد sed جایگزینی کاراکترها است.

به عنوان مثال بیایید 'l' را با "L" در یک فایل متنی نمونه جایگزین کنیم:

```
[root@centos7-1 ~]# cat sample.txt 
there are different operating systems in our planet.
one of them is linux.
almost six hundred linux distributions exist.

[root@centos7-1 ~]# sed 's/l/L/' sample.txt 
there are different operating systems in our pLanet.
one of them is Linux.
aLmost six hundred linux distributions exist.
```

به طور پیش‌فرض sed جایگزینی را فقط یک بار برای اولین وقوع عبارت انجام می‌دهد، از پرچم -g برای انجام جایگزینی برای تمام وقوع‌های عبارت در هر خط فایل استفاده کنید.

![](.gitbook/assets/processtxt-seds.jpg)

```
[root@centos7-1 ~]# sed 's/l/L/g' sample.txt 
there are different operating systems in our pLanet.
one of them is Linux.
aLmost six hundred Linux distributions exist.
```

علاوه بر این، می‌توانیم از gi به جای g استفاده کنیم تا حالت حروف (بزرگ و کوچک) نادیده گرفته شود:

```
[root@centos7-1 ~]# sed 's/linux/LINUX/gi' sample.txt 
there are different operating systems in our planet.
one of them is LINUX.
almost six hundred LINUX distributions exist.
```

مثال دیگر جایگزینی فضاهای خالی با تب است:

```
[root@centos7-1 ~]# cat sample.txt 
there are different operating systems in our planet.
one of them is linux.
almost six hundred linux distributions exist.

[root@centos7-1 ~]# sed 's/ /\t/g' sample.txt  | cat 
there    are    different    operating    systems    in    our    planet.
one    of    them    is    linux.
almost    six    hundred    linux    distributions    exist.
```

sed بسیار قدرتمند است و کارهایی که می‌تواند انجام دهد فقط به تخیل شما محدود می‌شود.

.

.

.

sources:

[https://developer.ibm.com/tutorials/l-lpic1-103-2/](https://developer.ibm.com/tutorials/l-lpic1-103-2/)

[https://ryanstutorials.net/linuxtutorial/piping.php#piping](https://ryanstutorials.net/linuxtutorial/piping.php#piping)

[https://www.tecmint.com/linux-io-input-output-redirection-operators/](https://www.tecmint.com/linux-io-input-output-redirection-operators/)

[https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection](https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection)

[https://www.tecmint.com/13-basic-cat-command-examples-in-linux/](https://www.tecmint.com/13-basic-cat-command-examples-in-linux/)

[https://kb.iu.edu/d/afar](https://kb.iu.edu/d/afar)

[https://www.tecmint.com/wc-command-examples/](https://www.tecmint.com/wc-command-examples/)

[https://www.tecmint.com/view-contents-of-file-in-linux/](https://www.tecmint.com/view-contents-of-file-in-linux/)

[http://landoflinux.com/linux_expand_unexpand_command.html](http://landoflinux.com/linux_expand_unexpand_command.html)

[https://www.geeksforgeeks.org/expand-command-in-linux-with-examples/](https://www.geeksforgeeks.org/expand-command-in-linux-with-examples/)

[https://www.thegeekstuff.com/2012/12/linux-tr-command](https://www.thegeekstuff.com/2012/12/linux-tr-command)

[https://www.howtoforge.com/linux-uniq-command/](https://www.howtoforge.com/linux-uniq-command/)

[https://www.tecmint.com/linux-file-operations-commands/](https://www.tecmint.com/linux-file-operations-commands/)

[https://www.tecmint.com/20-advanced-commands-for-linux-experts/](https://www.tecmint.com/20-advanced-commands-for-linux-experts/)

[https://www.ibm.com/support/knowledgecenter/en/ssw_aix\_72/com.ibm.aix.cmds4/nl.htm](https://www.ibm.com/support/knowledgecenter/en/ssw_aix\_72/com.ibm.aix.cmds4/nl.htm)

[https://shapeshed.com/unix-fmt/](https://shapeshed.com/unix-fmt/)

[https://www.tecmint.com/linux-more-command-and-less-command-examples/](https://www.tecmint.com/linux-more-command-and-less-command-examples/)

[https://www.tecmint.com/linux-file-operations-commands/](https://www.tecmint.com/linux-file-operations-commands/)

[https://shapeshed.com/unix-cut/](https://shapeshed.com/unix-cut/)

[https://www.computerhope.com/unix/upaste.htm](https://www.computerhope.com/unix/upaste.htm)

[https://www.howtoforge.com/tutorial/linux-join-command/](https://www.howtoforge.com/tutorial/linux-join-command/)

[https://www.tecmint.com/sed-command-to-create-edit-and-manipulate-files-in-linux/](https://www.tecmint.com/sed-command-to-create-edit-and-manipulate-files-in-linux/)

[https://www.tecmint.com/linux-sed-command-tips-tricks/](https://www.tecmint.com/linux-sed-command-tips-tricks/)
