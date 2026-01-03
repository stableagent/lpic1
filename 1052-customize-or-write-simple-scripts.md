# 105.2 شخصی‌سازی یا نوشتن اسکریپت‌های ساده

**وزن:** ۴

**توضیحات:** داوطلبان باید قادر به شخصی‌سازی اسکریپت‌های موجود یا نوشتن اسکریپت‌های جدید و ساده Bash باشند.

**حوزه‌های کلیدی دانش:**

* استفاده از سینتکس استاندارد sh (حلقه‌ها، تست‌ها)
* استفاده از جایگزینی دستور (command substitution)
* تست مقادیر بازگشتی برای موفقیت یا شکست یا سایر اطلاعات ارائه شده توسط یک دستور
* انجام ارسال ایمیل مشروط به superuser
* انتخاب صحیح مفسر اسکریپت از طریق خط shebang (#!)
* مدیریت مکان، مالکیت، اجرا و حقوق suid اسکریپت‌ها

**اصطلاحات و ابزارهای کاربردی:**

* for
* while
* test
* if
* read
* seq
* exec

این درس تماماً در مورد اسکریپت‌نویسی شل در لینوکس است و ما شروع به نوشتن چند اسکریپت ساده می‌کنیم. ما می‌توانیم تقریباً هر دستوری را که استفاده می‌کنیم، داخل یک اسکریپت شل قرار دهیم. اما قبل از شروع، چند نکته وجود دارد که باید در نظر بگیرید.

### shebang (#!)

در لینوکس و دنیای متن‌باز، فایل‌های اسکریپت بسیار مهم هستند. انواع مختلفی از زبان‌های اسکریپت‌نویسی برای نوشتن فایل‌های اسکریپت استفاده می‌شود. از آنجایی که پسوند فایل فقط یک برچسب برای اسکریپت است، **shebang** (خط مفسر فایل اسکریپت) برای تعیین زبان اسکریپت‌نویسی استفاده می‌شود. 

```
#!/bin/bash

echo "Hello World!
```

علاوه بر این، انواع مختلفی از شل وجود دارد و شما نمی‌توانید تضمین کنید که کاربران احتمالی شما کدام شل را ترجیح می‌دهند. shebang با دستور دادن به شل برای اجرای اسکریپت شما در یک شل خاص، اسکریپت‌ها را در تمام محیط‌های شل قابل حمل (portable) می‌کند.

> `#` و `!` این خط را خاص می‌کنند زیرا `#` به عنوان خط کامنت در bash استفاده می‌شود.

خط shebang باید اولین خط اسکریپت شما باشد و بقیه خط شامل مسیر شلی است که برنامه شما باید تحت آن اجرا شود.

### متغیرها (variables)

در بخش‌های قبلی این سری، یاد گرفتیم که چگونه یک متغیر تعریف کنیم: `VARIABLE="value"`. ما می‌توانیم همین کار را در یک اسکریپت انجام دهیم، به عنوان مثال:

```
#!/bin/bash

MYNAME="payam"
echo "Hello $MYNAME!"
```

ما مقادیر رشته‌ای را به متغیرها اختصاص داده‌ایم. Bash از محاسبات شل با استفاده از اعداد صحیح پشتیبانی می‌کند. 

{% hint style="info" %}
**declare**

دستور **declare** یک دستور داخلی شل **bash** است. برای تعریف متغیرها و توابع شل، تنظیم ویژگی‌های آن‌ها و نمایش مقادیر آن‌ها استفاده می‌شود. ` -i ` باعث می‌شود یک متغیر ویژگی 'integer' (عدد صحیح) داشته باشد.

```
user1@ubuntu16-1:~/sandbox$ declare VAR1="hello"
user1@ubuntu16-1:~/sandbox$ declare -i VAR2="42"
```

**-p** اگر با آرگومان‌های نام استفاده شود، گزینه‌ها و ویژگی‌های هر نام متغیر را نمایش می‌دهد:

```
user1@ubuntu16-1:~/sandbox$ declare -p VAR1 VAR2
declare -- VAR1="hello"
declare -i VAR2="42"
```
{% endhint %}

### جایگزینی دستور (command substitution)

گاهی اوقات ممکن است نیاز داشته باشیم از نتیجه یک دستور برای دستور دیگری یا یک متغیر استفاده کنیم. 

اگر دستوری را با `$( ` و ` )` یا با یک جفت backticks محصور کنیم، می‌توانیم خروجی دستور را به عنوان ورودی دستور دیگری جایگزین کنیم. این تکنیک _command substitution_ نامیده می‌شود. 

```
$(command)
### OR
`command`
```

مثال:

```
user1@ubuntu16-1:~/sandbox$ MYLIST=$(ls -l)
user1@ubuntu16-1:~/sandbox$ echo $MYLIST
total 4 -rw-rw-r-- 1 user1 user1 58 فوریه 4 12:24 script1

user1@ubuntu16-1:~/sandbox$ MYSPACE=`du -sh`
user1@ubuntu16-1:~/sandbox$ echo $MYSPACE
8.0K .
```

### اجرای اسکریپت‌ها

برای اجرای یک اسکریپت شل، اسکریپت باید قابل اجرا (executable) باشد.

```
user1@ubuntu16-1:~/sandbox$ ls -l
total 4
-rw-rw-r-- 1 user1 user1 58 فوریه  4 12:24 script1
user1@ubuntu16-1:~/sandbox$ chmod +x script1 
user1@ubuntu16-1:~/sandbox$ ls -l
total 4
-rwxrwxr-x 1 user1 user1 32 فوریه  4 14:14 script1
```

> راه دیگر برای اجرای یک اسکریپت، دادن نام اسکریپت به عنوان پارامتر به دستورات `sh` یا `bash` است. `sh ./script1` یا `bash ./script1`

### مکان‌یابی اسکریپت‌ها

اگر اسکریپت ما قابل اجرا باشد، می‌توانیم آن را به ۳ روش مکان‌یابی کنیم:

* اگر در همان دایرکتوری هستیم، می‌توانیم از `./scriptname` استفاده کنیم.
* می‌توانیم از مسیر مطلق `/home/user1/sandbox/scriptname` استفاده کنیم.
* قرار دادن اسکریپت در یکی از دایرکتوری‌های `$PATH` یا ویرایش متغیر `PATH`.

```
user1@ubuntu16-1:~/sandbox$ ./script1 
Hello World

user1@ubuntu16-1:~/sandbox$ /home/user1/sandbox/script1
Hello World

user1@ubuntu16-1:~$ echo $PATH
/home/user1/bin:/home/user1/.local/bin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/snap/bin

user1@ubuntu16-1:~$ mkdir bin
user1@ubuntu16-1:~$ cp sandbox/script1 bin/
user1@ubuntu16-1:~$ scr
screendump    script        script1       scriptreplay  
user1@ubuntu16-1:~$ script1
Hello World
```

### شرط‌ها (conditioning)

برای نوشتن برنامه‌های مفید، ما تقریباً همیشه به توانایی بررسی شرایط و تغییر رفتار برنامه بر اساس آن نیاز داریم. جملات شرطی این توانایی را به ما می‌دهند. 

ساده‌ترین شکل، دستور **if** است که شکل کلی زیر را دارد:

### if

```
if [expression]
then
   command1 
   command2
else
   command3
   command4
fi
```

> بخش else اختیاری است و می‌تواند حذف شود.

```
#!/bin/bash

MYOS="linux"
echo "Lets see What is your favirite OS"

if [ $MYOS = "linux" ] 
then
 echo "Haha happy to hear that"
else
 echo "I wish you liked linux"
fi
```

> بررسی واقعی شرط توسط دستور `test` انجام می‌شود که به صورت `test EXPRESSION` نوشته می‌شود و معادل `[ EXPRESSION ]` است.

عبارات به اشکال زیر هستند:

| عبارت | معنا |
| --------------------- | --------------------------------------------- |
| STRING1 **=** STRING2 | رشته‌ها برابر هستند |
| STRING1 != STRING2    | رشته‌ها برابر نیستند |
| INTEGER1 -eq INTEGER2 | INTEGER1 با INTEGER2 برابر است |
| INTEGER1 -ge INTEGER2 | INTEGER1 بزرگتر یا مساوی INTEGER2 است |
| INTEGER1 -gt INTEGER2 | INTEGER1 بزرگتر از INTEGER2 است |
| INTEGER1 -le INTEGER2 | INTEGER1 کوچکتر یا مساوی INTEGER2 است |
| INTEGER1 -lt INTEGER2 | INTEGER1 کوچکتر از INTEGER2 است |
| INTEGER1 -ne INTEGER2 | INTEGER1 با INTEGER2 برابر نیست |
| -n STRING             | طول STRING صفر نیست |
| -z STRING             | طول STRING صفر است |
| -f FILE               | FILE وجود دارد و یک فایل معمولی است |
| -s FILE               | FILE وجود دارد و اندازه آن بزرگتر از صفر است |
| -X FILE               | FILE وجود دارد و قابل اجرا است |
| -d FILE               | FILE وجود دارد و یک دایرکتوری است |

### read

دستور `read` یک خط را از ورودی استاندارد می‌خواند.

```
#!/bin/bash

MYOS="linux"
echo "Lets see What is your favirite OS"

if [ $MYOS = "linux" ] 
then
 echo "Haha happy to hear that"
else
 echo "I wish you liked linux"
fi
```

### حلقه‌ها (loops)

هدف از حلقه‌ها تکرار کد یکسان یا مشابه به تعداد دفعات مشخص است. این تعداد دفعات می‌تواند یک عدد مشخص باشد، یا می‌تواند توسط برقرار شدن یک شرط خاص تعیین شود. معمولاً انواع مختلفی از حلقه‌ها وجود دارد، از جمله حلقه‌های for، حلقه‌های while و ... .

### for

حلقه `for` هر آیتم را در یک دنباله پردازش می‌کند.

```
for VAR in LIST;
do
  command1
  command2
done
```

> لطفا به سینتکس `;` ، `do` و `done` توجه کنید.

> LIST می‌تواند لیستی از رشته‌ها، یک آرایه یا خروجی دستورات و غیره باشد.

```
#!/bin/bash

for i in 1 2 3 ;
do
 echo $i
done
```

### **seq**

دستور **seq** یک دستور بسیار کاربردی است. این دستور دنباله‌ای از اعداد را برای حلقه از FIRST تا LAST با گام‌های INCREMENT تولید می‌کند. 

```
seq [OPTION]... LAST
 ### or
seq [OPTION]... FIRST LAST
 ### or
seq [OPTION]... FIRST INCREMENT LAST
```

```
user1@ubuntu16-1:~/sandbox$ seq 3
1
2
3
user1@ubuntu16-1:~/sandbox$ seq 1 3
1
2
3
user1@ubuntu16-1:~/sandbox$ seq 1 10 2
1
user1@ubuntu16-1:~/sandbox$ seq 1 3 10
1
4
7
10
```

seq در حلقه for:

```
user1@ubuntu16-1:~/sandbox$ for i in $(seq 1 3); do echo $i; done
1
2
3
```

> ما همچنین می‌توانیم یک محدوده را با سینتکس `{START..END..INCREMENT}` تعریف کنیم که همان کار را انجام می‌دهد.

حلقه for با رشته‌ها نیز کار می‌کند:

```
#!/bin/bash

for MYFILE in $(ls);
do
    echo $MYFILE
done
```

### while

حلقه‌های while هر بار که حلقه شروع می‌شود یک شرط را ارزیابی می‌کنند و اگر شرط درست (true) باشد، لیست دستورات را اجرا می‌کنند. اگر شرط در ابتدا درست نباشد، دستورات هرگز اجرا نمی‌شوند.

```
while [condition]
do
    command1
    command2
done
```

مثال:

```
#!/bin/bash

MYVAR=3

while [ $MYVAR -gt 0 ]
do
  echo $MYVAR
  let MYVAR=MYVAR-1
done
```

> دستور **let** یک دستور داخلی است و برای ارزیابی عبارات محاسباتی روی متغیرهای شل استفاده می‌شود. این یعنی چه؟ بدون let، عبارت `MYVAR=MYVAR-1` مقدار "3-1" را به عنوان یک رشته خواهد داشت! 

> اگر می‌خواهید از مقدار یک متغیر در یک عبارت محاسباتی استفاده کنید، نیازی به استفاده از `$` قبل از نام متغیر نیست، اگرچه اگر بخواهید می‌توانید این کار را انجام دهید.

{% hint style="danger" %}
**حلقه‌های بی‌پایان (infinite loops)**

اگر شرط حلقه while هرگز برقرار نشود، برای همیشه اجرا می‌شود که به آن حلقه بی‌پایان می‌گویند و برای متوقف کردن آن باید از ctrl+c استفاده کنیم! 

حلقه‌های بی‌پایان آنقدرها هم بد نیستند! سیستم‌عامل یک حلقه بی‌پایان است که دائماً ورودی‌ها را بررسی کرده و بر اساس آن پاسخ می‌دهد.
{% endhint %}

{% hint style="info" %}
**until**

حلقه‌های until لیست دستورات را اجرا می‌کنند و هر بار که حلقه به پایان می‌رسد، یک شرط را ارزیابی می‌کنند. اگر شرط درست باشد، حلقه دوباره اجرا می‌شود. حتی اگر شرط در ابتدا درست نباشد، دستورات حداقل یک بار اجرا می‌شوند.
{% endhint %}

### exec <a href="mailing-notifications-to-root" id="mailing-notifications-to-root"></a>

دستور **exec** در لینوکس برای اجرای یک دستور از خودِ bash استفاده می‌شود. این دستور یک فرآیند (process) جدید ایجاد نمی‌کند، بلکه فقط bash را با دستوری که باید اجرا شود جایگزین می‌کند. اگر دستور exec موفقیت‌آمیز باشد، به فرآیند فراخوان باز نمی‌گردد. `exec BLAH` و `exec ls` را امتحان کنید و نتایج را مقایسه کنید.

### ارسال ایمیل اعلان به root <a href="mailing-notifications-to-root" id="mailing-notifications-to-root"></a>

برای ارسال ایمیل، ابتدا باید `mailutils` نصب شده باشد. سپس می‌توانیم ایمیل بفرستیم:

```
root@ubuntu16-1:~# mail root@localhost
Cc: 
Subject: test mail!
this is my first email!
```

فرض کنید اسکریپت شما در نیمه‌شب، زمانی که شما در خواب عمیق هستید، در حال انجام یک وظیفه مدیریتی روی سیستم شماست. وقتی مشکلی پیش می‌آید چه اتفاقی می‌افتد؟ خوشبختانه، ارسال اطلاعات خطا یا فایل‌های لاگ به خودتان، مدیر دیگر یا root آسان است. به سادگی پیام را به دستور `mail` پایپ (pipe) کنید و از گزینه `-s` برای افزودن موضوع (subject) استفاده کنید.

```
user1@ubuntu16-1:~$ echo "Body!" | mail -s "Subject" root@localhost
```

> اگر نیاز به ارسال یک فایل لاگ دارید، از تابع تغییر مسیر `<` استفاده کنید تا آن را به عنوان ورودی به دستور `mail` هدایت کنید. اگر نیاز به ارسال چندین فایل دارید، می‌توانید از `cat` برای ترکیب آن‌ها استفاده کنید و خروجی را به `mail` پایپ کنید. 

{% hint style="info" %}
**تقلب‌نامه (cheatsheet) اسکریپت‌نویسی bash**

همچنین شما را تشویق می‌کنم که از تقلب‌نامه اسکریپت‌نویسی bash من در این آدرس دیدن کنید: [https://borosan.gitbook.io/bash-scripting/](https://borosan.gitbook.io/bash-scripting/)
{% endhint %}

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-105-2/](https://developer.ibm.com/tutorials/l-lpic1-105-2/)

[https://www.poftut.com/linux-bin-bash-shell-and-script-tutorial/](https://www.poftut.com/linux-bin-bash-shell-and-script-tutorial/)

[https://www.geeksforgeeks.org/declare-command-in-linux-with-examples/](https://www.geeksforgeeks.org/declare-command-in-linux-with-examples/)

[https://www.gnu.org/software/bash/manual/html_node/Command-Substitution.html](https://www.gnu.org/software/bash/manual/html_node/Command-Substitution.html)

[https://jadi.gitbooks.io/lpic1/content/1052\_customize_or_write_simple_scripts.html](https://jadi.gitbooks.io/lpic1/content/1052\_customize_or_write_simple_scripts.html)

[https://www.computerhope.com/unix/test.htm](https://www.computerhope.com/unix/test.htm)

[https://www.dpscomputing.com/blog/2012/09/13/programming-the-purpose-of-loops/](https://www.dpscomputing.com/blog/2012/09/13/programming-the-purpose-of-loops/)

[https://www.geeksforgeeks.org/seq-command-in-linux-with-examples/](https://www.geeksforgeeks.org/seq-command-in-linux-with-examples/)

[https://linux4one.com/bash-for-loop-with-examples/](https://linux4one.com/bash-for-loop-with-examples/)

[https://www.geeksforgeeks.org/let-command-in-linux-with-examples/](https://www.geeksforgeeks.org/let-command-in-linux-with-examples/)

[https://www.quora.com/What-are-some-practical-uses-of-infinite-loops](https://www.quora.com/What-are-some-practical-uses-of-infinite-loops)

[https://www.geeksforgeeks.org/exec-command-in-linux-with-examples/](https://www.geeksforgeeks.org/exec-command-in-linux-with-examples/)

.
