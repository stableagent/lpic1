# 103.7. جستجوی فایل‌های متنی با استفاده از regular expressions

**وزن:** 2

**توضیحات:** داوطلبان باید قادر به دستکاری فایل‌ها و داده‌های متنی با استفاده از عبارات باقاعده (regular expressions) باشند. این هدف شامل ایجاد عبارات باقاعده ساده حاوی چندین عنصر نشانه‌گذاری است. همچنین شامل استفاده از ابزارهای عبارت باقاعده برای انجام جستجو در سیستم‌فایل یا محتوای فایل است.

**حوزه‌های کلیدی دانش:**

* ایجاد عبارات باقاعده ساده حاوی چندین عنصر نشانه‌گذاری
* استفاده از ابزارهای عبارت باقاعده برای انجام جستجو در سیستم‌فایل یا محتوای فایل

**اصطلاحات و ابزارهای کاربردی:**

* grep
* egrep
* fgrep
* sed
* regex(7)

در حالی که با فایل‌های متنی کار می‌کنیم، اغلب اتفاق می‌افتد که به دنبال متنی در یک فایل متنی هستیم. ممکن است به دنبال چیزی باشیم که چندان مشخص نیست. به عنوان مثال ما به دنبال "linux" یا "Linux" یا هر چیز دیگری هستیم، اینجاست که عبارت باقاعده (regular expression) به کار می‌آید (مهم‌ترین بخش سبک وزن!).

### عبارت باقاعده (Regular Expression)

عبارات باقاعده زمانی استفاده می‌شوند که بخواهیم خطوط خاصی از متن را که حاوی یک الگوی خاص است، جستجو کنیم. Regex را می‌توان در برنامه‌های مختلفی مانند grep، sed، vi، bash، rename و بسیاری دیگر استفاده کرد. در اینجا ما از regex با دستور grep استفاده خواهیم کرد.

یک الگوی regex از یک **موتور عبارت باقاعده (regular expression engine)** استفاده می‌کند که آن الگوها را ترجمه می‌کند.

لینوکس دارای دو موتور عبارت باقاعده است:

* موتور **عبارت باقاعده پایه (Basic Regular Expression یا BRE)**.
* موتور **عبارت باقاعده توسعه یافته (Extended Regular Expression یا ERE)**.

> بین عبارات باقاعده پایه و توسعه یافته تفاوت وجود دارد. برخی ابزارها فقط برای پشتیبانی از عبارات باقاعده پایه (BRE) نوشته شده‌اند و ابزارهای دیگر برای پشتیبانی از عبارات باقاعده توسعه یافته (ERE) نیز نوشته شده‌اند. اکثر برنامه‌های لینوکس با مشخصات موتور BRE به خوبی کار می‌کنند، در GNU grep، تفاوتی در عملکرد وجود ندارد.

## چه چیزی عبارات باقاعده را می‌سازد

دو نوع کاراکتر در عبارات باقاعده یافت می‌شوند:

* کاراکترهای تحت‌اللفظی (literal characters)
* متاکاراکترها (metacharacters)

**کاراکترهای تحت‌اللفظی** کاراکترهای استانداردی هستند که رشته‌های ما را می‌سازند. هر کاراکتر در این جمله یک کاراکتر تحت‌اللفظی است. شما می‌توانید از یک عبارت باقاعده برای جستجوی هر کاراکتر تحت‌اللفظی در آن رشته استفاده کنید.

**متاکاراکترها** موجودات کاملاً متفاوتی هستند؛ آن‌ها هستند که به عبارات باقاعده قدرت می‌دهند. با متاکاراکترها، ما می‌توانیم کارهای بیشتری از جستجوی یک کاراکتر واحد انجام دهیم. متاکاراکترها به ما اجازه می‌دهند ترکیبی از رشته‌ها و موارد بسیار بیشتری را جستجو کنیم. 

## grep

grep مخفف **general regular expression parser** است. فیلتر grep یک فایل را برای الگوی خاصی از کاراکترها جستجو کرده و تمام خطوطی را که حاوی آن الگو هستند نمایش می‌دهد. grep ابزاری است که می‌تواند از عبارات باقاعده بهره زیادی ببرد.

```
grep [options] pattern [files]
```

بیایید چند مثال ببینیم:

| command                          | description            |
| -------------------------------- | ---------------------- |
| echo "linux is my os" \| grep l  | **l**inux is my os     |
| echo "linux is my os" \| grep i  | l**i**nux **i**s my os |

 **اتصال (Concatenation)**

 متصل کردن دو عبارت باقاعده یک عبارت طولانی‌تر ایجاد می‌کند. 

| regex                                    | match                              |
| ---------------------------------------- | ---------------------------------- |
| echo "aa ab ba aaa bbb AB BA" \| grep a  | **aa a**b b**a** **aaa** bbb AB BA |
| echo "aa ab ba aaa bbb AB BA" \| grep ab | aa **ab** ba aaa bbb AB BA         |

```
Options Description
-c : This prints only a count of the lines that match a pattern
-h : Display the matched lines, but do not display the filenames.
-i : Ignores, case for matching
-l : Displays list of a filenames only.
-n : Display the matched lines and their line numbers.
-v : This prints out all the lines that do not matches the pattern
-e exp : Specifies expression with this option. Can use multiple times.
-f file : Takes patterns from file, one per line.
-E : Treats pattern as an extended regular expression (ERE)
-w : Match whole word
-o : Print only the matched parts of a matching line,
 with each such part on a separate output line.
```

### عبارات باقاعده توسعه یافته (Extended Regular Expressions)

 **تکرار (Repetition)**

* علامت **\*  ** به این معنی است که مورد قبلی **0 بار یا بیشتر** مطابقت داده خواهد شد.
* علامت **+** به این معنی است که مورد قبلی **1 بار یا بیشتر** مطابقت داده خواهد شد.
* علامت **?** به این معنی است که مورد قبلی **0 یا 1 بار** مطابقت داده خواهد شد.

{% hint style="info" %}
#### Globbing and Regex: So Similar, So Different

 مبتدیان گاهی اوقات تمایل دارند **wildcards**(گلابینگ) را با **عبارات باقاعده** هنگام استفاده از grep اما آن‌ها یکسان نیستند.\
 **Wildcards** هستند که توسط شل برای بسط دادن نام فایل‌ها ارائه می‌شوند در حالی که** عبارات باقاعده** یک مکانیسم فیلتر کردن متن هستند که برای استفاده با ابزارهایی مانند grep، sed و awk در نظر گرفته شده‌اند.
{% endhint %}

| Special Character | Meaning in Globs                   | Meaning in Regex                                            |
| ----------------- | ---------------------------------- | ----------------------------------------------------------- |
| **\***            | zero or more characters            | zero or more of the character it follows                    |
| **?**             | single occurrence of any character | zero or one of the character it follows but not more than 1 |
| **.**             | literal "." character              | any single character                                        |

به منظور جلوگیری از هرگونه اشتباه هنگام استفاده از عبارات باقاعده توسعه یافته، از `grep` با گزینه `-E` استفاده کنید، `-E` با الگو به عنوان یک عبارت باقاعده توسعه یافته (ERE) رفتار می‌کند.

{% hint style="info" %}
**نقل‌قول دوتایی " " : **همچنین باید regex توسعه یافته خود را بین نقل‌قول دوتایی قرار دهیم، در غیر این صورت ممکن است توسط شل تفسیر شود و نتایج متفاوتی به ما بدهد. 
{% endhint %}

| regex                                           | match                              |
| ----------------------------------------------- | ---------------------------------- |
| echo "aa ab ba aaa bbb AB BA" \| grep -E "a\*b" | aa **ab** **b**a aaa** bbb **AB BA |
| echo "aa ab ba aaa bbb AB BA" \| grep -E  "a+b" | aa **ab** ba aaa bbb AB BA         |
| echo "aa ab ba aaa bbb AB BA" \| grep -E "a?b"  | aa **ab** **b**a aaa **bbb** AB BA |

This is a point where egrep comes to play:

{% hint style="success" %}
## egrep

 **egrep** یک دستور جستجوی الگو است که به خانواده توابع grep تعلق دارد. این دستور همانند **`grep -E`** عمل می‌کند. با الگو به عنوان یک عبارت باقاعده توسعه یافته رفتار کرده و خطوطی را که با الگو مطابقت دارند چاپ می‌کند. اگر چندین فایل با الگوی مطابقت یافته وجود داشته باشد، نام فایل‌ها را نیز برای هر خط نمایش می‌دهد.

```
egrep [ options ] 'PATTERN' files 
```

 **Options:** Most of the options for this command are same as grep.

So instead of using grep -E command in above we can use egrep easily.
{% endhint %}

#### Curly Braces

آکولادها به ما اجازه می‌دهند تعداد دفعات وجود یک الگو را مشخص کنیم، سه فرمت دارد:

* **{N} **به این معنی است که مورد قبلی _دقیقاً_ **N بار** مطابقت داده می‌شود.
* **{N,} **به این معنی است که مورد قبلی **N بار یا بیشتر** مطابقت داده می‌شود.
* The **{N,M}** means preceding item is matched_ at least_ **N** _times, but not more than_ **M**_ times_.

| regex                                             | match                                   |
| ------------------------------------------------- | --------------------------------------- |
| echo "ab aab aaab aaaab ba Ab" \| egrep  "a{1}b"  | **ab** a**ab** aa**ab** aaa**ab** ba Ab |
| echo "ab aab aaab aaaab ba Ab" \| egrep "a{2,}b"  | ab **aab** **aaab** **aaaab** ba Abb    |
| echo "ab aab aaab aaaab ba Ab" \| egrep "a{1,3}b" | **ab aab aaab** a**aaab** ba Ab         |

**Any Characters**

کاراکتر نقطه (.) یک متاکاراکتر است که جایگزین هر کاراکتری (به جز خط جدید `\n`) می‌شود.

* The  **.  ** Matches **any single character**. 

> One of the most commonly used patterns is .**‘\*’**, which matches an arbitrary length string containing any characters (or no characters at all)

| regex                                         | match                     |
| --------------------------------------------- | ------------------------- |
| echo "ac abc aaabbbccc abcz" \| egrep "a.\*c" | **ac abc aaabbbccc abc**z |

**Anchor Characters**

کاراکترهای لنگر یا به سادگی کاراکترهای موقعیت‌دهی خط برای تعیین مکان ابتدا یا انتهای یک خط در متن استفاده می‌شوند:

* **^** با ابتدای خط مطابقت دارد.
* **$**  با انتهای خط مطابقت دارد.

```
root@ubuntu16-1:~# cat text
abc
bcd
efg
root@ubuntu16-1:~# egrep "^b" text
bcd
root@ubuntu16-1:~# egrep "g$" text
efg
```

**جایگزینی (Alternation) (چندین رشته)**

عملگر جایگزینی (|) با عبارت قبلی یا بعدی مطابقت دارد. برای مثال:

* **cat|dog** با **cat یا dog** مطابقت خواهد داشت.

| regex                                            | match                     |
| ------------------------------------------------ | ------------------------- |
| echo "cat dog was a cartoon" \| egrep "cat\|dog" | **cat dog** was a cartoon |

### کلاس‌های کاراکتر (Character Classes)

ما می‌توانیم با کاراکتر ویژه نقطه با هر کاراکتری مطابقت دهیم، اما اگر بخواهید فقط با مجموعه‌ای از کاراکترها مطابقت دهید چه؟ می‌توانیم از یک کلاس کاراکتر استفاده کنیم. کلاس کاراکتر با مجموعه‌ای از کاراکترها مطابقت دارد، اگر هر یک از آن‌ها پیدا شود، الگو مطابقت می‌یابد. کلاس کاراکتر با استفاده از براکت‌های مربعی \[ ] تعریف می‌شود.

**عبارت براکت (Bracket expression)**

با قرار دادن گروهی از کاراکترها در داخل براکت ("\[" و "]")، می‌توانیم مشخص کنیم که کاراکتر در آن موقعیت می‌تواند هر یک از کاراکترهای موجود در گروه براکت باشد.

*  **\[set_of_characters]** با هر تک کاراکتر در _**set_of_characters**_ مطابقت دارد. به طور پیش‌فرض، مطابقت به حروف بزرگ و کوچک حساس است.

| regex                                              | match                     |
| -------------------------------------------------- | ------------------------- |
| echo "cat dog was a cartoon" \| egrep "cart\[o]\*" | cat dog was a **cartoo**n |

### نفی کلاس‌های کاراکتر (Negating Character Classes)

در مورد جستجو برای کاراکتری که در کلاس کاراکتر نیست چطور؟ برای دستیابی به این هدف، قبل از محدوده کلاس کاراکتر از علامت توان (caret) **^** استفاده کنید.

*  **\[^set_of_characters] ** _Negation:_ با هر تک کاراکتری که در _**set_of_characters**_ **نیست** مطابقت دارد. به طور پیش‌فرض، مطابقت به حروف بزرگ و کوچک حساس است.

| regex                                                 | match                     |
| ----------------------------------------------------- | ------------------------- |
| echo "cat dog was a cartoon" \| grep ".\*\[^cartoon]" | **cat dog was a** cartoon |

**عبارت محدوده (Range expression)**

برای تعیین محدوده‌ای از کاراکترها، می‌توانیم از نماد **(-)** **خط تیره** استفاده کنیم، مانند 0-9 برای ارقام. توجه داشته باشید که محدوده‌ها به تنظیمات محلی (locale) بستگی دارند.

| regex                                        | match                               |
| -------------------------------------------- | ----------------------------------- |
| echo "a12345a a54321a 123" \| egrep "\[a-z]" | **a**12345**a** **a**54321**a** 123 |

### کلاس‌های کاراکتر خاص (**کلاس‌های نام‌گذاری شده یا Named classes)**

چندین کلاس نام‌گذاری شده، میانبر مناسبی برای کلاس‌های پرکاربرد فراهم می‌کنند. کلاس‌های نام‌گذاری شده با \[: باز شده و با :] بسته می‌شوند و ممکن است در عبارت‌های براکت استفاده شوند. برخی مثال‌ها:

* **\[\[:alnum:]] ** Alphanumeric characters.
* **\[\[:alpha:]]  **Alphabetic characters.
* **\[\[:blank:]] ** Space and tab characters.
* **\[\[:digit:]] ** The digits 0 through 9 (equivalent to 0-9).
* **\[\[:upper:]] **and** \[:lower:] ** Upper and lower case letters, respectively.

| **regex**                                                 | match                               |
| --------------------------------------------------------- | ----------------------------------- |
| echo "a12345a a54321a 123 678 9" \| egrep "\[\[:alpha:]]" | **a**12345**a** **a**54321**a** 123 |

**عبارات گروهی (Group expressions)**

با قرار دادن بخشی از یک عبارت باقاعده در پرانتز، می‌توانیم آن بخش از عبارت باقاعده را با هم گروه‌بندی کنیم.

* **() Groups regular expressions**

| regex                                                    | match                         |
| -------------------------------------------------------- | ----------------------------- |
| echo "a12345a a54321a 123 678 9" \| egrep "(1a.\*)\|(9)" | a12345a a5432**1a 123 678 9** |

### **کاراکترهای خاص (Special Characters)**

الگوهای regex از برخی کاراکترهای خاص استفاده می‌کنند. و ما نمی‌توانیم آن‌ها را در الگوهای خود بگنجانیم و اگر این کار را انجام دهیم، نتیجه مورد انتظار را نخواهیم گرفت. این کاراکترهای خاص توسط regex شناسایی می‌شوند:

**.\*\[]^${}\\+?|()**

پس چگونه می‌توانیم به دنبال برخی از آن‌ها در یک متن بگردیم؟ اینجاست که fgrep وارد بازی می‌شود.

### fgrep

اگر قدرت regex را نخواهید، می‌تواند بسیار ناامیدکننده باشد. این موضوع به ویژه زمانی صادق است که شما واقعاً به دنبال برخی از کاراکترهای خاص در مجموعه‌ای از متن هستید. می‌توانید از دستور `fgrep` (یا `grep -F` که همان است) استفاده کنید تا از هرگونه جایگزینی regex صرف‌نظر کنید. با استفاده از `fgrep` ، شما دقیقاً به دنبال آنچه تایپ می‌کنید می‌گردید، حتی اگر کاراکترهای خاص باشند.

```
fgrep [options] [ -e pattern_list] [pattern] [file]
```

```
    -c : It is used to print only a count of the lines which contain the pattern.
    -h : Used to display the matched lines.
    -i : During comparisions, it will ignore upper/lower case distinction.
    -l : Used to print the names of files with matching lines once, separated by new-lines. It will not repeat the names of files when the pattern is found more than once.
    -n : It is used precede each line by its line number in the file (first line is 1).
    -s : It will only display the error messages.
    -v : Print all lines except those contain the pattern.
    -x : Print only lines matched entirely.
    -e pattern_list : Search for a string in pattern-list (useful when the string begins with a “-“).
    -f pattern-file : Take the list of patterns from pattern-file.
    pattern : Specify a pattern to be used during the search for input.
    file : A path name of a file to be searched for the patterns. If no file operands are specified, the standard input will be used.

```

as an example:

```
root@ubuntu16-1:~# cat text
^abc
bcd$
efg|
root@ubuntu16-1:~# fgrep "^" text
^abc
root@ubuntu16-1:~# fgrep "$" text
bcd$
```

### sed

دستور Sed یا **S**tream **Ed**itor یک ابزار بسیار قدرتمند ارائه شده توسط سیستم‌های لینوکس/یونیکس است. عمدتاً برای **جایگزینی متن**، **جستجو و جایگزینی** استفاده می‌شود اما می‌تواند دستکاری‌های متنی دیگری مانند **درج (insertion)**، **حذف (deletion)**، **جستجو** و غیره را نیز انجام دهد.

With **sed**, we can edit complete files without actually having to open it. Sed also supports the use of regular expressions, which makes sed an even more powerful **text manipulation tool**.

 We have previously used sed for text substitution, here we want to use regex with that.

```
sed OPTIONS... [SCRIPT] [INPUTFILE...]
```

 `-r, --regexp-extended ` tells sed that we are using **regular expressions** in the script.

```
root@ubuntu16-1:~# cat text
Comment this is a  comment:
comment pigs can not fly because the sky is high!

root@ubuntu16-1:~# sed -r "s/^(Comment|comment)/\#/" text
# this is a  comment:
# pigs can not fly because the sky is high!
```

به طور پیش‌فرض، sed هر خط را چاپ می‌کند. اگر جایگزینی انجام دهد، متن جدید به جای متن قدیمی چاپ می‌شود. اگر از یک آرگومان اختیاری برای sed استفاده کنید، "sed -n" ، این کار را انجام نمی‌دهد:

```
root@ubuntu16-1:~# sed -rn "s/^(Comment|comment)/\#/"  text
root@ubuntu16-1:~# 
```

وقتی از گزینه "-n" استفاده می‌شود، پرچم "p" باعث می‌شود فقط خط اصلاح شده چاپ شود:

```
root@ubuntu16-1:~# sed -rn "s/^(Comment|comment)/CHANGED/p"  text
CHANGED this is a  comment:
CHANGED pigs can not fly because the sky is high!
```

sed ابزار بسیار قدرتمندی است و پیچیده می‌باشد، ما فقط نگاهی گذرا به آن انداختیم! 

.

.

.

[http://www.grymoire.com/Unix/Regular.html](http://www.grymoire.com/Unix/Regular.html)

[https://www.linux.com/tutorials/introduction-regular-expressions-new-linux-users/](https://www.linux.com/tutorials/introduction-regular-expressions-new-linux-users/)

[https://linux.die.net/Bash-Beginners-Guide/sect\_04\_01.html](https://linux.die.net/Bash-Beginners-Guide/sect\_04_01.html)

****[https://dzone.com/articles/35-examples-of-regex-patterns-using-sed-and-awk-in](https://dzone.com/articles/35-examples-of-regex-patterns-using-sed-and-awk-in)

[https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux#regular-expressions](https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux#regular-expressions)

[https://www.linuxnix.com/10-file-globbing-examples-linux-unix/](https://www.linuxnix.com/10-file-globbing-examples-linux-unix/)

[https://www.linuxjournal.com/content/globbing-and-regex-so-similar-so-different](https://www.linuxjournal.com/content/globbing-and-regex-so-similar-so-different)

[https://www.zyxware.com/articles/4627/difference-between-grep-and-egrep](https://www.zyxware.com/articles/4627/difference-between-grep-and-egrep)

[https://www.geeksforgeeks.org/fgrep-command-in-linux-with-examples/](https://www.geeksforgeeks.org/fgrep-command-in-linux-with-examples/)

[https://www.linuxtechi.com/20-sed-command-examples-linux-users/](https://www.linuxtechi.com/20-sed-command-examples-linux-users/)

[http://www.grymoire.com/Unix/Sed.html](http://www.grymoire.com/Unix/Sed.html)

[https://www.linuxtechi.com/20-sed-command-examples-linux-users/](https://www.linuxtechi.com/20-sed-command-examples-linux-users/)

.
