<center>Django 模型 readthedocs</center>

---

`Django` 对各种数据库提供了很好的支持，包括：`PostgreSQL`、`MySQL`、`SQLite`、`Oracle`。

`Django` 为这些数据库提供了统一的调用API。 我们可以根据自己业务需求选择不同的数据库。

`MySQL` 是 `Web` 应用中最常用的数据库。本章节我们将以 `Mysql` 作为实例进行介绍。你可以通过本站的[ MySQL 教程][1] 了解更多Mysql的基础知识。

* * *

I.数据库配置
===


我们在项目的 `settings.py` 文件中找到 `DATABASES` 配置项，将其信息修改为：
    
    
    
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',  # 或者使用 mysql.connector.django
            'NAME': 'test',
            'USER': 'test',
            'PASSWORD': 'test123',
            'HOST':'localhost',
            'PORT':'3306',
        }
    }
    

上面包含数据库名称和用户的信息，它们与`MySQL`中对应数据库和用户的设置相同。`Django`根据这一设置，与`MySQL`中相应的数据库和用户连接起来。

* * *

II.定义模型
===


i.创建 `APP`（源文档）
---


`Django`规定，如果要使用模型，必须要创建一个`app`。我们使用以下命令创建一个 `TestModel` 的 `app`:
    
    
    
    python manage.py startapp TestModel
    

目录结构如下：
    
    
    
    HelloWorld
    |-- TestModel
    |   |-- __init__.py
    |   |-- admin.py
    |   |-- models.py
    |   |-- tests.py
    |   `-- views.py
    

我们修改 `TestModel`/`models.py`文件，代码如下：
    
    
    
    # models.py
    from django.db import models
    
    class Test(models.Model):
        name = models.CharField(max_length=20)
    

以上的类名代表了数据库表名，且继承了`models.Model`，类里面的字段代表数据表中的字段(`name`)，数据类型则由`CharField`（相当于`varchar`）、`DateField`（相当于`datetime`）， `max_length` 参数限定长度。

接下来在`settings.py`中找到`INSTALLED_APPS`这一项，如下：
    
    
    
    INSTALLED_APPS = (
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'TestModel',               # 添加此项
    )
    

在命令行中运行`python manage.py syncdb`，看到几行"Creating table…"的字样，你的数据表就创建好了。
    
    
    
    Creating tables ...
    ……
    Creating table TestModel_test  #我们自定义的表
    ……
    

表名组成结构为：`app名_类名`（如：`TestModel_test`）。

**注意：**尽管我们没有在`models`给表设置主键，但是`Django`会自动添加一个`id`作为主键。

ii.实际测试
---


	[root@master HelloWorld]# python manage.py startapp TestModel

### 1.目录


```bash
[root@master HelloWorld]# ls
db.sqlite3  HelloWorld  manage.py  templates  TestModel
[root@master HelloWorld]# pwd
/root/python_project/HelloWorld
[root@master HelloWorld]# tree TestModel/
TestModel/
├── admin.py
├── apps.py
├── __init__.py
├── migrations
│   └── __init__.py
├── models.py
├── tests.py
└── views.py

1 directory, 7 files
```

### 2.`admin.py` 


```python
[root@master TestModel]# cat admin.py 
from django.contrib import admin

# Register your models here.
```

### 3.`apps.py` 


```python
[root@master TestModel]# cat apps.py 
from __future__ import unicode_literals

from django.apps import AppConfig


class TestmodelConfig(AppConfig):
    name = 'TestModel'
```

### 4.`__init__`.py 


```python
[root@master TestModel]# cat __init__.py 
[root@master TestModel]# 
```

### 5.`migrations`


```bash
[root@master TestModel]# ls migrations/
__init__.py
[root@master TestModel]# cat migrations/__init__.py 
[root@master TestModel]# 
```

### 6.`models.py` 


```python
[root@master TestModel]# cat models.py 
from __future__ import unicode_literals

from django.db import models

# Create your models here.
```

### 7.`tests.py`


```python
[root@master TestModel]# cat tests.py 
from django.test import TestCase

# Create your tests here.
```

### 8.`views.py`


```python
[root@master TestModel]# cat views.py 
from django.shortcuts import render

# Create your views here.
```

iii.错误
---


### 1.版本的问题


```bash
[root@master HelloWorld]# python manage.py syncdb
Unknown command: 'syncdb'
Type 'manage.py help' for usage.
```

```bash
[root@master HelloWorld]# python manage.py help

Type 'manage.py help <subcommand>' for help on a specific subcommand.

Available subcommands:

[auth]
    changepassword
    createsuperuser

[django]
    check
    compilemessages
    createcachetable
    dbshell
    diffsettings
    dumpdata
    flush
    inspectdb
    loaddata
    makemessages
    makemigrations
    migrate
    sendtestemail
    shell
    showmigrations
    sqlflush
    sqlmigrate
    sqlsequencereset
    squashmigrations
    startapp
    startproject
    test
    testserver

[sessions]
    clearsessions

[staticfiles]
    collectstatic
    findstatic
    runserver
```
从上面命令中可以看到并没有`syncdb`命令

经过检索找到

![|center](http://i.imgur.com/clwWkoF.jpg)

### 2.`MySQL`--`localhost` vs `127.0.0.1`


然后将`syncdb`换成`migrate`，这个时候的报错就变了

```python
  File "/usr/lib64/python2.7/site-packages/MySQLdb/connections.py", line 187, in __init__
    super(Connection, self).__init__(*args, **kwargs2)
django.db.utils.OperationalError: (2002, "Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)")
```

经过检索找到

![|center](http://i.imgur.com/1qR9u4K.jpg)

原来是`localhost`,现在改成`127.0.0.1`

```python
[root@master HelloWorld]# grep HOST settings.py
ALLOWED_HOSTS = ['192.168.142.32']
        'HOST': '127.0.0.1',
```

再次同步数据库

```bash
[root@master HelloWorld]# python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying sessions.0001_initial... OK
```

同步成功



---

III.数据库操作
===


接下来我们在 `HelloWorld` 目录中添加 `testdb.py` 文件，并修改`urls.py`：
        
    
    from django.conf.urls import *
    from HelloWorld.view import hello
    from HelloWorld.testdb import testdb
    
    urlpatterns = patterns("",
            ('^hello/$', hello),
            ('^testdb/$', testdb),
    )
    
### `urls.py`实际代码


```python
[root@master HelloWorld]# cat urls.py
...省略注释
from HelloWorld.view import hello
from django.conf.urls import url
from HelloWorld.testdb import testdb
urlpatterns = [
    url('^hello/$',hello),
    url('^testdb/$',testdb),
]
```


## i.添加数据


添加数据需要先创建对象，然后再执行 `save` 函数，相当于`SQL`中的`INSERT`：
    
    
    
    # -*- coding: utf-8 -*-
    
    from django.http import HttpResponse
    
    from TestModel.models import Test
    
    # 数据库操作
    def testdb(request):
    	test1 = Test(name='w3cschool.cc')
    	test1.save()
    	return HttpResponse("<p>数据添加成功！</p>")
    

访问`http://192.168.45.3:8000/testdb/` 就可以看到数据添加成功的提示。

### `testdb.py`实际代码


```python
[root@master HelloWorld]# cat testdb.py
# coding:utf-8

from django.http import HttpResponse
from TestModel.models import Test

def testdb(request):
    test1 = Test(name='JasonBourn')
    test1.save()
    return HttpResponse("<p>Database add successful!</p>")
```

访问结果

![](http://i.imgur.com/bf8BcG2.jpg)

第一个假设认为需要再次同步一下数据库

```python
[root@master HelloWorld]python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  No migrations to apply.
  Your models have changes that are not yet reflected in a migration, and so won't be applied.
  Run 'manage.py makemigrations' to make new migrations, and then re-run 'manage.py migrate' to apply them.
```


```python
[root@master HelloWorld]# python manage.py makemigrations
Migrations for 'TestModel':
  TestModel/migrations/0001_initial.py:
    - Create model Test
```

```python
[root@master HelloWorld]# python manage.py migrate
Operations to perform:
  Apply all migrations: TestModel, admin, auth, contenttypes, sessions
Running migrations:
  Applying TestModel.0001_initial... OK
```

假设是对的，的确是数据库没有同步。

### 测试结果


![|center](http://i.imgur.com/XNBaiVJ.jpg)

那就有一个问题，每次同步数据库都需要重新执行上面的两个命令吗？

### 查看`MySQL`数据库


现在看看数据库的变化

```bash
[root@master ~]# mysql -uroot -p123.abc
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 400
Server version: 5.7.13-log Source distribution

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

这里的`test`是自己创建的，并不是用`django`创建的

```bash
(root@localhost)(08:15:56) [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
| test_db            |
+--------------------+
6 rows in set (0.00 sec)
```

```bash
(root@localhost)(08:16:00) [(none)]> use test;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
(root@localhost)(08:16:04) [test]> 
```

查看`test`数据库当中的表

```bash
(root@localhost)(08:16:04) [test]> show tables;
+----------------------------+
| Tables_in_test             |
+----------------------------+
| TestModel_test             |
| auth_group                 |
| auth_group_permissions     |
| auth_permission            |
| auth_user                  |
| auth_user_groups           |
| auth_user_user_permissions |
| django_admin_log           |
| django_content_type        |
| django_migrations          |
| django_session             |
+----------------------------+
```

这个表里的所有内容都是通过`django`创建的

查看`TestModel_test`表中的内容

```python
(root@localhost)(08:17:37) [test]> select * from TestModel_test;
+----+------------+
| id | name       |
+----+------------+
|  1 | JasonBourn |
+----+------------+
1 row in set (0.00 sec)
```

而这个是通过`django`写进去的


## ii.获取数据


`Django`提供了多种方式来获取数据库的内容，如下代码所示：
    
    
    
    # -*- coding: utf-8 -*-
    
    from django.http import HttpResponse
    
    from TestModel.models import Test
    
    # 数据库操作
    def testdb(request):
    	# 初始化
    	response = ""
    	response1 = ""
    	
    	
    	# 通过objects这个模型管理器的all()获得所有数据行，相当于SQL中的SELECT * FROM
    	list = Test.objects.all()
    		
    	# filter相当于SQL中的WHERE，可设置条件过滤结果
    	response2 = Test.objects.filter(id=1) 
    	
    	# 获取单个对象
    	response3 = Test.objects.get(id=1) 
    	
    	# 限制返回的数据 相当于 SQL 中的 OFFSET 0 LIMIT 2;
    	Test.objects.order_by('name')[0:2]
    	
    	#数据排序
    	Test.objects.order_by("id")
    	
    	# 上面的方法可以连锁使用
    	Test.objects.filter(name="w3cschool.cc").order_by("id")
    	
    	# 输出所有数据
    	for var in list:
    		response1 += var.name + " "
    	response = response1
    	return HttpResponse("<p>" + response + "</p>")
    

输出结果如下图所示：

![][2]

### 实际测试


### `testdb.py`


```python
[root@master HelloWorld]# cat testdb.py
# coding:utf-8

from django.http import HttpResponse
from TestModel.models import Test

#def testdb(request):
#    test1 = Test(name='JasonBourn')
#    test1.save()
#    return HttpResponse("<p>Database add successful!</p>")

def testdb(request):
    response = ""
    response1 = ""

    list = Test.objects.all()
    response2 = Test.objects.filter(id=1)
    response3 = Test.objects.get(id=1)
    Test.objects.order_by('name')[0:2]
    Test.objects.order_by("id")
    Test.objects.filter(name="JasonBourn").order_by("id")

for var in list:
    response1 += var.name + " "
response = response1 
return HttpResponse("<p>" + response + "</p>")
```

这个时候提示的信息

```python
File "/root/python_project/HelloWorld/HelloWorld/testdb.py", line 25
    return HttpResponse("<p>" + response + "</p>")
SyntaxError: 'return' outside function
```
这个报错是格式的问题

第一次修改

```python
[root@master HelloWorld]# cat testdb.py
# coding:utf-8

from django.http import HttpResponse
from TestModel.models import Test

#def testdb(request):
#    test1 = Test(name='JasonBourn')
#    test1.save()
#    return HttpResponse("<p>Database add successful!</p>")

def testdb(request):
    response = ""
    response1 = ""

    list = Test.objects.all()
    response2 = Test.objects.filter(id=1)
    response3 = Test.objects.get(id=1)
    Test.objects.order_by('name')[0:2]
    Test.objects.order_by("id")
    Test.objects.filter(name="JasonBourn").order_by("id")

for var in list:
    response1 += var.name + " "
    response = response1 
    return HttpResponse("<p>" + response + "</p>")
```

依旧报同样的错误

```python
  File "/root/python_project/HelloWorld/HelloWorld/testdb.py", line 25
    return HttpResponse("<p>" + response + "</p>")
SyntaxError: 'return' outside function
```

第二次修改

```python
[root@master HelloWorld]# cat testdb.py
# coding:utf-8

from django.http import HttpResponse
from TestModel.models import Test

#def testdb(request):
#    test1 = Test(name='JasonBourn')
#    test1.save()
#    return HttpResponse("<p>Database add successful!</p>")

def testdb(request):
    response = ""
    response1 = ""

    list = Test.objects.all()
    response2 = Test.objects.filter(id=1)
    response3 = Test.objects.get(id=1)
    Test.objects.order_by('name')[0:2]
    Test.objects.order_by("id")
    Test.objects.filter(name="JasonBourn").order_by("id")

    for var in list:
        response1 += var.name + " "
        response = response1 
    
    return HttpResponse("<p>" + response + "</p>")
```

修改后服务器端的提示

```python
SyntaxError: 'return' outside function
Performing system checks...

System check identified no issues (0 silenced).
November 26, 2016 - 03:21:04
Django version 1.10.3, using settings 'HelloWorld.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

数据库中的数据显示在浏览器上

![](http://i.imgur.com/nWMjZXc.jpg)

![|center](http://i.imgur.com/1CDnGHV.jpg)


## iii.更新数据


修改数据可以使用 `save()` 或 `update()`:
    
    
    
    # -*- coding: utf-8 -*-
    
    from django.http import HttpResponse
    
    from TestModel.models import Test
    
    # 数据库操作
    def testdb(request):
    	# 修改其中一个id=1的name字段，再save，相当于SQL中的UPDATE
    	test1 = Test.objects.get(id=1)
    	test1.name = 'w3cschool菜鸟教程'
    	test1.save()
    	
    	# 另外一种方式
    	#Test.objects.filter(id=1).update(name='w3cschool菜鸟教程')
    	
    	# 修改所有的列
    	# Test.objects.all().update(name='w3cschool菜鸟教程')
    	
    	return HttpResponse("<p>修改成功</p>")
    

## iv.删除数据


删除数据库中的对象只需调用该对象的`delete()`方法即可：
    
    
    
    # -*- coding: utf-8 -*-
    
    from django.http import HttpResponse
    
    from TestModel.models import Test
    
    # 数据库操作
    def testdb(request):
    	# 删除id=1的数据
    	test1 = Test.objects.get(id=1)
    	test1.delete()
    	
    	# 另外一种方式
    	# Test.objects.filter(id=1).delete()
    	
    	# 删除所有数据
    	# Test.objects.all().delete()
    	
    	return HttpResponse("<p>删除成功</p>")
    

[1]: http://www.runoob.com/mysql/mysql-tutorial.html
[2]: http://www.runoob.com/wp-content/uploads/2015/01/testdb-get.jpg






---