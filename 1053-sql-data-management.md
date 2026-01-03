# 105.3 مدیریت داده SQL

**وزن:** ۲

**توضیحات:** داوطلبان باید قادر به پرس‌وجو (query) از پایگاه‌های داده و دستکاری داده‌ها با استفاده از دستورات پایه SQL باشند. این هدف شامل انجام پرس‌وجوهای مربوط به پیوستن (joining) ۲ جدول و/یا زیر‌انتخاب‌ها (subselects) است.

**حوزه‌های کلیدی دانش:**

* استفاده از دستورات پایه SQL
* انجام دستکاری‌های پایه روی داده‌ها

**اصطلاحات و ابزارهای کاربردی:**

* insert
* update
* select
* delete
* from
* where
* group by
* order by
* join

 تا اینجای این سری آموزشی، ما از فایل‌های متنی ساده (flat text files) برای ذخیره داده‌ها استفاده کردیم. فایل‌های متنی ساده می‌توانند برای مقادیر کمِ داده مناسب باشند، اما برای ذخیره مقادیر زیاد داده یا پرس‌وجو از آن داده‌ها مناسب نیستند. در طول سال‌ها، انواع مختلفی از پایگاه‌های داده برای این منظور توسعه یافته‌اند که رایج‌ترین آن‌ها اکنون **پایگاه داده رابطه‌ای (relational database)** است. 

امروزه چندین سیستم پایگاه داده رابطه‌ای وجود دارد، از جمله محصولات تجاری مانند Oracle Database و IBM DB2®، و پروژه‌های متن‌باز مانند MySQL، PostgreSQL، SQLite و MariaDB (انشعابی از MySQL). پایگاه‌های داده رابطه‌ای از SQL به عنوان زبان تعریف داده و پرس‌وجو استفاده می‌کنند.

{% hint style="info" %}
SQL (که "اِس-کیو-اِل" تلفظ می‌شود) مخفف Structured Query Language (زبان پرس‌وجوی ساختاریافته) است. SQL برای برقراری ارتباط با یک پایگاه داده استفاده می‌شود. این زبان استاندارد برای سیستم‌های مدیریت پایگاه داده رابطه‌ای است. عبارات SQL برای انجام کارهایی مانند به‌روزرسانی داده‌ها در یک پایگاه داده یا بازیابی داده‌ها از آن استفاده می‌شوند.
{% endhint %}

### پایگاه‌های داده، جداول، ستون‌ها و سطرها <a href="databases-tables-columns-and-rows" id="databases-tables-columns-and-rows"></a>

 یک پایگاه داده رابطه‌ای شامل مجموعه‌ای از _جداول_ است. هر _سطر_ (row) از داده‌ها در جدول را به عنوان یک رکورد در نظر بگیرید، که هر _ستون_ (column) از جدول مربوط به فیلدهای آن رکورد در سطر مربوطه است. داده‌های یک ستون همگی از یک _نوع_ (type) هستند - مانند کاراکتر، عدد صحیح، تاریخ، یا داده‌های باینری (مانند تصاویر).

ما قصد نداریم MySQL را نصب کنیم یا پایگاه‌های داده‌ای را از صفر بسازیم، تمرکز ما بر روی SQL است و از پایگاه داده موجود (ubuntu16.04) استفاده می‌کنیم:

![](.gitbook/assets/sql-mydata.jpg)

#### خط فرمان MySQL

mysql دارای یک خط فرمان ساده شل SQL است که ما از آن برای اتصال تعاملی به یک mysql-server استفاده می‌کنیم. 

```
# mysql -u root -p
Enter password: 
```

این به این معنی است که من قرار است از کاربر (`u`ser) root و رمز عبوری (`p`assword) که ارائه خواهم داد، استفاده کنم. همچنین امکان تایپ رمز عبور در دستور و تعیین پایگاه داده‌ای که قصد استفاده از آن را دارم وجود دارد، که ایده چندان خوبی نیست!

```
# mysql -u root -p password cars
```

#### استفاده از یک پایگاه داده

پس از اتصال به mysql-server، باید تعیین کنیم که از کدام پایگاه داده استفاده خواهیم کرد. ممکن است پایگاه‌های داده زیادی وجود داشته باشد، ابتدا آن‌ها را از طریق دستور `SHOW DATABASES` مشاهده می‌کنیم و سپس از `USE` برای تعیین پایگاه داده‌ای که می‌خواهیم استفاده کنیم، بهره می‌بریم.

{% hint style="info" %}
معمولاً دستورات MySQL با حروف بزرگ (CAPITAL LETTERS) و نام‌ها و مقادیر و ... با حروف کوچک تایپ می‌شوند. در انتهای هر دستور MySQL باید یک علامت `;` قرار داشته باشد.
{% endhint %}

```
root@ubuntu16-1:~# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 239
Server version: 5.7.17-0ubuntu0.16.04.1 (Ubuntu)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| cars               |
| mysql              |
| performance_schema |
| phpmyadmin         |
| sys                |
+--------------------+
6 rows in set (0.00 sec)

mysql> USE cars;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```

بیایید جداول را ببینیم:

```
mysql> SHOW TABLES;
+----------------+
| Tables_in_cars |
+----------------+
| hatchback      |
| sedan          |
+----------------+
2 rows in set (0.00 sec)
```

### بررسی جداول و ستون‌ها <a href="exploring-tables-and-columns" id="exploring-tables-and-columns"></a>

### SELECT

 عبارت **select** برای پرس‌وجو از پایگاه داده و بازیابی داده‌های انتخاب شده استفاده می‌شود. ما می‌توانیم با استفاده از `*` همه چیز را در جدول انتخاب کنیم، یا می‌توانیم از ستون‌های خاصی انتخاب کنیم:

>  SELECT _column1_,_ column2, ..._\
> FROM _table_name_;

```
mysql> SELECT * FROM hatchback;
+--------+-------+---------+
| name   | size  | country |
+--------+-------+---------+
| matiz  | small | korea   |
| golf   | big   | germany |
| ct200h | big   | japan   |
+--------+-------+---------+
3 rows in set (0.05 sec)

mysql> SELECT name FROM hatchback;
+--------+
| name   |
+--------+
| matiz  |
| golf   |
| ct200h |
+--------+
3 rows in set (0.01 sec)
```

### WHERE

 ما می‌توانیم با استفاده از `WHERE` انتخاب کنیم که کدام داده‌ها نمایش داده شوند:

>  SELECT _column1_,_ column2, ..._\
> FROM _table_name_\
> WHERE _condition_;

```
mysql> SELECT *  FROM hatchback WHERE size = 'big';
+--------+------+---------+
| name   | size | country |
+--------+------+---------+
| golf   | big  | germany |
| ct200h | big  | japan   |
+--------+------+---------+
2 rows in set (0.00 sec)
```

همچنین امکان ایجاد شرایط دلخواه از طریق `AND` و `OR` هنگام استفاده از `WHERE` وجود دارد:

```
mysql> SELECT *  FROM hatchback WHERE size = 'big' AND country = 'germany';
+------+------+---------+
| name | size | country |
+------+------+---------+
| golf | big  | germany |
+------+------+---------+
1 row in set (0.00 sec)

mysql> SELECT *  FROM hatchback WHERE size = 'big' OR country = 'korea';
+--------+-------+---------+
| name   | size  | country |
+--------+-------+---------+
| matiz  | small | korea   |
| golf   | big   | germany |
| ct200h | big   | japan   |
+--------+-------+---------+
3 rows in set (0.01 sec)
```

### ORDER BY 

اگر بخواهیم داده‌ها را بر اساس یک فیلد مرتب کنیم، از `ORDER BY` استفاده می‌شود:

>  SELECT _column1_,_ column2, ..._\
> FROM _table_name_\
> ORDER BY _column1, column2, ..._

```
mysql> SELECT *  FROM hatchback ORDER BY name;
+--------+-------+---------+
| name   | size  | country |
+--------+-------+---------+
| ct200h | big   | japan   |
| matiz  | small | korea   |
| golf   | big   | germany |
+--------+-------+---------+
3 rows in set (0.01 sec)
```

و می‌تواند نتایج را بر اساس هر فیلدی مرتب کند:

```
mysql> SELECT *  FROM hatchback ORDER BY country;
+--------+-------+---------+
| name   | size  | country |
+--------+-------+---------+
| golf   | big   | germany |
| ct200h | big   | japan   |
| matiz  | small | korea   |
+--------+-------+---------+
3 rows in set (0.00 sec)
```

### GROUP BY

 گاهی اوقات می‌خواهید اطلاعات جمع‌آوری شده (aggregate) را از یک جدول به دست آورید. برای مثال، می‌خواهید بدانید چند ماشین هاچ‌بک بزرگ دارید. برای این منظور می‌توانید از عبارت `GROUP BY` برای گروه‌بندی داده‌های خود استفاده کنید:

>  SELECT _column_name(s)_\
> FROM _table_name_\
> WHERE _condition_\
> GROUP BY _column_name(s)_

```
mysql> SELECT *  FROM hatchback GROUP BY size;                                  +-------+-------+---------+
| name  | size  | country |
+-------+-------+---------+
| golf  | big   | germany |
| matiz | small | korea   |
+-------+-------+---------+
2 rows in set (0.00 sec)
```

این دستور مانند دستور unique عمل کرده و نتیجه را گروه‌بندی می‌کند. برای فهمیدن اینکه چه تعداد سطر گروه‌بندی شده‌اند، می‌توانیم از `count` استفاده کنیم:

```
mysql> SELECT count(size)  FROM hatchback GROUP BY size;
+-------------+
| count(size) |
+-------------+
|           2 |
|           1 |
+-------------+
2 rows in set (0.04 sec)

mysql> SELECT name,count(size)  FROM hatchback GROUP BY size;
+-------+-------------+
| name  | count(size) |
+-------+-------------+
| golf  |           2 |
| matiz |           1 |
+-------+-------------+
2 rows in set (0.00 sec)
```

### ایجاد، تغییر و حذف داده‌ها و جداول <a href="creating-changing-and-deleting-data-and-tables" id="creating-changing-and-deleting-data-and-tables"></a>

###  INSERT

 دستور `INSERT` یک یا چند سطر از داده‌ها را به یک جدول اضافه می‌کند.

>  INSERT INTO _table_name_ (_column1_,_ column2_,_ column3_, ...)\
> VALUES (_value1_,_ value2_,_ value3_, ...);

```
mysql> INSERT INTO hatchback (name,size,country) VALUES ('yaris','small','japan');
Query OK, 1 row affected (0.07 sec)

mysql> SELECT * FROM hatchback;
+--------+-------+---------+
| name   | size  | country |
+--------+-------+---------+
| matiz  | small | korea   |
| golf   | big   | germany |
| ct200h | big   | japan   |
| yaris  | small | japan   |
+--------+-------+---------+
4 rows in set (0.00 sec)
```

> ترتیب فیلدها مهم نیست و پرس‌وجوی زیر همان نتیجه را خواهد داشت:

> ```
> INSERT INTO hatchback (size,name,country) VALUES ('small','yaris','japan');
> ```

###   UPDATE

دستور `UPDATE` برای اصلاح این اشتباه به کار می‌رود. این دستور سطر را به‌روزرسانی می‌کند، اما کدام سطر؟ ما باید آن را با دستور `WHERE` مشخص کنیم.

>  UPDATE _table_name_\
> SET _column1 _=_ value1_,_ column2 _=_ value2_, ...\
> WHERE _condition_;

```
mysql> UPDATE hatchback SET size= 'big' WHERE name= 'yaris';
Query OK, 1 row affected (0.05 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> SELECT * FROM hatchback;
+--------+-------+---------+
| name   | size  | country |
+--------+-------+---------+
| matiz  | small | korea   |
| golf   | big   | germany |
| ct200h | big   | japan   |
| yaris  | big   | japan   |
+--------+-------+---------+
4 rows in set (0.00 sec)
```

### DELETE

عبارت `DELETE` برای حذف رکوردهای موجود در یک جدول استفاده می‌شود.

>  DELETE FROM _table_name _WHERE _condition_;

{% hint style="danger" %}
هنگام حذف رکوردها در یک جدول مراقب باشید! به `WHERE` در دستور `DELETE` توجه کنید. عبارت `WHERE` مشخص می‌کند که کدام رکورد(ها) باید حذف شوند. اگر عبارت `WHERE` را حذف کنید، تمام رکوردهای جدول حذف خواهند شد!
{% endhint %}

```
mysql> DELETE FROM hatchback WHERE name= 'yaris';
Query OK, 1 row affected (0.03 sec)

mysql> SELECT * FROM hatchback;
+--------+-------+---------+
| name   | size  | country |
+--------+-------+---------+
| matiz  | small | korea   |
| golf   | big   | germany |
| ct200h | big   | japan   |
+--------+-------+---------+
3 rows in set (0.00 sec)
```

### JOIN

یک عبارت `JOIN` برای ترکیب سطرها از دو یا چند جدول، بر اساس یک ستون مرتبط بین آن‌ها استفاده می‌شود. اگر هیچ ستون مرتبطی داده نشود، هر سطر از جدول دوم (sedan) در مقابل هر سطر از جدول اول (hatchback) کپی می‌شود.

```
mysql> SELECT * FROM hatchback;
+--------+-------+---------+
| name   | size  | country |
+--------+-------+---------+
| matiz  | small | korea   |
| golf   | big   | germany |
| ct200h | big   | japan   |
+--------+-------+---------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM sedan;
+---------+-------+---------+
| name    | size  | country |
+---------+-------+---------+
| e200    | big   | germany |
| accent  | small | korea   |
| corolla | small | japan   |
+---------+-------+---------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM hatchback JOIN sedan;
+--------+-------+---------+---------+-------+---------+
| name   | size  | country | name    | size  | country |
+--------+-------+---------+---------+-------+---------+
| matiz  | small | korea   | e200    | big   | germany |
| golf   | big   | germany | e200    | big   | germany |
| ct200h | big   | japan   | e200    | big   | germany |
| matiz  | small | korea   | accent  | small | korea   |
| golf   | big   | germany | accent  | small | korea   |
| ct200h | big   | japan   | accent  | small | korea   |
| matiz  | small | korea   | corolla | small | japan   |
| golf   | big   | germany | corolla | small | japan   |
| ct200h | big   | japan   | corolla | small | japan   |
+--------+-------+---------+---------+-------+---------+
9 rows in set (0.01 sec)
```

و اگر به `JOIN` یک فیلد مشترک برای پیوستن جداول بر اساس آن بدهیم، جادو اتفاق می‌افتد:

```
mysql> SELECT * FROM hatchback JOIN sedan ON  hatchback.country = sedan.country; 
+--------+-------+---------+---------+-------+---------+
| name   | size  | country | name    | size  | country |
+--------+-------+---------+---------+-------+---------+
| golf   | big   | germany | e200    | big   | germany |
| matiz  | small | korea   | accent  | small | korea   |
| ct200h | big   | japan   | corolla | small | japan   |
+--------+-------+---------+---------+-------+---------+
3 rows in set (0.01 sec)


mysql> SELECT * FROM hatchback JOIN sedan ON  hatchback.size = sedan.size;
+--------+-------+---------+---------+-------+---------+
| name   | size  | country | name    | size  | country |
+--------+-------+---------+---------+-------+---------+
| golf   | big   | germany | e200    | big   | germany |
| ct200h | big   | japan   | e200    | big   | germany |
| matiz  | small | korea   | accent  | small | korea   |
| matiz  | small | korea   | corolla | small | japan   |
+--------+-------+---------+---------+-------+---------+
4 rows in set (0.00 sec)

```

{% hint style="info" %}
### انواع مختلف SQL JOIN

در اینجا انواع مختلف JOIN در SQL آورده شده است:

* **(INNER) JOIN**: رکوردهایی را برمی‌گرداند که در هر دو جدول مقادیر منطبق دارند
* **LEFT (OUTER) JOIN**: تمام رکوردها را از جدول چپ و رکوردهای منطبق را از جدول راست برمی‌گرداند
* **RIGHT (OUTER) JOIN**: تمام رکوردها را از جدول راست و رکوردهای منطبق را از جدول چپ برمی‌گرداند
* **FULL (OUTER) JOIN**: تمام رکوردها را زمانی که در جدول چپ یا راست تطابقی وجود داشته باشد، برمی‌گرداند
{% endhint %}

![](.gitbook/assets/sql-jointypes.jpg)

در نهایت از دستور `quit` برای خروج از خط فرمان MySQL استفاده کنید.

```
mysql> quit
Bye
root@ubuntu16-1:~# 
```

تمام!

.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-105-3/](https://developer.ibm.com/tutorials/l-lpic1-105-3/)

[http://www.sqlcourse.com/intro.html](http://www.sqlcourse.com/intro.html)

[https://dev.mysql.com/doc/refman/8.0/en/mysql.html](https://dev.mysql.com/doc/refman/8.0/en/mysql.html)

[https://jadi.gitbooks.io/lpic1/content/1053\_sql_data_management.html](https://jadi.gitbooks.io/lpic1/content/1053\_sql_data_management.html)

``[`https://www.w3schools.com/sql/sql_select.asp`](https://www.w3schools.com/sql/sql_select.asp)``

[https://tableplus.com/blog/2018/08/mysql-how-to-turn-off-only-full-group-by.html](https://tableplus.com/blog/2018/08/mysql-how-to-turn-off-only-full-group-by.html)

.
