# 103.3 انجام مدیریت فایل (file management) پایه

### **103.3 انجام مدیریت فایل (file management) پایه**

**وزن:** ۴

**توضیحات:** داوطلبان باید قادر باشند از دستورات پایه Linux برای مدیریت فایل‌ها و دایرکتوری‌ها استفاده کنند.

**حوزه‌های کلیدی دانش:**

* کپی، جابه‌جایی و حذف فایل‌ها و دایرکتوری‌ها به صورت تکی
* کپی کردن چندین فایل و دایرکتوری به صورت بازگشتی (recursive)
* حذف فایل‌ها و دایرکتوری‌ها به صورت بازگشتی
* استفاده از wildcardهای ساده و پیشرفته در دستورات
* استفاده از find برای یافتن فایل‌ها و انجام عملیات بر اساس نوع، اندازه یا زمان
* استفاده از tar، cpio و dd

**اصطلاحات و ابزارهای کاربردی:**

* cp
* find
* mkdir
* mv
* ls
* rm
* rmdir
* touch
* tar
* cpio
* dd
* file
* gzip
* gunzip
* bzip2
* xz
* file globbing

همانطور که گفتیم Linux دنیای processها و fileهاست. در این بخش درباره file management در Linux صحبت می‌کنیم و هم فایل‌ها و هم دایرکتوری‌ها (folderها) را بررسی می‌کنیم. ابزارها نسبتاً ساده هستند و نام‌هایشان تا حد زیادی بیانگر کاری است که واقعاً انجام می‌دهند.

### ls

دستور ls **فایل‌ها و دایرکتوری‌ها را لیست می‌کند** و متا-دیتاهای مرتبط با آن‌ها مانند اندازه فایل، مالکیت و زمان اصلاح را نشان می‌دهد.

```
ls [options] [file|dir]
```

ما می‌توانیم از **مسیرهای مطلق (absolute paths)** یا **مسیرهای نسبی (relative paths)** با ls استفاده کنیم.

بدون هیچ گزینه‌ای، ls فایل‌های موجود در دایرکتوری فعلی را لیست کرده و آن‌ها را به ترتیب الفبا مرتب می‌کند.

```
user1@ubuntu16-1:~$ ls
Desktop    Downloads         Music     Public     Videos
Documents  examples.desktop  Pictures  Templates
```

` -1` خروجی را در هر خط 1 مورد چاپ می‌کند:

```
user1@ubuntu16-1:~$ ls -1
Desktop
Documents
Downloads
examples.desktop
Music
Pictures
Public
Templates
Videos
```

خروجی پیش‌فرض دستور ls فقط نام فایل‌ها را نشان می‌دهد که چندان آموزنده نیست. پس بیایید از ls با گزینه `-l` برای قالب لیست طولانی استفاده کنیم:

```
user1@ubuntu16-1:~$ ls -l
total 44
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Desktop
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Documents
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Downloads
-rw-r--r-- 1 user1 user1 8980 Apr 20  2016 examples.desktop
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Music
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Pictures
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Public
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Templates
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Videos
```

اولین کاراکتر نشان‌دهنده نوع فایل است: "-" برای یک فایل معمولی، "d" برای یک دایرکتوری، "l" برای یک لینک نمادین (symbolic link).

هنگامی که از قالب لیست طولانی استفاده می‌شود، دستور ls اطلاعات فایل زیر را نمایش می‌دهد:

1. نوع فایل
2. مجوزهای فایل
3. تعداد لینک‌های سخت (hard links) به فایل
4. مالک فایل
5. گروه فایل
6. اندازه فایل
7. تاریخ و زمان
8. نام فایل

**نمایش فایل‌های مخفی**

به طور پیش‌فرض، دستور ls فایل‌های مخفی را نشان نمی‌دهد._ در لینوکس، فایل مخفی هر فایلی است که با یک نقطه (.) شروع می‌شود_. برای نمایش تمام فایل‌ها از جمله فایل‌های مخفی از گزینه `-a` استفاده کنید:

```
user1@ubuntu16-1:~$ ls -a
.              .config           .gconf         Pictures   .Xauthority
..             Desktop           .gnupg         .profile   .xsession-errors
.bash_history  .dmrc             .ICEauthority  Public     .xsession-errors.old
.bash_logout   Documents         .local         .ssh
.bashrc        Downloads         .mozilla       Templates
.cache         examples.desktop  Music          Videos
```

برخی گزینه‌های مفید دیگر:

| ls command options | description                                              |
| ------------------ | -------------------------------------------------------- |
| -l                 | list with long format - show permissions                 |
| -r                 | list in reverse order                                    |
| -t                 | sort by time & date                                      |
| -lh                | List Files with Human Readable Format                    |
| -F                 | List Files and Directories with ‘/’ Character at the end |
| -R                 | Recursively list Sub-Directories                         |
| -a                 | list all files including hidden file starting with '.'   |

## کپی، جابجایی و حذف <a href="copying-moving--deleting" id="copying-moving--deleting"></a>

برای آسان‌تر شدن کار، بیایید آن‌ها را در چند گروه دسته‌بندی کنیم:

![](.gitbook/assets/basicfilemgmnt-table.jpg)

قبل از پرداختن به نحوه استفاده از دستور touch، بیایید با مرور برچسب‌های زمانی (timestamps) فایل در لینوکس شروع کنیم.

#### برچسب‌های زمانی فایل‌های لینوکس (Linux Files Timestamps)

در لینوکس هر فایل با برچسب‌های زمانی مرتبط است و هر فایل اطلاعات **زمان آخرین دسترسی**، **زمان آخرین اصلاح** و **زمان آخرین تغییر** را ذخیره می‌کند. بنابراین، هر زمان که فایل جدیدی ایجاد می‌کنیم، به فایلی دسترسی پیدا می‌کنیم یا فایلی را اصلاح می‌کنیم، برچسب‌های زمانی آن فایل به طور خودکار به‌روزرسانی می‌شوند.

یک فایل در لینوکس دارای سه برچسب زمانی است:

* **atime** (access time) - آخرین زمانی که فایل توسط دستور یا برنامه‌ای مانند cat، vim یا grep دسترسی/باز شده است.
* **mtime** (modify time) - آخرین زمانی که محتوای فایل اصلاح شده است.
* **ctime** (change time) - آخرین زمانی که ویژگی (attribute) یا محتوای فایل تغییر کرده است. ویژگی‌ها شامل مجوزهای فایل، مالکیت فایل یا مکان فایل است.

برای نمایش وضعیت فایل از جمله برچسب‌های زمانی می‌توانید از دستور `stat` استفاده کنید.

```
root@ubuntu16-1:~# stat /etc/hosts
  File: '/etc/hosts'
  Size: 247           Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d    Inode: 1200795     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 02:34:44.962514623 -0700
Modify: 2018-07-01 23:15:30.140815060 -0700
Change: 2018-07-01 23:15:30.144817112 -0700
 Birth: -
```

### touch

دستور touch یک دستور استاندارد در سیستم‌عامل یونیکس/لینوکس است که برای ایجاد، تغییر و اصلاح برچسب‌های زمانی یک فایل استفاده می‌شود.

* دستور touch (بدون هیچ گزینه‌ای) اگر فایل وجود نداشته باشد، یک فایل خالی ایجاد می‌کند.
* اگر فایل وجود داشته باشد، دستور touch برچسب‌های زمانی آن را تغییر می‌دهد.

**ایجاد یک فایل خالی:**

دستور touch برای ایجاد یک فایل بدون هیچ محتوایی استفاده می‌شود. فایل ایجاد شده با استفاده از دستور touch خالی است. این دستور زمانی می‌تواند استفاده شود که کاربر در زمان ایجاد فایل داده‌ای برای ذخیره کردن نداشته باشد.

```
touch file_name
```

مثال:

```
root@ubuntu16-1:~/test-space# touch file1
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep  8 02:37 file1
```

**ایجاد چندین فایل خالی:**

```
touch File1_name File2_name File3_name
```

مثلاً:

```
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep  8 02:37 file1
root@ubuntu16-1:~/test-space# touch file1 file2 file3
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep  8 02:44 file1
-rw-r--r-- 1 root root 0 Sep  8 02:44 file2
-rw-r--r-- 1 root root 0 Sep  8 02:44 file3
```

**جلوگیری از ایجاد فایل‌های جدید:**

```
root@ubuntu16-1:~/test-space# touch -c file4
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep  8 02:44 file1
-rw-r--r-- 1 root root 0 Sep  8 02:44 file2
-rw-r--r-- 1 root root 0 Sep  8 02:44 file3
```

گزینه `-c` می‌گوید که اگر فایل وجود ندارد، آن را ایجاد نکن:

#### گزینه‌های برچسب زمانی دستور touch:

* `-a`: فقط تغییر **زمان دسترسی** (access time)
* `-d`: به‌روزرسانی **زمان دسترسی و اصلاح**
* `-m`: فقط تغییر **زمان اصلاح** (modification time)
* `-r`: **استفاده از زمان دسترسی و اصلاح فایل دیگر به عنوان مرجع.**
* `-t`: **ایجاد یک فایل با استفاده از یک زمان مشخص**

بیایید برخی اصلاحات برچسب زمانی را انجام دهیم.

ایجاد یک فایل با استفاده از یک زمان مشخص: `touch -t YYMMDDHHMM fileName`

```
root@ubuntu16-1:~/test-space# touch -t 9812010510 myfile
root@ubuntu16-1:~/test-space# ls -l | grep myfile
-rw-r--r-- 1 root root 0 Dec  1  1998 myfile
root@ubuntu16-1:~/test-space# stat myfile
  File: 'myfile'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228284     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 1998-12-01 05:10:00.000000000 -0800
Modify: 1998-12-01 05:10:00.000000000 -0800
Change: 2019-09-08 03:19:28.410459835 -0700
 Birth: -
```

تغییر زمان دسترسی: `touch -a fileName`

```
root@ubuntu16-1:~/test-space# touch -a  myfile
```

فقط زمان آخرین دسترسی را به‌روزرسانی می‌کند.

```
root@ubuntu16-1:~/test-space# stat myfile
  File: 'myfile'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228284     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 03:26:42.378185145 -0700
Modify: 1998-12-01 05:10:00.000000000 -0800
Change: 2019-09-08 03:26:42.378185145 -0700
```

تغییر زمان اصلاح: `touch -m fileName`

```
root@ubuntu16-1:~/test-space# touch -m myfile
```

فقط زمان آخرین اصلاح را به‌روزرسانی می‌کند.

```
root@ubuntu16-1:~/test-space# stat myfile 
  File: 'myfile'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228284     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 03:26:42.378185145 -0700
Modify: 2019-09-08 03:28:06.314537292 -0700
Change: 2019-09-08 03:28:06.314537292 -0700
 Birth: -
```

Explicitly Set the Access and Modification times: `touch -c -t YYDDHHMM filename`

Update access and modification time:`touch -c -d fileName`

ما از برچسب زمانی فایل دیگری با گزینه -r استفاده می‌کنیم: `touch -r refrence_file_name file_name`

```
root@ubuntu16-1:~/test-space# stat file1
  File: 'file1'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228281     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 02:44:27.744736670 -0700
Modify: 2019-09-08 02:44:27.744736670 -0700
Change: 2019-09-08 02:44:27.744736670 -0700
 Birth: -
root@ubuntu16-1:~/test-space# touch -r file1 myfile 
root@ubuntu16-1:~/test-space# stat myfile 
  File: 'myfile'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228284     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 02:44:27.744736670 -0700
Modify: 2019-09-08 02:44:27.744736670 -0700
Change: 2019-09-08 03:34:14.724762374 -0700
 Birth: -
```

and many more examples.

### mkdir

دستور mkdir در لینوکس به کاربر اجازه می‌دهد دایرکتوری‌ها را ایجاد کند (در برخی سیستم‌عامل‌ها به عنوان پوشه یا folder نیز شناخته می‌شوند).

```
mkdir [options...] [directories ...]
```

```
root@ubuntu16-1:~/test-space# mkdir dir1
root@ubuntu16-1:~/test-space# ls -l
total 4
drwxr-xr-x 2 root root 4096 Sep  8 03:44 dir1
```

این دستور می‌تواند چندین دایرکتوری را به طور همزمان ایجاد کرده و همچنین مجوزها را برای دایرکتوری‌ها تنظیم کند. مهم است توجه داشته باشید که کاربر اجراکننده این دستور باید مجوزهای کافی برای ایجاد دایرکتوری در دایرکتوری والد را داشته باشد، در غیر این صورت ممکن است با خطای ‘permission denied’ مواجه شود.

```
root@ubuntu16-1:~/test-space# mkdir --help
Usage: mkdir [OPTION]... DIRECTORY...
Create the DIRECTORY(ies), if they do not already exist.

Mandatory arguments to long options are mandatory for short options too.
  -m, --mode=MODE   set file mode (as in chmod), not a=rwx - umask
  -p, --parents     no error if existing, make parent directories as needed
  -v, --verbose     print a message for each created directory
  -Z                   set SELinux security context of each created directory
                         to the default type
      --context[=CTX]  like -Z, or if CTX is specified then set the SELinux
                         or SMACK security context to CTX
      --help     display this help and exit
      --version  output version information and exit

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
Full documentation at: <http://www.gnu.org/software/coreutils/mkdir>
or available locally via: info '(coreutils) mkdir invocation'
```

بیایید برخی از مفیدترین سوئیچ‌ها را امتحان کنیم، `-p` دستور را قادر می‌سازد تا در صورت لزوم دایرکتوری‌های والد (parent) را ایجاد کند. اگر دایرکتوری‌ها وجود داشته باشند، خطایی داده نمی‌شود:

```
root@ubuntu16-1:~/test-space# mkdir -p dir1/floor1/floor2
root@ubuntu16-1:~/test-space# tree
.
└── dir1
    └── floor1
        └── floor2

3 directories, 0 files
```

ما می‌توانیم یک دایرکتوری ایجاد کنیم و مجوزها را برای آن دایرکتوری به طور همزمان با استفاده از گزینه -m تنظیم کنیم:

```
root@ubuntu16-1:~/test-space# mkdir -m777 dir2 
root@ubuntu16-1:~/test-space# ls -l
total 8
drwxr-xr-x 3 root root 4096 Sep  8 03:57 dir1
drwxrwxrwx 2 root root 4096 Sep  8 04:09 dir2
```

The syntax of the mode is the same as the chmod command (will be discussed later).

```
root@ubuntu16-1:~/test-space# mkdir -v -m o=--- dir3
mkdir: created directory 'dir3'
root@ubuntu16-1:~/test-space# ls -l
total 12
drwxr-xr-x 3 root root 4096 Sep  8 03:57 dir1
drwxrwxrwx 2 root root 4096 Sep  8 04:09 dir2
drwxr-x--- 2 root root 4096 Sep  8 04:14 dir3
```

با گزینه `-v` پیامی برای هر دایرکتوری ایجاد شده چاپ می‌شود.

### cp

دستور `cp` یک ابزار خط فرمان برای کپی کردن فایل‌ها و دایرکتوری‌ها است. \[کپی‌های فایل‌ها مستقل از فایل اصلی هستند (برخلاف دستور `mv`)]. سینتکس اصلی دستور cp به این صورت است:

```
cp [options….] source(s) destination
```

> cp می‌تواند 1 یا چند منبع (source) بگیرد اما فقط یک مقصد (destination).

It supports copying one or more files or directories with options for taking backups and preserving attributes.

فراموش نکنید که هنگام استفاده از دستور cp انواع منبع و مقصد (فایل یا دایرکتوری) را در نظر بگیرید.

* اگر هدف یک دایرکتوری موجود باشد، تمام منابع به داخل هدف کپی می‌شوند. 
* اگر هدف دایرکتوری باشد که وجود ندارد، آنگاه منبع (واحد) نیز باید یک دایرکتوری باشد و کپی از دایرکتوری منبع و محتویات آن با نام هدف به عنوان نام جدید ساخته می‌شود. 
* اگر هدف یک فایل باشد، آنگاه منبع (واحد) نیز باید یک فایل باشد و کپی از فایل منبع با نام هدف ساخته می‌شود و جایگزین هر فایل موجود با همان نام می‌شود.

lets do some examples:

```
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── floor1
│       └── floor2
├── dir2
├── dir3
└── file1

5 directories, 1 file
```

**کپی کردن یک فایل:**

```
root@ubuntu16-1:~/test-space# cp file1 file2
root@ubuntu16-1:~/test-space# ls
dir1  dir2  dir3  file1  file2
```

**تهیه نسخه پشتیبان هنگام کپی کردن یک فایل**

اگر عملیات کپی باعث بازنویسی یک فایل شود، می‌توان از پرچم `-b` برای ایجاد نسخه پشتیبان از فایل استفاده کرد. این کار فایل را در محل کپی کرده و یک فایل پشتیبان می‌نویسد.

```
root@ubuntu16-1:~/test-space# ls
dir1  dir2  dir3   file1  file2  
root@ubuntu16-1:~/test-space# echo "this is file2 content" > file2
root@ubuntu16-1:~/test-space# echo "ehis is file1 content" > file1
root@ubuntu16-1:~/test-space# cp -b file1 file2
root@ubuntu16-1:~/test-space# ls
dir1  dir2  dir3   file1  file2  file2~ 
root@ubuntu16-1:~/test-space# cat file2
ehis is file1 content
root@ubuntu16-1:~/test-space# cat file2~
this is file2 content
```

برای تعیین نام فایل پشتیبان از گزینه `-S` استفاده کنید. `cp -S .file2bak file1 file2` را امتحان کنید.

**کپی کردن چندین فایل (به درون یک دایرکتوری)**

```
root@ubuntu16-1:~/test-space# cp file1 file2 dir2/
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── floor1
│       └── floor2
├── dir2
│   ├── file1
│   └── file2
├── dir3
├── file1
├── file2
└── file2~


5 directories, 4 files
```

**کپی کردن فایل‌ها به صورت بازگشتی (recursively)**

ما می‌توانیم از cp برای کپی کردن کل ساختارهای دایرکتوری از یک مکان به مکان دیگر با استفاده از گزینه `-R` برای انجام کپی بازگشتی استفاده کنیم.

فرض کنید شما کاربر root هستید و دایرکتوری /home/test-space را دارید که شامل فایل‌ها و زیردایرکتوری‌های بسیاری است. می‌خواهید تمام آن فایل‌ها و تمام زیردایرکتوری‌ها را به مکان جدیدی به نام /root/files-backup کپی کنید. می‌توانید همه آن‌ها را با استفاده از دستور زیر کپی کنید:

`cp -R ~/test-space ~/files-backup`

* اگر دایرکتوری files-backup از قبل وجود داشته باشد، دایرکتوری test-space در داخل آن قرار خواهد گرفت.
* اگر files-backup از قبل وجود نداشته باشد، ایجاد خواهد شد و محتویات دایرکتوری test-space در داخل آن قرار می‌گیرد.

**کپی کردن یک دایرکتوری**

به طور پیش‌فرض دستور cp دایرکتوری‌ها را کپی نمی‌کند. تلاش برای کپی کردن یک دایرکتوری منجر به خطا می‌شود.

```
root@ubuntu16-1:~/test-space# cp dir2 dir4
cp: omitting directory 'dir2'
```

برای کپی کردن یک دایرکتوری، پرچم `-R` را اضافه کنید. این کار به صورت بازگشتی یک پوشه را کپی کرده و یک کپی ایجاد می‌کند.

```
root@ubuntu16-1:~/test-space# cp -R dir1 NewDirectoryA
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── floor1
│       └── floor2
├── dir2
├── dir3
├── file1
├── file2
├── file2~
└── NewDirectoryA
    └── floor1
        └── floor2

8 directories, 5 files
```

**کپی کردن چندین دایرکتوری**

برای کپی کردن چندین دایرکتوری، مسیر دایرکتوری‌هایی که قرار است کپی شوند را داده و به دنبال آن دایرکتوری مقصد را بیاورید.

```
root@ubuntu16-1:~/test-space# cp -R dir1 dir2 NewDirectoryB
cp: target 'NewDirectoryB' is not a directory
root@ubuntu16-1:~/test-space# mkdir NewDirectoryB
root@ubuntu16-1:~/test-space# cp -R dir1 dir2 NewDirectoryB
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── floor1
│       └── floor2
├── dir2
│   ├── file1
│   └── file2
├── dir3
├── file1
├── file2
├── file2~
├── NewDirectoryA
│   └── floor1
│       └── floor2
└── NewDirectoryB
    ├── dir1
    │   └── floor1
    │       └── floor2
    └── dir2
        ├── file1
        └── file2

13 directories, 7 files
```

اکنون بیایید نگاهی به برخی گزینه‌های دستور cp بیندازیم:

| cp command useful options: | Description                                                                                                                                                                                                                                        |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -v,--verbose               | Verbose mode; explain what is being done.                                                                                                                                                                                                          |
| --preserve                 | preserve file attributes (permissions, group and user owernship).By default mode, ownership and timestamps will be preserved.                                                                                                                      |
| -i , --interactive         | Prompt before overwrite (overrides a previous -n option).                                                                                                                                                                                          |
| -n , --no-clobber          | Do not overwrite an existing file. If -i/--interactive was previously specified, this option overrides it. This option cannot be specified with -b/--backup, because backups are only created when a file would have been overwritten.             |
| -f,--force                 | If an existing destination file cannot be opened, remove it and try again. This option has no effect if the -n/--no-clobber option is used. However, it applies independently of -i/--interactive; neither option cancels the effect of the other. |

cp command has lots of options.Try `man cp` command for more information.

### mv

دستور `mv` برای **جابجایی یا تغییر نام یک یا چند فایل یا دایرکتوری** استفاده می‌شود. به طور کلی، نام‌هایی که می‌توانیم استفاده کنیم از همان قوانین کپی کردن با cp پیروی می‌کنند؛ \[اگر فایلی را در همان سیستم‌فایل جابجا می‌کنید، inode تغییر نخواهد کرد]. 

* تغییر نام یک فایل (یا دایرکتوری) از منبع (source) به مقصد (destination):

```
mv [options]  source destination
```

* جابجایی فایل(های) منبع یا دایرکتوری(ها) به دایرکتوری به نام مقصد:

```
mv [options] source [source2 ...] destination
```

همانند سینتکس قبلی، اما مشخص کردن دایرکتوری در ابتدا و فایل(های) منبع در انتها:

```
mv [options] -t destination source [source2 ...]
```

بیایید دستور mv را امتحان کنیم:

```
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
├── dir2/
└── file1
```

**تغییر نام یک فایل یا دایرکتوری:**

```
root@ubuntu16-1:~/test-space# mv file1 NewFile1
root@ubuntu16-1:~/test-space# mv dir1 dirA
root@ubuntu16-1:~/test-space# tree -F
.
├── dir2/
├── dirA/
├── file2
└── NewFile1
```

**جابجایی فایل‌ها به درون یک دایرکتوری:**

```
root@ubuntu16-1:~/test-space# mv NewFile1 file2 dir3
mv: target 'dir3' is not a directory
root@ubuntu16-1:~/test-space# mkdir dir3
root@ubuntu16-1:~/test-space# mv NewFile1 file2 dir3
root@ubuntu16-1:~/test-space# tree -F
.
├── dir2/
├── dir3/
│   ├── file2
│   └── NewFile1
└── dirA/

3 directories, 2 files
```

**جابجایی یک دایرکتوری به درون دیگری :**

```
root@ubuntu16-1:~/test-space# mv dir3 dirA
root@ubuntu16-1:~/test-space# tree -F
.
├── dir2/
└── dirA/
    └── dir3/
        ├── file2
        └── NewFile1

3 directories, 2 files
```

ما می‌توانستیم از دستور `mv -t dirA dir3` نیز استفاده کنیم. گزینه `-t, --target-directory` تمام منابع را به دایرکتوری مقصد منتقل می‌کند.

گزینه‌های مفید دستور mv:

| mv command options | description                                               |
| ------------------ | --------------------------------------------------------- |
| -v                 | verbose - print source and destination files              |
| -i                 | interactive prompt before overwrite                       |
| -u                 | update - move when source is newer than destination       |
| -f                 | force move by overwriting destination file without prompt |

> #### cp در مقابل mv:
>
> به طور معمول، دستور cp اگر فایل موجود قابل نوشتن باشد، یک فایل را روی کپی موجود کپی می‌کند. از سوی دیگر، mv اگر هدف وجود داشته باشد، فایل را جابجا یا تغییر نام نمی‌دهد. ما می‌توانیم با استفاده از سوئیچ `-f` بر این موضوع غلبه کنیم.

### rm

`rm` در اینجا مخفف **remove** است. `rm` فایل‌ها یا دایرکتوری‌ها را حذف می‌کند.

```
rm [OPTION]... FILE...
```

بیایید امتحان کنیم:

```
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
├── dir2/
│   └── MyFile
└── file1

2 directories, 2 files
```

**حذف فایل‌ها**

```
root@ubuntu16-1:~/test-space# rm file1
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
└── dir2/
    └── MyFile

2 directories, 1 file
```

{% hint style="info" %}
**نام فایل‌هایی که با خط تیره شروع می‌شوند، چگونه آن‌ها را حذف کنیم؟**

برای حذف فایلی که نام آن با خط تیره ("-") شروع می‌شود، می‌توانید یک دو خط تیره ("--") را به طور جداگانه قبل از نام فایل مشخص کنید. این خط تیره اضافی لازم است تا rm نام فایل را به عنوان یک گزینه تفسیر نکند: `rm -- -file.txt` یا می‌توانیم با ارجاع به آن با یک مسیر، آن را حذف کنیم: `rm /home/hope/-file.txt`
{% endhint %}

برخی گزینه‌های دیگر دستور rm:

| mv options | description                                                  |
| ---------- | ------------------------------------------------------------ |
| -v         | Verbose mode; explain at all times what is being done.       |
| -i         | prompt before every removal                                  |
| -f,--force | Ignore none existant files,and never prompt before removing. |
| -d,--dir   | Remove empty directories.                                    |

#### حذف دایرکتوری‌ها

```
root@ubuntu16-1:~/test-space# rm dir1
rm: cannot remove 'dir1': Is a directory
```

به طور پیش‌فرض، `rm` دایرکتوری‌ها را حذف نمی‌کند.

اگر دایرکتوری مشخص شده خالی باشد، ممکن است با گزینه -d/--dir حذف شود.

```
root@ubuntu16-1:~/test-space# rm -d dir1
root@ubuntu16-1:~/test-space# tree -F
.
└── dir2/
    └── MyFile

1 directory, 1 file
```

اگر دایرکتوری مورد نظر شامل فایل‌هایی باشد چه؟ اگر گزینه **-r/-R/--recursive** مشخص شده باشد، می‌توانیم آن دایرکتوری را حذف کنیم، اما rm هر دایرکتوری مطابقت یافته و محتویات آن‌ها را حذف خواهد کرد!

```
root@ubuntu16-1:~/test-space# rm -rf dir2
root@ubuntu16-1:~/test-space# tree -F
.

0 directories, 0 files
```

`rm -d` به ما اجازه می‌دهد بدون مشخص کردن **-r/-R/--recursive** یک دایرکتوری را حذف کنیم، به شرطی که دایرکتوری **خالی** باشد. به عبارت دیگر، **rm -d** معادل استفاده از **rmdir**.

### rmdir

حذف دایرکتوری‌ها با استفاده از دستور rmdir برعکس ایجاد آن‌ها است. ما می‌توانیم یک دایرکتوری را با `rmdir` حذف کنیم تنها در صورتی که **خالی** باشد زیرا **هیچ گزینه‌ای برای اجبار به حذف وجود ندارد**.

```
rmdir [options] directory ...
```

دوباره، یک گزینه `-p` برای حذف دایرکتوری‌های والد نیز وجود دارد. بیایید آن را امتحان کنیم:

```
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
│   └── file1
├── dir2/
└── dir3/
    └── dirA/
        └── dirB/

5 directories, 1 file
root@ubuntu16-1:~/test-space# rmdir dir1
rmdir: failed to remove 'dir1': Directory not empty
root@ubuntu16-1:~/test-space# rmdir dir2
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
│   └── file1
└── dir3/
    └── dirA/
        └── dirB/

4 directories, 1 file
root@ubuntu16-1:~/test-space# rmdir -p dir3/dirA/
root@ubuntu16-1:~/test-space# rmdir -p dir3/dirA/dirB/
root@ubuntu16-1:~/test-space# tree -F
.
└── dir1/
    └── file1

1 directory, 1 file
```

Normally, when`rmdir` is instructed to remove a non-empty directory, it reports an error. With `--ignore-fail-on-non-empty`option suppresses those error messages.

## کار با چندین فایل و دایرکتوری

گاهی اوقات نیاز داریم روی بیش از یک فایل کار کنیم، اکنون سعی می‌کنیم مروری بر برخی دستورات بازگشتی (recursive) داشته باشیم.

### عملیات بازگشتی (Recursive manipulation)

**لیست کردن بازگشتی**

دستور ls دارای گزینه -R (توجه کنید به R بزرگ) برای لیست کردن یک دایرکتوری و تمام زیردایرکتوری‌های آن است. گزینه بازگشتی فقط برای نام دایرکتوری‌ها اعمال می‌شود، به عنوان مثال در یک درخت دایرکتوری.

```
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── text1
├── dir2
├── file1
└── file2

2 directories, 3 files
root@ubuntu16-1:~/test-space# ls -R
.:
dir1  dir2  file1  file2

./dir1:
text1

./dir2:
```

**کپی بازگشتی**

ما می‌توانیم از گزینه -r (یا -R یا --recursive) استفاده کنیم تا دستور cp به دایرکتوری‌های منبع وارد شده و محتویات را به صورت بازگشتی کپی کند. برای جلوگیری از بازگشت نامحدود، نمی‌توانیم دایرکتوری منبع را در خودش کپی کنیم!

```
root@ubuntu16-1:~# cp -R test-space NewCopy
root@ubuntu16-1:~# tree NewCopy/
NewCopy/
├── dir1
│   └── text1
├── dir2
├── file1
└── file2

2 directories, 3 files
```

**حذف بازگشتی**

قبلاً اشاره کردم که _rmdir فقط دایرکتوری‌های **خالی** را حذف می‌کند_. ما می‌توانیم از گزینه `-r` (یا -R یا --recursive) استفاده کنیم تا دستور rm هم فایل‌ها و هم دایرکتوری‌ها را حذف کند:

```
root@ubuntu16-1:~# ls
client.conf             NewCopy      unzip_6.0-20ubuntu1_amd64.deb
files-backup            openvpn.key  zip_3.0-11_amd64.deb
jcal_0.4.1-2_amd64.deb  test-space
root@ubuntu16-1:~# rm -rf NewCopy/
root@ubuntu16-1:~# ls
client.conf             openvpn.key                    zip_3.0-11_amd64.deb
files-backup            test-space
jcal_0.4.1-2_amd64.deb  unzip_6.0-20ubuntu1_amd64.deb
```

## کاراکترهای عمومی و گلابینگ (Wildcards and Globbing)

**گلابینگ فایل (File globbing) ویژگی است که توسط شل یونیکس/لینوکس برای نمایش چندین نام فایل با استفاده از کاراکترهای خاص به نام Wildcards به جای یک نام فایل واحد ارائه می‌شود.** یک wildcard اساساً نمادی است که ممکن است برای جایگزینی یک یا چند کاراکتر استفاده شود. بنابراین، ما می‌توانیم از wildcardها برای تولید ترکیب مناسب نام فایل‌ها طبق نیاز خود استفاده کنیم.

```
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep 28 00:26 aaa.txt
-rw-r--r-- 1 root root 0 Sep 28 00:27 ababa.txt
-rw-r--r-- 1 root root 0 Sep 28 00:26 bbbb.txt
-rw-r--r-- 1 root root 0 Sep 27 23:33 file1
-rw-r--r-- 1 root root 0 Sep 27 23:33 file2
```

* `?` **برای مطابقت با هر تک کاراکتر استفاده می‌شود. **ما می‌توانیم از ‘?’ چندین بار برای مطابقت با چندین کاراکتر استفاده کنیم.

```
root@ubuntu16-1:~/test-space# ls ???.txt
aaa.txt
```

* `*`** برای مطابقت با صفر یا چند کاراکتر استفاده می‌شود.** اگر اطلاعات کمی برای جستجوی هر فایل یا اطلاعاتی داریم، می‌توانیم از ‘*’ در الگوی گلابینگ استفاده کنیم.

```
root@ubuntu16-1:~/test-space# ls *.txt
aaa.txt  ababa.txt  bbbb.txt
```

* `[ ]`**برای مطابقت با کاراکترهایی از یک محدوده استفاده می‌شود. برخی از پرکاربردترین اعلام‌های محدوده در زیر ذکر شده است:**

`[A-Z]` : All uppercase alphabets

`[a-z]` : All lowercase alphabets

`[a-zA-Z0-9]` : All uppercase alphabets, lowercase alphabet and digits

> کاراکتر `-` بین دو کاراکتر دیگر نشان‌دهنده محدوده‌ای است که شامل آن دو کاراکتر و تمام کاراکترهای بین آن‌ها در توالی مرتب‌سازی (collating sequence) می‌شود.

```
root@ubuntu16-1:~/test-space# ls [e-z]*
file1  file2
```

* **کاراکتر** `!` **به معنای NOT است، بنابراین با هر کاراکتری به جز کاراکترهای باقی‌مانده مطابقت دارد**.

```
root@ubuntu16-1:~/test-space# ls [!e-z]*
aaa.txt  ababa.txt  bbbb.txt
```

* `{ }`**می‌تواند برای مطابقت با نام فایل‌ها با بیش از یک الگوی گلابینگ استفاده شود. هر الگو با ‘,’ در داخل آکولاد بدون هیچ فاصله‌ای جدا می‌شود.**

```
root@ubuntu16-1:~/test-space# ls {???.txt,????.txt}
aaa.txt  bbbb.txt
```

`rm {*.doc,*.docx}` : تمام فایل‌هایی را که پسوند آن‌ها ‘doc’ یا ‘docx’ است حذف می‌کند.

* **و در نهایت ` \ ` به عنوان یک کاراکتر "گریز" (escape) استفاده می‌شود**، ما از آن برای محافظت از یک کاراکتر خاص بعدی استفاده کرده‌ایم. مثال: "\\\\" به دنبال یک بک‌اسلش می‌گردد.

> ما می‌توانیم گلابینگ را با استفاده از دستور `set -f` غیرفعال کنیم.

{% hint style="danger" %}
**الگوهای Wildcard در مقابل Regular Expressions**

الگوهای Wildcard و الگوهای regular expression برخی ویژگی‌های مشترک دارند، اما یکسان نیستند. به دقت توجه کنید.
{% endhint %}

اکنون که موضوع فایل و دایرکتوری را با چکش بزرگ بازگشتی که به همه چیز ضربه می‌زند، و چکش گلابینگ که انتخابی‌تر ضربه می‌زند پوشش دادیم، بیایید به دستور find نگاه کنیم که می‌تواند بیشتر شبیه چاقوی جراحی باشد.

### یافتن فایل‌ها

دستور find برای یافتن فایل‌ها در یک یا چند درخت دایرکتوری، بر اساس معیارهایی مانند نام، برچسب زمانی یا اندازه استفاده می‌شود.

### find

دستور find با استفاده از تمام یا بخشی از نام، یا بر اساس معیارهای جستجوی دیگر مانند اندازه، نوع، مالک فایل، تاریخ ایجاد یا آخرین تاریخ دسترسی، به دنبال فایل‌ها یا دایرکتوری‌ها می‌گردد.

```
find starting/path options  expression
```

1. ویژگی `starting/path` دایرکتوری سطح بالایی را که find فیلتر کردن را از آنجا شروع می‌کند، تعریف می‌کند.
2. ویژگی `options` رفتار و روش بهینه‌سازی فرآیند find را کنترل می‌کند.
3.  ویژگی `expression` تست‌هایی را کنترل می‌کند که سلسله‌مراتب دایرکتوری را برای تولید خروجی جستجو می‌کنند.

    ابتدایی‌ترین find جستجو بر اساس نام یا بخشی از یک نام است:

```
root@ubuntu16-1:~/test-space# find /etc/ -iname "[y-z]*"
/etc/xdg/autostart/zeitgeist-datahub.desktop
/etc/zsh_command_not_found
/etc/vmware-tools/messages/zh_TW
/etc/vmware-tools/messages/zh_CN
/etc/dhcp/dhclient-exit-hooks.d/zzz_avahi-autoipd
/etc/kernel/postrm.d/zz-update-grub
/etc/kernel/postinst.d/zz-update-grub
/etc/brltty/Contraction/zu.ctb
/etc/brltty/Contraction/zh-tw-ucb.ctb
/etc/brltty/Contraction/zh-tw.ctb
```

گزینه `-name` برای جستجوی فایل‌ها بر اساس نام آن‌ها استفاده می‌شود. `-i` آن را نسبت به حالت حروف (بزرگ و کوچک) حساس نمی‌سازد. In the first example above, we found both files and a directory (/etc). 

{% hint style="info" %}
**یافتن فایل‌های مخفی : **اگر می‌خواهید فایل یا دایرکتوری را پیدا کنید که نام آن با نقطه شروع می‌شود، مانند .bashrc یا دایرکتوری فعلی (.)، باید یک نقطه ابتدایی را به عنوان بخشی از الگو مشخص کنید. در غیر این صورت، جستجوهای نام این فایل‌ها یا دایرکتوری‌ها را نادیده می‌گیرند. `find . -name ".*"`
{% endhint %}

توجه: اگر می‌خواهید نتایج مختلف را به هم زنجیره کنید، می‌توانید از دستورات “-and” یا “-or” استفاده کنید. اگر “-and” حذف شود، فرض می‌شود. `find . -name file1 -or -name file9`

**یافتن فایل‌ها بر اساس نوع**

ما می‌توانیم نوع فایل‌هایی را که می‌خواهیم پیدا کنیم با پارامتر “-type” مشخص کنیم. به این صورت کار می‌کند:

```
find -type type_descriptor query
```

* `-type f` به دنبال یک فایل معمولی می‌گردد
* `-type d` به دنبال یک دایرکتوری می‌گردد
* `-type l` به دنبال یک لینک نمادین می‌گردد

```
root@ubuntu16-1:~/test-space# find /etc/ -type f -iname "[y-z]*"
/etc/xdg/autostart/zeitgeist-datahub.desktop
/etc/zsh_command_not_found
/etc/dhcp/dhclient-exit-hooks.d/zzz_avahi-autoipd
/etc/kernel/postrm.d/zz-update-grub
/etc/kernel/postinst.d/zz-update-grub
/etc/brltty/Contraction/zu.ctb
/etc/brltty/Contraction/zh-tw-ucb.ctb
/etc/brltty/Contraction/zh-tw.ctb
```

**یافتن فایل‌ها بر اساس اندازه**

ما همچنین می‌توانیم بر اساس اندازه فایل جستجو کنیم، یا برای یک اندازه خاص (n) یا برای فایل‌هایی که بزرگتر (+n) یا کوچکتر از یک مقدار داده شده (-n) هستند. با استفاده از هر دو کران بالا و پایین اندازه، می‌توانیم فایل‌هایی را پیدا کنیم که اندازه آن‌ها در یک محدوده خاص است. به طور پیش‌فرض، گزینه -size در find واحد ‘b’ را برای بلوک‌های 512 بایتی فرض می‌کند.

* `-size  +/- [b] [c] [w] [k] [M} [G]`

```
b     for 512-byte blocks (this is the default if no suffix is used)
c     for bytes
w     for two-byte words
k     for Kilobytes (units of 1024 bytes)
M     for Megabytes (units of 1048576 bytes)
G     for Gigabytes (units of 1073741824 bytes)
```

به عنوان مثال بیایید فایل‌های کوچکتر از 1 کیلوبایت را پیدا کنیم:

```
root@ubuntu16-1:~/test-space# find /etc/ -type f -size -1k
/etc/dictionaries-common/ispell-default
/etc/cups/printers.conf
/etc/vmware-caf/pme/config/_previous_/cafenv.config
/etc/.pwd.lock
/etc/apt/trusted.gpg.d/peek-developers_ubuntu_stable.gpg~
/etc/security/opasswd
/etc/gconf/gconf.xml.defaults/%gconf-tree.xml
/etc/gconf/gconf.xml.mandatory/%gconf-tree.xml
/etc/sensors.d/.placeholder
/etc/newt/palette.original
```

ما می‌توانیم تمام فایل‌های خالی را با استفاده از `find . -size 0b` یا `find . -empty` پیدا کنیم.

**یافتن فایل‌ها بر اساس زمان آن‌ها:**

لینوکس داده‌های زمانی مربوط به زمان‌های دسترسی، اصلاح و تغییر را ذخیره می‌کند.

* **زمان دسترسی (Access Time)**: Last time a file was read or written to.
* **زمان اصلاح (Modification Time)**: Last time the contents of the file were modified.
* **زمان تغییر (Change Time)**: Last time the file’s inode meta-data was changed.

ما می‌توانیم از برچسب‌های زمانی توضیح داده شده با دستور touch برای مکان‌یابی فایل‌هایی که دارای برچسب‌های زمانی خاصی هستند استفاده کنیم.

```
-amin n: The file was last accessed n minutes ago
-anewer: The file was last accessed more recently than it was modified
-atime n: The file was last accessed more n days ago
-cmin n: The file was last changed n minutes ago
-cnewer: The file was last changed more recently than the file was modified
-ctime n: The file was last changed more than n days ago
-mmin n: File's data was last modified n minutes ago
-mtime n: File's data was last modified n days ago
```

دوباره علائم (+/-) می‌توانند برای دادن محدوده به آن استفاده شوند.

یافتن فایل‌های تغییر یافته در 2 ساعت گذشته:

```
root@ubuntu16-1:~/test-space# find /home -cmin -120
/home/user1/.cache/upstart
/home/user1/.cache/upstart/indicator-printers.log
/home/user1/.cache/upstart/unity-panel-service.log
```

توجه: اضافه کردن گزینه `-daystart` به `-mtime` یا `-atime` به این معنی است که ما می‌خواهیم روزها را به عنوان روزهای تقویمی، از نیمه‌شب شروع کنیم. بنابراین برای لیست کردن فایل‌های معمولی در دایرکتوری خانگی شما که دیروز اصلاح شده‌اند، می‌توانیم از `find ~/ -daystart -type f -mtime 1` استفاده کنیم.

> ما همچنین می‌توانیم فایل‌ها را بر اساس مالک و مجوزها پیدا کنیم و نتیجه را بر اساس عمق (depth) فیلتر کنیم (در بخش‌های بعدی "104-7" مورد بحث قرار خواهد گرفت)

اقدام روی فایل‌ها با دو سوئیچ دیگر:

| find command switch | meaning                                                 |
| ------------------- | ------------------------------------------------------- |
| -ls                 | list current file in ls -dils format on standard output |
| -print              | print the full file name on the standard output         |

As you can see find command has tons of options, get into more details using man page files.

#### اجرا و ترکیب دستورات Find با exec-

ما می‌توانیم یک دستور کمکی دلخواه را روی هر چیزی که find مطابقت می‌دهد با استفاده از پارامتر “-exec” اجرا کنیم. این به این صورت فراخوانی می‌شود:

```
find find_parameters -exec command_and_params {} \;
```

`{}` به عنوان جایگاهی برای فایل‌هایی که find مطابقت می‌دهد استفاده می‌شود. “\;” به این دلیل استفاده می‌شود که find بداند دستور کجا تمام می‌شود.

به عنوان مثال این دستور تمام فایل‌های خالی را حذف می‌کند:

```
find . -empty -exec rm {} \;
```

ما می‌توانیم تمام فایل‌های خالی را در این دایرکتوری و زیردایرکتوری‌های آن حذف کنیم:

```
find . -empty -exec rm '{}' \;
```

سپس می‌توانیم مجوزهای دایرکتوری را به این صورت تغییر دهیم:

```
find . -name "*.deb" -exec cp {} /tmp/packages \;
```

## شناسایی فایل‌ها <a href="identifying-files" id="identifying-files"></a>

نام فایل‌ها اغلب دارای پسوندی مانند gif، jpeg یا html هستند که اشاره‌ای به محتوای احتمالی فایل می‌دهند. لینوکس به چنین پسوندهایی نیاز ندارد و به طور کلی از آن‌ها برای شناسایی نوع فایل استفاده نمی‌کند. دانستن اینکه با چه نوع فایلی سر و کار دارید به شما کمک می‌کند بدانید از چه برنامه‌ای برای نمایش یا دستکاری آن استفاده کنید.

### file

دستور file چیزی در مورد نوع داده‌های موجود در یک یا چند فایل به ما می‌گوید.

```
file [option] [filename]
```

```
root@ubuntu16-1:~/test-space# ls -l
total 10880
-rwxrw-rw- 1 user1 user1 10819475 Mar 29  2015 01 - Intro.mp4
-rw------- 1 user1 user1   221941 Sep  1 00:24 zabbix-cli-manual-1.7.0.pdf
-rw------- 1 user1 user1    91087 Aug 31 00:40 zabbix-cli-rpm-2.1.1-1.tar.gz

root@ubuntu16-1:~/test-space# file 01\ -\ Intro.mp4 
01 - Intro.mp4: ISO Media, MP4 Base Media v1 [IS0 14496-12:2003]
```

`-b, –brief` : این گزینه برای نمایش فقط نوع فایل در حالت مختصر (brief) استفاده می‌شود.

```
root@ubuntu16-1:~/test-space# file  -b zabbix-cli-manual-1.7.0.pdf 
PDF document, version 1.4
```

`-z` : سعی می‌کند داخل فایل‌های فشرده شده را نگاه کند.

```
root@ubuntu16-1:~/test-space# file -z zabbix-cli-rpm-2.1.1-1.tar.gz zabbix-cli-rpm-2.1.1-1.tar.gz:
POSIX tar archive (gzip compressed data, from Unix)
```

و گزینه `-i` برای مشاهده mime type فایل.

{% hint style="success" %}
MIME type برچسبی است که برای شناسایی نوع داده استفاده می‌شود. این برچسب به این دلیل استفاده می‌شود که نرم‌افزار بداند چگونه داده‌ها را مدیریت کند. این همان هدفی را در اینترنت دنبال می‌کند که پسوند فایل‌ها در مایکروسافت ویندوز دنبال می‌کنند.
{% endhint %}

### آرشیو کردن و فشرده‌سازی فایل‌ها

آرشیو کردن و فشرده‌سازی دو چیز متفاوت هستند.

![](.gitbook/assets/performbasicfile-archcompress.jpg)

* tar فقط فایل‌ها را آرشیو می‌کند و به طور پیش‌فرض هیچ فشرده‌سازی انجام نمی‌دهد.
* zip هم آرشیو کردن و هم فشرده‌سازی را انجام می‌دهد.
* gzip و bzip2 فقط برای فشرده‌سازی استفاده می‌شوند.

![](.gitbook/assets/performbasicfile-compratio.jpg)

### zip

`Zip` یکی از محبوب‌ترین فرمت‌های فایل آرشیو است. با zip، می‌توانید چندین فایل را در یک فایل فشرده کنید. این کار نه تنها فضای دیسک را ذخیره می‌کند، بلکه پهنای باند شبکه را نیز ذخیره می‌کند. به همین دلیل است که تقریباً همیشه با فایل‌های zip مواجه خواهید شد.

```
zip options archive inpath inpath ...
```

بیایید نگاهی بیندازیم:

```
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt

2 directories, 5 files

root@ubuntu16-1:~/test-space/myfiles# zip myconf.zip myconf.txt 
  adding: myconf.txt (deflated 60%)

root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
├── myconf.zip
└── mylog.txt

2 directories, 6 files
```

از گزینه `-r` با دستور zip استفاده کنید تا فایل‌های یک دایرکتوری را به صورت بازگشتی زیپ کند. این گزینه به شما کمک می‌کند تا تمام فایل‌های موجود در دایرکتوری مشخص شده را زیپ کنید:

```
root@ubuntu16-1:~/test-space/myfiles# cd ..
root@ubuntu16-1:~/test-space# zip -r myfiles.zip myfiles/
  adding: myfiles/ (stored 0%)
  adding: myfiles/dir1/ (stored 0%)
  adding: myfiles/dir1/myvideo.mp4 (deflated 71%)
  adding: myfiles/etc/ (stored 0%)
  adding: myfiles/dir2/ (stored 0%)
  adding: myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz (deflated 0%)
  adding: myfiles/dir2/zabbix-cli-manual-1.7.0.pdf (deflated 88%)
  adding: myfiles/mylog.txt (deflated 89%)
  adding: myfiles/myconf.txt (deflated 60%)
  adding: myfiles/myconf.zip (stored 0%)
```

### unzip

یک برنامه همراه جداگانه به نام unzip، آرشیوهای zip را باز و از حالت فشرده خارج می‌کند.

```
root@ubuntu16-1:~/test-space# rm -rf myfiles
root@ubuntu16-1:~/test-space# unzip myfiles.zip 
Archive:  myfiles.zip
   creating: myfiles/
   creating: myfiles/dir1/
  inflating: myfiles/dir1/myvideo.mp4  
   creating: myfiles/etc/
   creating: myfiles/dir2/
  inflating: myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz  
  inflating: myfiles/dir2/zabbix-cli-manual-1.7.0.pdf  
  inflating: myfiles/mylog.txt       
  inflating: myfiles/myconf.txt      
 extracting: myfiles/myconf.zip   
```

| zip command example                     | Description                                     |
| --------------------------------------- | ----------------------------------------------- |
| zip –v filename.zip file1.txt           |  Verbose mode or print diagnostic version info. |
| zip –d filename.zip file.txt            |  Removes the file from the zip archive.         |
| zip –u filename.zip file.txt            | Updates the file in the zip archive.            |
| zip –m filename.zip file.txt            | Deletes the original files after zipping.       |
| zip –x filename.zip file_to_be_excluded | Exclude the files in creating the zip.          |

### فشرده‌سازی فایل‌ها (Compressing files)

### gzip

Gzip (GNU zip) یک ابزار فشرده‌سازی است که برای کاهش حجم فایل استفاده می‌شود. 

```
gzip [OPTION]... [FILE]...
```

**به طور پیش‌فرض** فایل اصلی با فایل فشرده شده با پسوند (.gz) جایگزین می‌شود و gzip پس از ایجاد فایل فشرده، فایل‌های اصلی را حذف می‌کند. gzip مود فایل، مالکیت و برچسب زمانی اصلی را حفظ می‌کند.

```
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt

2 directories, 5 files
root@ubuntu16-1:~/test-space/myfiles# gzip mylog.txt 
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt.gz

2 directories, 5 files
```

### gunzip

برای خارج کردن یک فایل از حالت فشرده می‌توانیم از دستور gunzip استفاده کنیم و فایل اصلی شما باز خواهد گشت.

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 136
drwxr-xr-x 2 root root   4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root   4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root   2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 123925 Sep 30 05:41 mylog.txt.gz
root@ubuntu16-1:~/test-space/myfiles# gunzip mylog.txt.gz 
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
```

همچنین می‌توانیم از گزینه -d برای خارج کردن یک فایل از حالت فشرده با استفاده از دستور “gzip” استفاده کنیم. مثال: `gzip -d mydoc.gz`

| gzip command example | Description                                                                                                                                                                    |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| gzip -r testfolder   | compress every file in a folder and its subfolders                                                                                                                             |
| gzip -k mydoc.txt    | compress the file and keep the original file                                                                                                                                   |
| gzip -f myfile1.txt  | fore to compress already compressed file                                                                                                                                       |
| gzip -v mydoc.txt    | displays the name and percentage reduction for each file compressed or decompressed. we can use -l with compressed files.                                                      |
| gzip -L filename.gz  | displays the gzip license                                                                                                                                                      |
| gzip -9 mydoc.txt    | <p>-[1-9] option : It allows to change the compression level.</p><p>1 : fastest compression speed but lower ratio</p><p>9 : highest compression ratio but at a lower speed</p> |

### bzip2

مانند gzip، دستور bzip2 در لینوکس برای فشرده‌سازی و خارج کردن فایل‌ها از حالت فشرده استفاده می‌شود. این دستور از الگوریتم فشرده‌سازی متفاوتی استفاده می‌کند، بنابراین فایل‌ها را بهتر از gzip فشرده می‌کند، اما زمان بازگشت از حالت فشرده طولانی‌تر و مصرف حافظه بالاتری دارد.

```
bzip2 [OPTIONS] filenames ...
```

```
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt

2 directories, 5 files
root@ubuntu16-1:~/test-space/myfiles# bzip2 mylog.txt 
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt.bz2

2 directories, 5 files
```

### bunzip2

bunzip2 برای باز کردن فایل‌های bzip2 استفاده می‌شود:

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 92
drwxr-xr-x 2 root root  4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root  4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root  2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 79421 Sep 30 05:41 mylog.txt.bz2
root@ubuntu16-1:~/test-space/myfiles# bunzip2 mylog.txt.bz2 
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
```

همچنین گزینه **-d** برای خارج کردن فایل‌های فشرده از حالت فشرده استفاده می‌شود.

| bzip2 command example | Description                                                    |
| --------------------- | -------------------------------------------------------------- |
| bzip2 -k input.txt    | does compression but does not deletes the original file        |
| bzip2 -z input.txt    | forces compression. It is an opposite command of decompression |
| bzip2 -v input.txt    | show the compression ratio for each file processed             |
| bzip2 -L filename.gz  | -V , -L used to display the software version, license terms    |
| bzip2 -q input.txt    |  It will suppress non-essential warning messages.              |

bzip2 هیچ گزینه‌ای برای فشرده‌سازی یک دایرکتوری ندارد، بنابراین از tar همراه با آن استفاده کنید. چگونه؟ بخش tar را بخوانید.

### xz

xz یک ابزار فشرده‌سازی داده خط فرمان جدید و چند منظوره است، مشابه gzip و bzip2. می‌تواند برای فشرده‌سازی یا خارج کردن یک فایل از حالت فشرده بر اساس حالت عملیات انتخاب شده استفاده شود. این ابزار از فرمت‌های مختلفی برای فشرده‌سازی یا باز کردن فایل‌ها پشتیبانی می‌کند.

Selecting a compression utility to use will depend mainly on two factors, the compression speed and rate of a given tool. Unlike its counterparts, xz is not commonly used but offers the best compression.

```
root@ubuntu16-1:~/test-space/myfiles# ls
dir1  dir2  myconf.txt  mylog.txt
root@ubuntu16-1:~/test-space/myfiles# xz mylog.txt 
```

یا می‌توانیم از xz `-x` برای فشرده‌سازی فایل استفاده کنیم. `-d` برای باز کردن از حالت فشرده استفاده می‌شود:

```
root@ubuntu16-1:~/test-space/myfiles# xz -d mylog.txt.xz 
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
```

| xz command example | Description                                                                                                       |
| ------------------ | ----------------------------------------------------------------------------------------------------------------- |
| xz -k file.txt     | prevent deleting of the input file(s)                                                                             |
| xz -f file.txt.xz  | use the -f option to force the process(ex: compressed file already exist)                                         |
| xz -9 file.txt     | different compression preset levels (0 to 9, with default being 6) --fast equals  -0 and --best is the same as -9 |
| xz -q file.txt     | run it in quiet mode or enable verbose mode with the -v                                                           |

### آرشیو کردن فایل‌ها (Archiving files)

یک فایل آرشیو چیست؟

An Archive file is a file that is composed of one or more files along with metadata. Archive files are used to collect multiple data files together into a single file for easier portability and storage, or simply to compress files to use less storage space.

### tar

کلمه ‘tar’ در لینوکس مخفف tape archive است و برای ایجاد آرشیو و استخراج فایل‌های آرشیو استفاده می‌شود. دستور tar در لینوکس یکی از دستورات مهمی است که قابلیت آرشیو کردن را در لینوکس فراهم می‌کند.

Here a few common use cases of the tar command:

* Backup of Servers and Desktops.
* Document archiving.
* Software Distribution.

![](.gitbook/assets/performbasicfile-tar.jpg)

بیایید شروع کنیم:

```
root@ubuntu16-1:~/test-space# tree -F
.
└── myfiles/
    ├── dir1/
    │   └── myvideo.mp4*
    ├── dir2/
    │   ├── zabbix-cli-manual-1.7.0.pdf
    │   └── zabbix-cli-rpm-2.1.1-1.tar.gz
    ├── myconf.txt
    └── mylog.txt

3 directories, 5 files
```

**ایجاد فایل آرشیو tar**

```
root@ubuntu16-1:~/test-space# tar -cvf myarch.tar myfiles
myfiles/
myfiles/dir1/
myfiles/dir1/myvideo.mp4
myfiles/dir2/
myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz
myfiles/dir2/zabbix-cli-manual-1.7.0.pdf
myfiles/mylog.txt
myfiles/myconf.txt

root@ubuntu16-1:~/test-space# ls
myarch.tar  myfiles
```

ما می‌توانیم بیش از یک دایرکتوری را شامل کنیم، همچنین امکان حذف با گزینه `  --exclude  ` وجود دارد.

توجه: به طور پیش‌فرض tar فایل را در دایرکتوری فعلی شما باز می‌کند و می‌تواند مشکلاتی ایجاد کند (بازنویسی و غیره)، برای جلوگیری از آن از دستور `tar -xvf backupfile  -C  /restoreDir` استفاده کنید. `  -C  ` به معنای تغییر دایرکتوری قبل از استخراج نسخه پشتیبان است.

**Untar tar Archive File**

```
root@ubuntu16-1:~/test-space# tar -xvf myarch.tar 
myfiles/
myfiles/dir1/
myfiles/dir1/myvideo.mp4
myfiles/dir2/
myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz
myfiles/dir2/zabbix-cli-manual-1.7.0.pdf
myfiles/mylog.txt
myfiles/myconf.txt
```

ما می‌توانیم از دستور tar لینوکس برای ایجاد فایل‌های آرشیو فشرده شده یا فشرده نشده استفاده کنیم و همچنین آن‌ها را نگهداری و اصلاح کنیم.

![](.gitbook/assets/performbasicfile-targzipbzip2.jpg)

```
root@ubuntu16-1:~/test-space# tar -cvzf myfiles.tar.gz myfiles/
myfiles/
myfiles/dir1/
myfiles/dir1/myvideo.mp4
myfiles/dir2/
myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz
myfiles/dir2/zabbix-cli-manual-1.7.0.pdf
myfiles/mylog.txt
myfiles/myconf.txt

root@ubuntu16-1:~/test-space# tar -cvjf myfiles.tar.bz2 myfiles/
myfiles/
myfiles/dir1/
myfiles/dir1/myvideo.mp4
myfiles/dir2/
myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz
myfiles/dir2/zabbix-cli-manual-1.7.0.pdf
myfiles/mylog.txt
myfiles/myconf.txt

root@ubuntu16-1:~/test-space# ls -lh
total 6.4M
drwxr-xr-x 4 root root 4.0K Oct  5 23:51 myfiles
-rw-r--r-- 1 root root 3.2M Oct  6 01:59 myfiles.tar.bz2
-rw-r--r-- 1 root root 3.3M Oct  6 01:58 myfiles.tar.gz
```

برای خارج کردن tar.gzip از حالت فشرده از ` tar -xzvf file.tar.gz` و برای فایل‌های bzip2 از `tar -xjvf file.tar.bz2` استفاده کنید.

```
tar command Options:
-c : Creates Archive
-x : Extract the archive
-f : creates archive with given filename
-v : Displays Verbose Information
-t : displays or lists files in archived file
-u : archives and adds to an existing archive file
-A : Concatenates the archive files
-z : zip, tells tar command that create tar file using gzip
-j : filter archive tar file using tbzip
-W : Verify a archive file
-r : update or add file or directory in already existed .tar file
wildcards – Specify patterns in unix tar command
```

توجه: گزینه `-r` نمی‌تواند هیچ فایلی را به یک **فایل فشرده شده** اضافه کند. 

ما معمولاً از ترکیبی از گزینه‌های tar استفاده می‌کنیم تا به آنچه می‌خواهیم برسیم:

| tar command example                   | Description                                                |
| ------------------------------------- | ---------------------------------------------------------- |
| tar tf file.tar                       | list the contents and specify the tarfile                  |
| tar tvf file.tar                      | Viewing the Archive                                        |
| tar tvf file.tar filename             | We can pass a file name as an argument to search a tarfile |
| tar tvf file.tar --wildcards '\*.png' | Using wildcards with tar                                   |
| tar -tvfW file.tar                    | Verify tar  Archive File                                   |
| tar -rvf file.tar newfile.txt         | Add a file to .tar File                                    |
| tar -uvf file.tar newdir              | Add a directory to an existing .tar file                   |

![](.gitbook/assets/performbasicfile-over.jpg)

از ` tar -cJf file.tar.xz` برای ایجاد فایل فشرده xz و `    tar -xJf file.tar.xz` برای استخراج استفاده کنید.

### dd

dd مخفف Convert & Copy است اما چرا cc نیست؟ چون نام cc قبلاً توسط کامپایلر c استفاده شده است. بسیاری از مردم آن را Disk Destroyer می‌نامند زیرا dd اصلاً به سیستم‌فایل اهمیت نمی‌دهد و صرفاً با دستگاه‌های بلوکی (Block Devices) کار می‌کند!

```
dd [OPERAND]...
dd OPTION
```

سینتکس خط فرمان dd با بسیاری از برنامه‌های یونیکس دیگر متفاوت است، از این جهت که از سینتکس _option=value_ برای گزینه‌های خط فرمان خود استفاده می‌کند، نه از فرمت‌های استانداردتر _-option value_ یا _–option=value_. به طور پیش‌فرض، dd از stdin می‌خواند و در stdout می‌نویسد، اما این‌ها را می‌توان با استفاده از گزینه‌های if (فایل ورودی) و of (فایل خروجی) تغییر داد.

![](.gitbook/assets/performbasicfile-dd.jpg)

dd یک خط فایل ورودی را می‌گیرد (مثلاً /dev/sda) و آن را در فایل خروجی که مشخص می‌کنیم (مثلاً /dev/sdb) می‌نویسد، bs اندازه بلوک (block size) است، اینکه می‌خواهیم بلوک‌ها را چقدر بزرگ بنویسیم و ضروری نیست و می‌توان آن را حذف کرد.

 پشتیبان‌گیری از کل هارد دیسک:

```
dd if = /dev/sda of = /dev/sdb
```

ما حتی می‌توانیم از dd برای کپی کردن هر نوع دستگاه بلوکی استفاده کنیم و از آنجایی که dd روی خود دستگاه‌های بلوکی کار می‌کند، فرقی نمی‌کند که پارتیشن بالا باشد یا نه.

* اگر خطایی رخ دهد، دستور بالا شکست خواهد خورد. اگر پارامتر _“conv=noerror”_ را بدهید، در صورت وجود خطاهای خواندن به کپی کردن ادامه می‌دهد.`dd if = /dev/sda of = /dev/sdb conv=noerror`
* ورودی و خروجی باید با دقت زیادی ذکر شوند. در صورتی که دستگاه منبع را در مقصد ذکر کنید و برعکس، ممکن است تمام داده‌های خود را از دست بدهید.
* برای کپی کردن هارد دیسک به هارد دیسک با استفاده از دستور dd که در زیر آمده است، گزینه sync به شما اجازه می‌دهد همه چیز را با استفاده از ورودی/خروجی همگام‌سازی شده (synchronized I/O) کپی کنید.`dd if = /dev/sda of = /dev/sdb conv=noerror, sync`

| dd comand examples                                 | Description                                            |
| -------------------------------------------------- | ------------------------------------------------------ |
| dd if = /dev/sda of = /dev/sdb                     | backup entire Disk                                     |
| dd if=/dev/hda1 of=\~/partition.img                | backup a partition                                     |
| dd if = /dev/hda of = \~/hdadisk.img               | create an image of a Hard Disk                         |
| dd if = hdadisk.img of = /dev/hdb                  | restore using  Hard Disk image                         |
| dd if = /dev/cdrom of = tgsservice.iso bs = 2048   | create CD-Rom backup                                   |
| dd if=/path/to/ubuntu.iso of=/dev/sdb bs=1M        | create bootable usb drive from  image                  |
| dd if=textfile.ebcdic of=textfile.ascii conv=ascii | Convert the data format of a file from EBCDIC to ASCII |

conv می‌تواند کارهای زیادی انجام دهد مانند تبدیل یک فایل به حروف بزرگ یا برعکس.

### cpio

`cpio` مخفف “**C**o**p**y **i**n, copy **o**ut“ است. برای پردازش فایل‌های آرشیو مانند .cpio یا .tar استفاده می‌شود. این دستور می‌تواند فایل‌ها را به آرشیوها کپی کرده و از آن‌ها خارج کند.

* **حالت Copy-out:** کپی فایل‌های نام‌گذاری شده در لیست به آرشیو` command | cpio -o  > archive`
* **حالت Copy-in:** استخراج فایل‌ها از آرشیو` cpio -i < archive`
* **حالت Copy-pass:** کپی فایل‌های نام‌گذاری شده در لیست به دایرکتوری مقصد` cpio -p destination-directory < name-list`

![](.gitbook/assets/performbasicfile-cpio.jpg)

```
Policy options:
    -i, –extract: Extract files from an archive and it runs only in copy-in mode.
    -o, –create: Create the archive and it runs only in copy-out mode.
    -p, –pass-through: Run in copy-pass mode.
    -t, –list: Print a table of contents of all the inputs present.
```

| cpio command example           | Description                           |
| ------------------------------ | ------------------------------------- |
| ls /etc \| cpio -ov file.cpio  | create a cpio file                    |
| cpio -iv <  file.cpio          | extract a cpio file                   |
| ls \| cpio -ov -H tar file.tar | create a tar file using cpio          |
| cpio -iv -F file.tar           | extract a tar file using cpio         |
| cpio -it -F < file.tar         | only view tar archive file using cpio |

.

.

.

.

{% hint style="info" %}
#### دستگاه‌های لینوکس (Linux Devices) (فراتر از محدوده آزمون LPIC1)

در لینوکس فایل‌های خاص مختلفی را می‌توان زیر دایرکتوری `/dev` پیدا کرد. این فایل‌ها فایل‌های دستگاه (device files) نامیده می‌شوند و رفتاری متفاوت از فایل‌های معمولی دارند.

```
root@ubuntu16-1:~/test-space# ls -l /dev/
total 0
...
brw-rw----  1 root disk      8,   0 Dec  2  2018 sda
brw-rw----  1 root disk      8,   1 Dec  2  2018 sda1
brw-rw----  1 root disk      8,   2 Dec  2  2018 sda2
brw-rw----  1 root disk      8,   5 Dec  2  2018 sda5
crw-rw----  1 root disk     21,   0 Dec  2  2018 sg0
crw-rw----+ 1 root cdrom    21,   1 Dec  2  2018 sg1
...
```

ستون‌ها از چپ به راست به شرح زیر است:

* مجوزها (Permissions)
* مالک (Owner)
* گروه (Group)
* شماره اصلی دستگاه (Major Device Number)
* شماره فرعی دستگاه (Minor Device Number)
* برچسب زمانی (Timestamp)
* نام دستگاه (Device Name)

به یاد داشته باشید در دستور ls می‌توانید نوع فایل را با اولین بیت در هر خط ببینید. فایل‌های دستگاه به صورت زیر نشان داده می‌شوند:

```
c - character
b - block
p - pipe
s - socket
```

رایج‌ترین انواع فایل‌های دستگاه برای دستگاه‌های بلوکی (block devices) و دستگاه‌های کاراکتری (character devices) هستند. این فایل‌ها رابطی برای درایور واقعی (بخشی از هسته لینوکس) هستند که به نوبه خود به سخت‌افزار دسترسی پیدا می‌کند. نوع دیگری از فایل دستگاه که کمتر رایج است، لوله نام‌گذاری شده (named pipe) است:

**دستگاه کاراکتری (Character Device):** این دستگاه‌ها داده‌ها را منتقل می‌کنند، اما در هر بار یک کاراکتر. شما تعداد زیادی دستگاه شبه (/dev/null) را به عنوان دستگاه‌های کاراکتری خواهید دید، این دستگاه‌ها واقعاً به صورت فیزیکی به ماشین متصل نیستند، اما به سیستم‌عامل قابلیت‌های بیشتری می‌دهند.

**دستگاه بلوکی (Block Device):** این دستگاه‌ها داده‌ها را در بلوک‌های بزرگ با اندازه ثابت منتقل می‌کنند. ما معمولاً دستگاه‌هایی را که از بلوک‌های داده استفاده می‌کنند به عنوان دستگاه‌های بلوکی می‌بینیم، مانند هارد درایوها، سیستم‌فایل‌ها و غیره.

**دستگاه لوله (Pipe Device):** لوله‌های نام‌گذاری شده به دو یا چند فرآیند اجازه می‌دهند با یکدیگر ارتباط برقرار کنند، این‌ها مشابه دستگاه‌های کاراکتری هستند، اما به جای اینکه خروجی به یک دستگاه فرستاده شود، به فرآیند دیگری فرستاده می‌شود.

**دستگاه سوکت (Socket Device):** دستگاه‌های سوکت ارتباط بین فرآیندها را تسهیل می‌کنند، مشابه دستگاه‌های لوله اما می‌توانند با چندین فرآیند به طور همزمان ارتباط برقرار کنند.

**مشخصات دستگاه (شماره اصلی و شماره فرعی)**

دستگاه‌ها با استفاده از دو عدد مشخص می‌شوند: شماره اصلی (major device number) و شماره فرعی (minor device number). ما می‌توانیم این اعداد را در مثال ls بالا ببینیم، آن‌ها با یک کاما از هم جدا شده‌اند. به عنوان مثال، فرض کنید یک دستگاه دارای شماره‌های 8، 0 باشد:

شماره اصلی نشان‌دهنده درایور دستگاهی است که استفاده می‌شود، در این مورد 8، که اغلب شماره اصلی برای دستگاه‌های بلوکی sd است. شماره فرعی به هسته می‌گوید که کدام دستگاه منحصر به فرد در این کلاس درایور است، در این مورد 0 برای نشان دادن اولین دستگاه (a) استفاده می‌شود.
{% endhint %}



.

.

[https://www.computerhope.com/unix/uls.htm](https://www.computerhope.com/unix/uls.htm)

[https://linuxize.com/post/how-to-list-files-in-linux-using-the-ls-command/](https://linuxize.com/post/how-to-list-files-in-linux-using-the-ls-command/)

[https://www.rapidtables.com/code/linux/ls.html](https://www.rapidtables.com/code/linux/ls.html)

[https://www.tecmint.com/8-pratical-examples-of-linux-touch-command/](https://www.tecmint.com/8-pratical-examples-of-linux-touch-command/)

[https://www.geeksforgeeks.org/touch-command-in-linux-with-examples/](https://www.geeksforgeeks.org/touch-command-in-linux-with-examples/)

[https://linuxize.com/post/linux-touch-command/](https://linuxize.com/post/linux-touch-command/)

[https://www.geeksforgeeks.org/mkdir-command-in-linux-with-examples/](https://www.geeksforgeeks.org/mkdir-command-in-linux-with-examples/)

[https://www.lifewire.com/create-directories-linux-mkdir-command-3991847](https://www.lifewire.com/create-directories-linux-mkdir-command-3991847)

[https://developer.ibm.com/tutorials/l-lpic1-103-3/](https://developer.ibm.com/tutorials/l-lpic1-103-3/)

[https://shapeshed.com/unix-cp/](https://shapeshed.com/unix-cp/)

[https://www.rapidtables.com/code/linux/cp.html](https://www.rapidtables.com/code/linux/cp.html)

[https://www.computerhope.com/unix/ucp.htm](https://www.computerhope.com/unix/ucp.htm)

[https://www.rapidtables.com/code/linux/mv.html](https://www.rapidtables.com/code/linux/mv.html)

[https://www.computerhope.com/unix/umv.htm](https://www.computerhope.com/unix/umv.htm)

[https://jadi.gitbooks.io/lpic1/content/1033\_perform_basic_file_management.html](https://jadi.gitbooks.io/lpic1/content/1033\_perform_basic_file_management.html)

[https://www.computerhope.com/unix/urm.htm](https://www.computerhope.com/unix/urm.htm)

[https://www.computerhope.com/unix/urmdir.htm](https://www.computerhope.com/unix/urmdir.htm)

[https://www.linuxnix.com/10-file-globbing-examples-linux-unix/](https://www.linuxnix.com/10-file-globbing-examples-linux-unix/)

[https://linuxhint.com/bash_globbing_tutorial/](https://linuxhint.com/bash_globbing_tutorial/)

[https://www.linode.com/docs/tools-reference/tools/find-files-in-linux-using-the-command-line/](https://www.linode.com/docs/tools-reference/tools/find-files-in-linux-using-the-command-line/)

[https://www.lifewire.com/uses-of-linux-command-find-2201100](https://www.lifewire.com/uses-of-linux-command-find-2201100)

[https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps](https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps)

[https://www.geeksforgeeks.org/tar-command-linux-examples/](https://www.geeksforgeeks.org/tar-command-linux-examples/)

[https://itsfoss.com/linux-zip-folder/](https://itsfoss.com/linux-zip-folder/)

[https://www.geeksforgeeks.org/zip-command-in-linux-with-examples/](https://www.geeksforgeeks.org/zip-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/gzip-command-linux/](https://www.geeksforgeeks.org/gzip-command-linux/)

[https://www.geeksforgeeks.org/bzip2-command-in-linux-with-examples/](https://www.geeksforgeeks.org/bzip2-command-in-linux-with-examples/)

[https://www.tecmint.com/xz-command-examples-in-linux/](https://www.tecmint.com/xz-command-examples-in-linux/)

[https://www.debian.org/releases/wheezy/amd64/apds01.html.en](https://www.debian.org/releases/wheezy/amd64/apds01.html.en)

[https://linuxjourney.com/lesson/device-types](https://linuxjourney.com/lesson/device-types)

[https://www.geeksforgeeks.org/dd-command-linux/](https://www.geeksforgeeks.org/dd-command-linux/)

[https://linoxide.com/linux-command/linux-dd-command-create-1gb-file/](https://linoxide.com/linux-command/linux-dd-command-create-1gb-file/)

``[`https://www.geeksforgeeks.org/cpio-command-in-linux-with-examples/`](https://www.geeksforgeeks.org/cpio-command-in-linux-with-examples/)``

and whith the special thanks from shawn powers.
