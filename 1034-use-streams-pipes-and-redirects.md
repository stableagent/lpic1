# 103.4. استفاده از streams، pipes و redirects

**وزن:** 4

**توضیحات:** داوطلبان باید قادر به هدایت جریان‌ها (redirecting streams) و اتصال آن‌ها به منظور پردازش بهینه داده‌های متنی باشند. وظایف شامل تغییر مسیر ورودی استاندارد، خروجی استاندارد و خطای استاندارد، پایپ کردن خروجی یک دستور به ورودی دستور دیگر، استفاده از خروجی یک دستور به عنوان آرگومان برای دستور دیگر و ارسال خروجی به هر دو خروجی استاندارد (stdout) و یک فایل است.

**حوزه‌های کلیدی دانش:**

* تغییر مسیر ورودی استاندارد، خروجی استاندارد و خطای استاندارد
* پایپ کردن خروجی یک دستور به ورودی دستور دیگر
* استفاده از خروجی یک دستور به عنوان آرگومان برای دستور دیگر
* ارسال خروجی به هر دو stdout و یک فایل

**اصطلاحات و ابزارهای کاربردی:**

* tee
* xargs

ما در بخش‌های قبلی درباره مبانی پایپینگ و تغییر مسیر صحبت کرده‌ایم و در این آموزش نگاه دقیق‌تری به تکنیک‌های لینوکس برای تغییر مسیر جریان‌های ورودی/خروجی (IO) استاندارد می‌اندازیم.

### ورودی، خروجی و جریان‌ها (Input, output, and streams) <a href="input-output-and-streams" id="input-output-and-streams"></a>

در محاسبات، یک جریان (stream) چیزی است که می‌تواند داده‌ها را منتقل کند. جریان‌های داده، مانند جریان‌های آب، دو سر دارند. آن‌ها یک منبع و یک خروجی دارند. 

یک شل لینوکس، مانند Bash، ورودی را دریافت می‌کند و خروجی را به صورت توالی‌ها یا جریان‌هایی از کاراکترها ارسال می‌کند. جریان کاراکترها می‌تواند از یک فایل، صفحه‌کلید، یک پنجره روی نمایشگر یا یک device دیگر IO بیاید یا به آن برود.

stdin، stdout و stderr سه جریان استاندارد هستند که هنگام اجرای یک دستور در Linux ایجاد می‌شوند.

![](.gitbook/assets/usestreams-streams.jpg)

شل‌های لینوکس از این سه جریان استاندارد ورودی/خروجی استفاده می‌کنند که هر کدام با یک **توصیف‌گر فایل (file descriptor)** مرتبط هستند:

* **stdout** جریان خروجی استاندارد است که خروجی دستورات را نمایش می‌دهد (**file descriptor 1**)
* **stderr** جریان خطای استاندارد است که خروجی خطا از دستورات را نمایش می‌دهد (**file descriptor 2**)
* **stdin** جریان ورودی استاندارد است که ورودی دستورات را فراهم می‌کند (**file descriptor 0**)

بنابراین می‌بینیم که دو جریان خروجی، `stdout` و `stderr` و یک جریان ورودی، `stdin` وجود دارد.

**توصیف‌گرهای فایل (file descriptors) چیستند؟**

جریان‌ها مانند فایل‌ها مدیریت می‌شوند. ما می‌توانیم متن را از یک فایل بخوانیم و می‌توانیم متن را در یک فایل بنویسیم. هر دوی این اقدامات شامل یک جریان داده است. بنابراین مفهوم مدیریت یک جریان داده به عنوان یک فایل چندان دور از ذهن نیست.

هر فایلی که به یک process مرتبط باشد، یک شماره یکتا برای شناسایی دریافت می‌کند.

```
STDIN - /proc/<processID>/fd/0
STDOUT - /proc/<processID>/fd/1
STDERR - /proc/<processID>/fd/2
```

این به عنوان **توصیف‌گر فایل** شناخته می‌شود. هرگاه نیاز به انجام اقدامی روی یک فایل باشد، از توصیف‌گر فایل برای شناسایی فایل استفاده می‌شود. این مقادیر همیشه برای `stdin`, `stdout` و `stderr` استفاده می‌شوند:

* 0: stdin 
* 1: stdout 
* 2: stderr

سیستم برای راحتی بیشتر، چند میانبر برای ما ایجاد می‌کند:

```
 root@ubuntu16-1:~/test-space/myfiles# ls -al /dev/std*
lrwxrwxrwx 1 root root 15 Dec  1  2018 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx 1 root root 15 Dec  1  2018 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx 1 root root 15 Dec  1  2018 /dev/stdout -> /proc/self/fd/1
```

### تغییر مسیر ورودی/خروجی استاندارد (Redirecting standard IO) <a href="redirecting-standard-io" id="redirecting-standard-io"></a>

اگرچه مدل ورودی و خروجی استاندارد یک جریان سریال از کاراکترها به و از ترمینال است، ممکن است بخواهیم داده‌های ورودی را در یک فایل آماده کنیم یا اطلاعات خروجی یا خطا را در یک فایل ذخیره کنیم. اینجاست که تغییر مسیر (redirection) وارد عمل می‌شود.

#### تغییر مسیر خروجی (Redirecting output) <a href="redirecting-output" id="redirecting-output"></a>

دو راه برای تغییر مسیر خروجی به یک فایل وجود دارد:

_**n**_**> ** خروجی را از توصیف‌گر فایل n به یک فایل هدایت می‌کند. شما باید اجازه نوشتن در فایل را داشته باشید. اگر فایل وجود نداشته باشد، ایجاد می‌شود. اگر وجود داشته باشد، محتویات موجود معمولاً بدون هیچ هشداری از بین می‌روند.

_**n**_**>> ** نیز خروجی را از توصیف‌گر فایل n به یک فایل هدایت می‌کند. باز هم، شما باید اجازه نوشتن در فایل را داشته باشید. اگر فایل وجود نداشته باشد، ایجاد می‌شود. اگر وجود داشته باشد، خروجی به انتهای فایل موجود اضافه می‌شود.

> حرف _n_ در n> یا n>> به _توصیف‌گر فایل_ اشاره دارد. اگر حذف شود، خروجی استاندارد (توصیف‌گر فایل 1) فرض می‌شود.

نمادهای تغییر مسیر ` >` و `>>` به طور پیش‌فرض با **stdout** کار می‌کنند. ما می‌توانیم از یکی از توصیف‌گرهای فایل عددی استفاده کنیم تا نشان دهیم کدام جریان خروجی استاندارد را می‌خواهید هدایت کنید.

* **>** - خروجی استاندارد (standard output)
* **2>** - خطای استاندارد (standard error)

">" خروجی stdout را هدایت می‌کند

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# ls -l > list
root@ubuntu16-1:~/test-space/myfiles# cat list 
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root       0 Oct  9 01:42 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
```

"2>" خروجی stderr را هدایت می‌کند

```
root@ubuntu16-1:~/test-space/myfiles# ls -l BlahBlah
ls: cannot access 'BlahBlah': No such file or directory
root@ubuntu16-1:~/test-space/myfiles# ls -l BlahBlah 2> error
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1152
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root      56 Oct  9 01:43 error
-rw-r--r-- 1 root root     267 Oct  9 01:42 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# cat error 
ls: cannot access 'BlahBlah': No such file or directory
```

**الحاق (Append)**

دستورات با براکت دوتایی محتویات موجود مقصد را پاک نمی‌کنند، بلکه به آن اضافه (append) می‌کنند.

* **>>** - خروجی استاندارد
* **2>>** - خطای استاندارد

```
root@ubuntu16-1:~/test-space/myfiles# date
Sat Oct 12 02:52:04 PDT 2019
root@ubuntu16-1:~/test-space/myfiles# date > mydate
root@ubuntu16-1:~/test-space/myfiles# cat mydate
Sat Oct 12 02:52:10 PDT 2019
root@ubuntu16-1:~/test-space/myfiles# date > mydate 
root@ubuntu16-1:~/test-space/myfiles# cat mydate 
Sat Oct 12 02:53:00 PDT 2019
root@ubuntu16-1:~/test-space/myfiles# date >> mydate 
root@ubuntu16-1:~/test-space/myfiles# cat mydate 
Sat Oct 12 02:53:00 PDT 2019
Sat Oct 12 02:53:23 PDT 2019
```

{% hint style="info" %}
 **clobber یا noclobber؟**

گفتیم که تغییر مسیر خروجی با استفاده از n> معمولاً فایل‌های موجود را بازنویسی می‌کند. می‌توانید این موضوع را با گزینه `noclobber` در دستور داخلی `set` کنترل کنید. از `set -C` برای فعال کردن noclobber استفاده کنید:

```
user1@ubuntu16-1:~$ set -o | grep noclobber
noclobber          off
user1@ubuntu16-1:~$ ls
Desktop    Downloads         Music     Public     Videos
Documents  examples.desktop  Pictures  Templates
user1@ubuntu16-1:~$ ls > list1
user1@ubuntu16-1:~$ set -C
user1@ubuntu16-1:~$ set -o | grep noclobber
noclobber          on
user1@ubuntu16-1:~$ ls > list1
bash: list1: cannot overwrite existing file
```

اگر تنظیم شده باشد، می‌توانیم با استفاده از `n>|` آن را نادیده بگیریم:

```
user1@ubuntu16-1:~$ ls >| list1
```

استفاده از `set +C` برای غیرفعال کردن آن.
{% endhint %}

**تغییر مسیر هر دو خروجی استاندارد و خطای استاندارد**

گاهی اوقات ممکن است بخواهیم هر دو خروجی استاندارد و خطای استاندارد را به یک فایل هدایت کنیم. این کار اغلب برای فرآیندهای خودکار یا کارهای پس‌زمینه انجام می‌شود تا بتوانیم خروجی را بعداً بررسی کنیم.

* Use** &>** or** &>>** to redirect both standard output and standard error to the same place**.**

```
root@ubuntu16-1:~/test-space/myfiles# ls blah &>result
root@ubuntu16-1:~/test-space/myfiles# cat result 
ls: cannot access 'blah': No such file or directory
root@ubuntu16-1:~/test-space/myfiles# ls blah2 &>>result
root@ubuntu16-1:~/test-space/myfiles# cat result 
ls: cannot access 'blah': No such file or directory
ls: cannot access 'blah2': No such file or directory
```

ما می‌توانیم به روش دیگری نیز این کار را انجام دهیم، توصیف‌گر فایل _n_ را هدایت کرده و سپس توصیف‌گر فایل _m_ را با استفاده از ساختار m>\&n یا m>>\&n به همان مکان هدایت کنید.

* **&1** و **&2** و **&0** به مکان فعلی **stdout**، **stderr** و **stdin** اشاره دارند.

```
root@ubuntu16-1:~/test-space/myfiles# ls blah3 > result 2>&1
root@ubuntu16-1:~/test-space/myfiles# cat result 
ls: cannot access 'blah3': No such file or directory
```

{% hint style="info" %}
 ترتیبی که خروجی‌ها هدایت می‌شوند مهم است. به عنوان مثال،
`command 2>&1 >output.txt`
 با دستور زیر یکسان نیست:
`command >output.txt 2>&1`

در حالت اول، stderr به مکان فعلی stdout هدایت می‌شود و سپس stdout به output.txt هدایت می‌شود، اما این تغییر مسیر دوم فقط روی stdout تاثیر می‌گذارد، نه stderr. در حالت دوم، stderr به مکان فعلی stdout هدایت می‌شود که همان output.txt است.
{% endhint %}

**تغییر مسیر به dev/null/**

در مواقع دیگر، ممکن است بخواهیم خروجی استاندارد یا خطای استاندارد را کاملاً نادیده بگیریم. برای این کار، جریان مربوطه را به فایل خالی /dev/null هدایت کنید.

/dev/null یک فایل خاص است که دستگاه نال (null device) نامیده می‌شود. همچنین به آن bit-bucket یا سیاهچاله (black-hole) نیز می‌گویند زیرا بلافاصله هر چیزی را که در آن نوشته شود دور می‌ریزد و هنگام خواندن فقط یک EOF (پایان فایل) برمی‌گرداند.

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1156
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root     170 Oct  9 02:25 error
-rw-r--r-- 1 root root      97 Oct  9 03:17 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r--r-- 1 root root      58 Oct 12 02:53 mydate
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# ls -l Blah 2>/dev/null
```

### تغییر مسیر ورودی (Redirecting input)

"<" ورودی stdin را هدایت می‌کند

```
root@ubuntu16-1:~/test-space/myfiles# cat list 
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root       0 Oct  9 01:42 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# wc < list 
  6  47 267
```

یا به سادگی `wc 0< list` همین کار را انجام می‌دهد.

### اسناد درجا (here documents)

یک سند درجا (here document یا heredoc) راهی برای وارد کردن متن به یک اسکریپت یا دستور بدون نیاز به خواندن آن از یک فایل جداگانه است.

```
Command << HeredocDelimiter
. . .
. . .
. . .
HeredocDelimiter
```

**<<** ورودی استاندارد را تغییر مسیر می‌دهد و دستور تا زمانی که به رشته "HeredocDelimiter" نرسد، ورودی را می‌گیرد.

**توجه:** نباید هیچ فضایی بین نماد << و رشته محدودکننده وجود داشته باشد. اگر هر کاراکتری بین رشته محدودکننده و نماد << وجود داشته باشد، سند درجا احتمالاً کار نخواهد کرد.

```
root@ubuntu16-1:~/test-space/myfiles# cat << EOF > /tmp/myfile.txt
> Theis line will be written to the file.
>     this line is 2nd line
> this is last line.
> EOF
root@ubuntu16-1:~/test-space/myfiles# cat /tmp/myfile.txt 
Theis line will be written to the file.
    this line is 2nd line
this is last line.
```

> یادآوری: "-" یک خط تیره (که به تنهایی استفاده می‌شود) عموماً نشان می‌دهد که ورودی به جای یک فایل نام‌گذاری شده، از `stdin` گرفته می‌شود. امتحان کنید: `cat - << EOF > interesting.txt`

### رشته درجا (here string)

**<<<** یک خط متن واحد را به stdin دستور هدایت می‌کند. به این کار رشته درجا (here-string) می‌گویند.

```
cmd <<< "string"
```

### **لوله‌ها (Pipes)**

ما از عملگر ` | ` (پایپ) بین دو دستور استفاده می‌کنیم تا خروجی استاندارد (stdout) دستور اول را به ورودی استاندارد (stdin) دستور دوم هدایت کنیم. **`command | command`**

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1156
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root     170 Oct  9 02:25 error
-rw-r--r-- 1 root root      97 Oct  9 03:17 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r--r-- 1 root root      58 Oct 12 02:53 mydate
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# ls -l | cut -f1 -d' '
total
drwxr-xr-x
drwxr-xr-x
-rw-r--r--
-rw-r--r--
-rw-r--r--
-rw-r--r--
-rw-r-----
root@ubuntu16-1:~/test-space/myfiles# ls -l | cut -f1 -d' ' | wc -l
8
```

برخی دستورات مانند grep ورودی را به عنوان پارامتر می‌پذیرند، اما برخی دستورات آرگومان‌ها را می‌پذیرند.

> پارامتر در مقابل آرگومان (Parameter vs Argument):
>
> * **پارامتر** متغیری است که در تعریف تابع می‌آید.
> *   **آرگومان** مقدار واقعی این متغیر است که به تابع پاس داده می‌شود.
>
>     > ```
>     >                  Parameters
>     >                    |     |
>     > function test ( param1 , param2 ){
>     >     return ( param1 + param2 );
>     >     }
>     > test (5, 6)
>     >       |  |
>     >      Arguments
>     > ```

### استفاده از خروجی به عنوان آرگومان (Using output as arguments) <a href="using-output-as-arguments" id="using-output-as-arguments"></a>

در مبحث خط لوله‌ها، یاد گرفتیم که چگونه خروجی یک دستور را بگیریم و از آن به عنوان ورودی برای دستور دیگر استفاده کنیم. اما اگر بخواهیم از خروجی یک دستور یا محتویات یک فایل به عنوان آرگومان برای یک دستور استفاده کنیم، خط لوله‌ها برای این کار کار نمی‌کنند.

سه روش متداول عبارتند از:

1. دستور `xargs`
2. دستور `find` با گزینه `-exec` (در بخش قبل)
3. جایگزینی دستور (command substitution) (بعداً مورد بحث قرار خواهد گرفت)

### xargs

**xargs** یک دستور یونیکس است که می‌تواند برای ساخت و اجرای دستورات از ورودی استاندارد استفاده شود.

```
xargs [options] [command]
```

> اگر هیچ دستوری مشخص نشود، xargs به طور پیش‌فرض دستور echo را اجرا می‌کند.

```
root@ubuntu16-1:~/test-space/myfiles# ls | xargs 
dir1 dir2 interesting.txt list myconf.txt mydate mylog.txt
```

راه‌های متعددی وجود دارد که در آن‌ها **xargs** در استفاده روزمره از خط فرمان مفید است.

![](.gitbook/assets/usestreams-streams.jpg)

ما می‌توانیم وقوع آرگومان‌ها را با سوئیچ `-I` جایگزین کنیم:

```
root@ubuntu16-1:~/test-space/myfiles# find . -type f | xargs -I FILE  echo this is my file FILE yes
this is my file ./dir1/myvideo.mp4 yes
this is my file ./interesting.txt yes
this is my file ./list yes
this is my file ./dir2/zabbix-cli-rpm-2.1.1-1.tar.gz yes
this is my file ./dir2/zabbix-cli-manual-1.7.0.pdf yes
this is my file ./mylog.txt yes
this is my file ./myconf.txt yes
this is my file ./mydate yes
```

`همچنین امکان اجرای چندین دستور با xargs وجود دارد`

cat a.txt | xargs -I % sh -c {command1; command2; ... }

```
root@ubuntu16-1:~/test-space# cat foo 
one
two
three

root@ubuntu16-1:~/test-space# cat foo | xargs -I % sh -c 'mkdir  %  ;echo  "directory % has been made" '
directory one has been made
directory two has been made
directory three has been made

root@ubuntu16-1:~/test-space# ls
foo  one  three  two
```

با گزینه `-L` ورودی بر اساس خط و نه بر اساس فضاهای خالی شکسته می‌شود. سایر گزینه‌ها:

```
xargs options :
-0 : input items are terminated by null character instead of white spaces
-a file : read items from file instead of standard input
-d,–delimiter = delim : input items are terminated by a special character
-E eof-str : set the end of file string to eof-str
-I replace-str : replace occurrences of replace-str in the initial arguments with names read from standard input
-L max-lines : use at-most max-lines non-blank input lines per command line.
-p : prompt the user about whether to run each command line and read a line from terminal.
-r : If the standard input does not contain any nonblanks, do not run the command
-x : exit if the size is exceeded.
–help : print the summary of options to xargs and exit
–version : print the version no. of xargs and exit
```

| xarg command example | Description                                                                |
| -------------------- | -------------------------------------------------------------------------- |
| xargs --version      | Prints the version number of xargs command                                 |
| xargs -a test.txt    | It will show contents of file                                              |
| xargs -p -a test.txt | -p option prompts for confirmation before running each command line.       |
| xargs -r -a test.txt | -r option ensures if standard input is empty, then command is not executed |

### tee

گاهی اوقات ممکن است بخواهیم خروجی را روی صفحه ببینیم در حالی که یک کپی از آن را برای بعد ذخیره می‌کنیم. در حالی که **می‌توانیم** این کار را با هدایت خروجی دستور به یک فایل در یک پنجره و سپس استفاده از `tail -f` برای دنبال کردن خروجی در پنجره‌ای دیگر انجام دهیم، استفاده از دستور `tee` آسان‌تر است.

```
tee [OPTION]... [FILE]...
```

**tee** ورودی استاندارد را می‌خواند و آن را هم در خروجی استاندارد و هم در یک یا چند فایل می‌نویسد.

![](.gitbook/assets/upstreams-tee.jpg)

tee همیشه بعد از '|' کار می‌کند و هر دو وظیفه را همزمان انجام می‌دهد. مثال:

```
root@ubuntu16-1:~/test-space/myfiles# ls  | tee list.txt
dir1
dir2
interesting.txt
list
list.txt
myconf.txt
mydate
mylog.txt
root@ubuntu16-1:~/test-space/myfiles# cat list.txt 
dir1
dir2
interesting.txt
list
list.txt
myconf.txt
mydate
mylog.txt
```

با گزینه **`-a`**، این دستور فایل را بازنویسی نمی‌کند بلکه به انتهای فایل داده شده اضافه می‌کند.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-103-4/](https://developer.ibm.com/tutorials/l-lpic1-103-4/)

[https://www.gnu.org/software/libc/manual/html_node/Streams-and-File-Descriptors.html](https://www.gnu.org/software/libc/manual/html_node/Streams-and-File-Descriptors.html)

[https://geek-university.com/linux/streams/](https://geek-university.com/linux/streams/)

[https://ryanstutorials.net/bash-scripting-tutorial/bash-input.php](https://ryanstutorials.net/bash-scripting-tutorial/bash-input.php)

[https://www.howtogeek.com/435903/what-are-stdin-stdout-and-stderr-on-linux/](https://www.howtogeek.com/435903/what-are-stdin-stdout-and-stderr-on-linux/)

[https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection](https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection)

[https://www.serverwatch.com/columns/article.php/3860446/Shell-Scripts-and-Here-Documents.htm](https://www.serverwatch.com/columns/article.php/3860446/Shell-Scripts-and-Here-Documents.htm)

[https://linuxhint.com/bash-heredoc-tutorial/](https://linuxhint.com/bash-heredoc-tutorial/)

[https://stackoverflow.com/questions/40230008/whats-the-usages-of-hyphen-in-linux-shell](https://stackoverflow.com/questions/40230008/whats-the-usages-of-hyphen-in-linux-shell)

[https://www.geeksforgeeks.org/xargs-command-unix/](https://www.geeksforgeeks.org/xargs-command-unix/)

[https://www.tecmint.com/xargs-command-examples/](https://www.tecmint.com/xargs-command-examples/)

.
