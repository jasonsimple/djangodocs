<center>Django 表单 readthedocs</center>

---


`HTML`表单是网站交互性的经典方式。 本章将介绍如何用`Django`对用户提交的表单数据进行处理。 

* * *

I.`HTTP` 请求
===


`HTTP`协议以"`请求`－`回复`"的方式工作。客户发送请求时，可以在请求中附加数据。服务器通过解析请求，就可以获得客户传来的数据，并根据`URL`来提供特定的服务。

## i.`GET` 方法


我们在之前的项目中创建一个 `search.py` 文件，用于接收用户的请求：
    
    
    
    # -*- coding: utf-8 -*-
    
    from django.http import HttpResponse
    from django.shortcuts import render_to_response
    
    # 表单
    def search_form(request):
    	return render_to_response('search_form.html')
    
    # 接收请求数据
    def search(request):  
    	request.encoding='utf-8'
    	if 'q' in request.GET:
    		message = '你搜索的内容为: ' + request.GET['q'].encode('utf-8')
    	else:
    		message = '你提交了空表单'
    	return HttpResponse(message)
    
### 1.实际代码`search.py`


```python
[root@master HelloWorld]# cat search.py 
#!/usr/bin/env python

from django.http import HttpResponse
from django.shortcuts import render_to_response

def search_form(request):
    return render_to_response('search_form.html')


def search(request):
    request.encoding='utf-8'
    if 'q' in request.GET:
        message = 'Your searching: ' + request.GET['q'].encode('utf-8')
    else:
        message = 'Your submit is empty'
    return HttpReponse(message)
```

在模板目录`template`中添加 `search_form.html` 表单：
            
    
    
    	<meta charset="utf-8"> 
        <title>Search - w3cschool.cc</title>
    
    
        <form action="/search/" method="get">
            <input type="text" name="q">
            <input type="submit" value="Search">
        </form>
    
### 2.实际代码`search_form.html`


```python
[root@master templates]# pwd
/root/python_project/HelloWorld/templates
[root@master templates]# cat search_form.html 
<meta charset="utf-8">
<title>Search - w3cschool.cc</title>

<form action="/search/" method="get">
    <input type="text" name="q">
    <input type="submit" value="Search">
</form>
```

    

`urls.py` 规则修改为如下形式：
    
    
    
    from django.conf.urls import *
    from HelloWorld.view import hello
    from HelloWorld.testdb import testdb
    from HelloWorld import search
    
    urlpatterns = patterns("",
    	('^hello/$', hello),
    	('^testdb/$', testdb),
    	(r'^search-form/$', search.search_form),
    	(r'^search/$', search.search),
    )

### 3.实际代码`urls.py`    


```python
[root@master HelloWorld]# cat urls.py
"""HelloWorld URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/1.10/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  url(r'^$', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  url(r'^$', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.conf.urls import url, include
    2. Add a URL to urlpatterns:  url(r'^blog/', include('blog.urls'))
"""
# Origin code
#from django.conf.urls import url
# from django.contrib import admin

# urlpatterns = [
#    url(r'^admin/', admin.site.urls),
# ]

# from django.conf.urls import *
from HelloWorld.view import hello
from django.conf.urls import url
from HelloWorld.testdb import testdb
urlpatterns = [
    url('^hello/$',hello),
    url('^testdb/$',testdb),
    url(r'^search-form/$',search.search_form),
    url(r'^search/$',search.search),
]
```

### 4.测试


```python
  File "/root/python_project/HelloWorld/HelloWorld/urls.py", line 31, in <module>
    url(r'^search-form/$',search.search_form),
NameError: name 'search' is not defined
```

其实这里你的思路应该是很清晰的。要么是`search`没有定义，要么是你没有导入，还有一种可能就是，你的search在定义或者是导入，或者是`urls.py`文件中拼写有不一致的。

虽然解决的过程中没有这样的思路指导，但是还是依次排查了一下涉及到的脚本。后来发现问题是自己没有从`search.py`中导入`search`方法。

### 5.解决'search' is not defined问题


```python
[root@master HelloWorld]# cat urls.py
"""HelloWorld URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/1.10/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  url(r'^$', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  url(r'^$', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.conf.urls import url, include
    2. Add a URL to urlpatterns:  url(r'^blog/', include('blog.urls'))
"""
# Origin code
#from django.conf.urls import url
# from django.contrib import admin

# urlpatterns = [
#    url(r'^admin/', admin.site.urls),
# ]

# from django.conf.urls import *
from HelloWorld.view import hello
from django.conf.urls import url
from HelloWorld.testdb import testdb
from HelloWorld import search

urlpatterns = [
    url('^hello/$',hello),
    url('^testdb/$',testdb),
    url(r'^search-form/$',search.search_form),
    url(r'^search/$',search.search),
]
```

更改完成后看到的变化

```python
  File "/root/python_project/HelloWorld/HelloWorld/urls.py", line 31, in <module>
    url(r'^search-form/$',search.search_form),
NameError: name 'search' is not defined
Performing system checks...

System check identified no issues (0 silenced).
November 26, 2016 - 07:32:39
Django version 1.10.3, using settings 'HelloWorld.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

访问地址：`http://192.168.45.3:8000/search-form/`并搜索，结果如下所示:

![][1]

![][2]

### 6.访问测试


![|center](http://i.imgur.com/ZZmTPLN.jpg)

![|center](http://i.imgur.com/F0j4L6Y.jpg)

![|center](http://i.imgur.com/XZ9XcBb.jpg)

### 7.搜索排错


![](http://i.imgur.com/bppKvw3.jpg)

```python
[26/Nov/2016 07:36:52] "GET /search/?q=jason HTTP/1.1" 500 62533
Internal Server Error: /search/
Traceback (most recent call last):
  File "/usr/lib/python2.7/site-packages/django/core/handlers/exception.py", line 39, in inner
    response = get_response(request)
  File "/usr/lib/python2.7/site-packages/django/core/handlers/base.py", line 187, in _get_response
    response = self.process_exception_by_middleware(e, request)
  File "/usr/lib/python2.7/site-packages/django/core/handlers/base.py", line 185, in _get_response
    response = wrapped_callback(request, *callback_args, **callback_kwargs)
  File "/root/python_project/HelloWorld/HelloWorld/search.py", line 16, in search
    return HttpReponse(message)
NameError: global name 'HttpReponse' is not defined
```
上面的报错也能看出来问题是`HttpReponse`


更改后

```python
[root@master HelloWorld]# grep return search.py
    return render_to_response('search_form.html')
    return HttpResponse(message)
```

服务器的变化

```python
[26/Nov/2016 07:48:47] "GET /search/?q=jason HTTP/1.1" 500 62533
Performing system checks...

System check identified no issues (0 silenced).
November 26, 2016 - 07:56:33
Django version 1.10.3, using settings 'HelloWorld.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
[26/Nov/2016 07:56:41] "GET /search/?q=jason HTTP/1.1" 200 21
```

### 8.搜索测试成功



![|center](http://i.imgur.com/H9d43Dg.jpg)



## ii.`POST` 方法


上面我们使用了`GET`方法。视图显示和请求处理分成两个函数处理。

提交数据时更常用`POST`方法。我们下面使用该方法，并用一个`URL`和处理函数，同时显示视图和处理请求。

我们在`tmplate` 创建 `post.html`：    
    
    
    	<meta charset="utf-8"> 
        <title>Search - w3cschool.cc</title>
    
    
    	<form action="/search-post/" method="post">
    		{% csrf_token %}
    		<input type="text" name="q">
    		<input type="submit" value="Submit">
    	</form>
    
    	<p>{{ rlt }}</p>
    
    
### 1.实际代码`post.html` 


```python
[root@master templates]# cat post.html
<meta charset="utf-8">
<title>Search - w3cschool.cc</title>

<form action="/search-post/" method="post">
    {% csrf_token %}
    <input type="text" name="q">
    <input type="submit" value="Submit">
</form>

<p>{{ rlt }}</p>
```


在模板的末尾，我们增加一个`rlt`记号，为表格处理结果预留位置。

表格后面还有一个`{% csrf_token %}`的标签。`csrf`全称是`Cross Site Request Forgery`。这是`Django`提供的防止伪装提交请求的功能。`POST`方法提交的表格，必须有此标签。

在`HelloWorld`目录下新建 `search2.py` 文件并使用 `search_post` 函数来处理 `POST` 请求：
    
    # -*- coding: utf-8 -*-
    
    from django.shortcuts import render
    from django.core.context_processors import csrf
    
    # 接收POST请求数据
    def search_post(request):
    	ctx ={}
    	ctx.update(csrf(request))
    	if request.POST:
    		ctx['rlt'] = request.POST['q']
    	return render(request, "post.html", ctx)
    
### 2.实际代码`search2.py` 



```python
[root@master HelloWorld]# cat search2.py
#!/usr/bin/env python
from django.shortcuts import render
from django.core.context_processors import csrf

def search_post(request):
    ctx = {}
    ctx.update(csrf(request))
    if request.POST:
        ctx['rlt'] = request.POST['q']
    return render(request, "post.html", ctx)
```


`urls.py` 规则修改为如下形式：
   
    from django.conf.urls import *
    from HelloWorld.view import hello
    from HelloWorld.testdb import testdb
    from HelloWorld import search
    from HelloWorld import search2
    
    urlpatterns = patterns("",
    	('^hello/$', hello),
    	('^testdb/$', testdb),
    	(r'^search-form/$', search.search_form),
    	(r'^search/$', search.search),
    	(r'^search-post/$', search2.search_post),
    )
    
### 3.实际代码`urls.py`


```python
[root@master HelloWorld]# cat urls.py
"""HelloWorld URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/1.10/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  url(r'^$', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  url(r'^$', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.conf.urls import url, include
    2. Add a URL to urlpatterns:  url(r'^blog/', include('blog.urls'))
"""
# Origin code
#from django.conf.urls import url
# from django.contrib import admin

# urlpatterns = [
#    url(r'^admin/', admin.site.urls),
# ]

# from django.conf.urls import *
from HelloWorld.view import hello
from django.conf.urls import url
from HelloWorld.testdb import testdb
from HelloWorld import search
from HelloWorld import search2


urlpatterns = [
    url('^hello/$',hello),
    url('^testdb/$',testdb),
    url(r'^search-form/$',search.search_form),
    url(r'^search/$',search.search),
    url(r'^search-post/$',search2.search_post),
]
```


访问 `http://192.168.45.3:8000/search-post/` 显示结果如下：

![][3]

服务器报错


```python
[26/Nov/2016 08:02:18] "GET /search/?q=Bourn HTTP/1.1" 200 21
Performing system checks...

Unhandled exception in thread started by <function wrapper at 0x2545de8>
Traceback (most recent call last):
  File "/usr/lib/python2.7/site-packages/django/utils/autoreload.py", line 226, in wrapper
    fn(*args, **kwargs)
  File "/usr/lib/python2.7/site-packages/django/core/management/commands/runserver.py", line 121, in inner_run
    self.check(display_num_errors=True)
  File "/usr/lib/python2.7/site-packages/django/core/management/base.py", line 374, in check
    include_deployment_checks=include_deployment_checks,
  File "/usr/lib/python2.7/site-packages/django/core/management/base.py", line 361, in _run_checks
    return checks.run_checks(**kwargs)
  File "/usr/lib/python2.7/site-packages/django/core/checks/registry.py", line 81, in run_checks
    new_errors = check(app_configs=app_configs)
  File "/usr/lib/python2.7/site-packages/django/core/checks/urls.py", line 14, in check_url_config
    return check_resolver(resolver)
  File "/usr/lib/python2.7/site-packages/django/core/checks/urls.py", line 24, in check_resolver
    for pattern in resolver.url_patterns:
  File "/usr/lib/python2.7/site-packages/django/utils/functional.py", line 35, in __get__
    res = instance.__dict__[self.name] = self.func(instance)
  File "/usr/lib/python2.7/site-packages/django/urls/resolvers.py", line 313, in url_patterns
    patterns = getattr(self.urlconf_module, "urlpatterns", self.urlconf_module)
  File "/usr/lib/python2.7/site-packages/django/utils/functional.py", line 35, in __get__
    res = instance.__dict__[self.name] = self.func(instance)
  File "/usr/lib/python2.7/site-packages/django/urls/resolvers.py", line 306, in urlconf_module
    return import_module(self.urlconf_name)
  File "/usr/lib64/python2.7/importlib/__init__.py", line 37, in import_module
    __import__(name)
  File "/root/python_project/HelloWorld/HelloWorld/urls.py", line 29, in <module>
    from HelloWorld import search2
  File "/root/python_project/HelloWorld/HelloWorld/search2.py", line 3, in <module>
    from django.core.context_processors import csrf
ImportError: No module named context_processors
```
从上面可以看到问题是出在`search2.py`

![](http://i.imgur.com/JtAjGUR.jpg)

### 4.解决`ImportError: No module named context_processors`问题


![search-post排错|center](http://i.imgur.com/zNLQIMi.jpg)

[`csrf`官网](https://docs.djangoproject.com/ja/1.9/ref/csrf/)  

更改`search2.py`

```python
[root@master HelloWorld]# cat search2.py
#!/usr/bin/env python
from django.shortcuts import render
from django.views.decorators import csrf

def search_post(request):
    ctx = {}
    ctx.update(csrf(request))
    if request.POST:
        ctx['rlt'] = request.POST['q']
    return render(request, "post.html", ctx)
```

服务器端的变化

```python
Performing system checks...

System check identified no issues (0 silenced).
November 26, 2016 - 08:28:50
Django version 1.10.3, using settings 'HelloWorld.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

```python
Internal Server Error: /search-post/
Traceback (most recent call last):
  File "/usr/lib/python2.7/site-packages/django/core/handlers/exception.py", line 39, in inner
    response = get_response(request)
  File "/usr/lib/python2.7/site-packages/django/core/handlers/base.py", line 187, in _get_response
    response = self.process_exception_by_middleware(e, request)
  File "/usr/lib/python2.7/site-packages/django/core/handlers/base.py", line 185, in _get_response
    response = wrapped_callback(request, *callback_args, **callback_kwargs)
  File "/root/python_project/HelloWorld/HelloWorld/search2.py", line 7, in search_post
    ctx.update(csrf(request))
TypeError: 'module' object is not callable
[26/Nov/2016 08:29:43] "GET /search-post/ HTTP/1.1" 500 62691
```

再次测试

![](http://i.imgur.com/VwETkaQ.jpg)

```python
Internal Server Error: /search-post/
Traceback (most recent call last):
  File "/usr/lib/python2.7/site-packages/django/core/handlers/exception.py", line 39, in inner
    response = get_response(request)
  File "/usr/lib/python2.7/site-packages/django/core/handlers/base.py", line 187, in _get_response
    response = self.process_exception_by_middleware(e, request)
  File "/usr/lib/python2.7/site-packages/django/core/handlers/base.py", line 185, in _get_response
    response = wrapped_callback(request, *callback_args, **callback_kwargs)
  File "/root/python_project/HelloWorld/HelloWorld/search2.py", line 7, in search_post
    ctx.update(csrf(request))
TypeError: 'module' object is not callable
[26/Nov/2016 08:29:43] "GET /search-post/ HTTP/1.1" 500 62691
```

### 5.解决`TypeError: 'module' object is not callable`问题


![|center](http://i.imgur.com/Mod5rhc.jpg)

[`csrf 1.10`](https://docs.djangoproject.com/en/1.10/ref/csrf/)

```
csrf_protect(view)
```

Decorator that provides the protection of `CsrfViewMiddleware` to a view.

>Usage:

```python
from django.views.decorators.csrf import csrf_protect
from django.shortcuts import render

csrf_protect
def my_view(request):
    c = {}
    # ...
    return render(request, "a_template.html", c)
```

更改`search2.py`

```python
[root@master HelloWorld]# cat search2.py
#!/usr/bin/env python
from django.shortcuts import render
from django.views.decorators import csrf
from django.views.decorators.csrf import csrf_protect

@csrf_protect
def search_post(request):
    ctx = {}
#    ctx.update(csrf_protect(request))
    if request.POST:
        ctx['rlt'] = request.POST['q']
    return render(request, "post.html", ctx)
```


### 6.测试`search-post`页面


>访问`search-post`页面

![访问search-post页面|center](http://i.imgur.com/yxnNnQf.jpg)

>输入`jason`到`search-post`页面

![输入jason|center](http://i.imgur.com/cdcr2AS.jpg)

>提交后页面
 
![submit|center](http://i.imgur.com/lzZSZnz.jpg)

>服务器端的变化

```python
[26/Nov/2016 08:49:23] "GET /search-post/ HTTP/1.1" 500 62890
Performing system checks...

System check identified no issues (0 silenced).
November 26, 2016 - 09:08:14
Django version 1.10.3, using settings 'HelloWorld.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
[26/Nov/2016 09:08:18] "GET /search-post/ HTTP/1.1" 200 324
[26/Nov/2016 09:08:22] "POST /search-post/ HTTP/1.1" 200 329
```
可以看到`POST`的操作`"POST /search-post/ HTTP/1.1" 200 329`



完成以上实例后，我们的目录结构为：
    
    
    
    HelloWorld
    |-- HelloWorld
    |   |-- __init__.py
    |   |-- __init__.pyc
    |   |-- models.pyc
    |   |-- search.py
    |   |-- search.pyc
    |   |-- search2.py
    |   |-- search2.pyc
    |   |-- settings.py
    |   |-- settings.pyc
    |   |-- testdb.py
    |   |-- testdb.pyc
    |   |-- urls.py
    |   |-- urls.pyc
    |   |-- view.py
    |   |-- view.pyc
    |   |-- wsgi.py
    |   `-- wsgi.pyc
    |-- TestModel
    |   |-- __init__.py
    |   |-- __init__.pyc
    |   |-- admin.py
    |   |-- models.py
    |   |-- models.pyc
    |   |-- tests.py
    |   `-- views.py
    |-- manage.py
    `-- templates
        |-- base.html
        |-- hello.html
        |-- post.html
        `-- search_form.html
    
    3 directories, 29 files
   
### 7.实际`HelloWorld`目录
 
```bash
[root@master python_project]# tree HelloWorld/
HelloWorld/
├── db.sqlite3
├── HelloWorld
│   ├── __init__.py
│   ├── __init__.pyc
│   ├── search2.py
│   ├── search2.pyc
│   ├── search.py
│   ├── search.pyc
│   ├── settings.py
│   ├── settings.py.bak
│   ├── settings.pyc
│   ├── testdb.py
│   ├── testdb.pyc
│   ├── urls.py
│   ├── urls.py.bak
│   ├── urls.pyc
│   ├── view.py
│   ├── view.py.bak
│   ├── view.pyc
│   ├── wsgi.py
│   └── wsgi.pyc
├── manage.py
├── templates
│   ├── base.html
│   ├── hello.html
│   ├── post.html
│   └── search_form.html
└── TestModel
    ├── admin.py
    ├── admin.pyc
    ├── apps.py
    ├── __init__.py
    ├── __init__.pyc
    ├── migrations
    │   ├── 0001_initial.py
    │   ├── 0001_initial.pyc
    │   ├── __init__.py
    │   └── __init__.pyc
    ├── models.py
    ├── models.py.bak
    ├── models.pyc
    ├── tests.py
    └── views.py

4 directories, 39 files
```

* * *

II.`Request` 对象
===


每个`view`函数的第一个参数是一个`HttpRequest`对象，就像下面这个`hello()`函数:
    
    
    
    from django.http import HttpResponse
    
    def hello(request):
        return HttpResponse("Hello world")
    

HttpRequest对象包含当前请求URL的一些信息：


| **属性**| **描述**
|:----- |:--------| 
| `path` | 请求页面的全路径,不包括域名—例如, "`/hello/`"。|
|  `method`| 请求中使用的`HTTP`方法的字符串表示。全大写表示。例如:<br/>          if request.method&nbsp;==&nbsp;'GET':&nbsp;&nbsp;&nbsp;&nbsp;<br/>   &nbsp;&nbsp;&nbsp;&nbsp;do_something()     elif&nbsp;request.method&nbsp;==&nbsp;'POST': &nbsp;&nbsp;&nbsp;&nbsp;do_something_else()|
|`GET`| 包含所有`HTTP GET`参数的类字典对象。参见`QueryDict` 文档。|  
|`POST`| 包含所有`HTTP POST`参数的类字典对象。参见`QueryDict` 文档。<br/>服务器收到空的`POST`请求的情况也是有可能发生的。也就是说，表单`form`通过`HTTP POST`方法提交请求，但是表单中可以没有数据。因此，不能使用语句`if request.POST`来判断是否使用`HTTP POST`方法；应该使用`if request.method == "POST"` (参见本表的`method`属性)。<br/>注意: `POST`不包括`file-upload`信息。参见`FILES`属性。 |  
|`REQUEST`| 为了方便，该属性是`POST`和`GET`属性的集合体，但是有特殊性，先查找`POST`属性，然后再查找`GET`属性。借鉴`PHP's $_REQUEST`。<br/>例如，如果`GET = {"name": "john"}` 和`POST = {"age": '34'}`,则 `REQUEST["name"]` 的值是`"john"`, `REQUEST["age"]`的值是`"34"`.<br/>强烈建议使用`GET and POST`,因为这两个属性更加显式化，写出的代码也更易理解。|  
|`COOKIES`| 包含所有`cookies`的标准`Python`字典对象。`Keys`和`values`都是字符串。参见第12章，有关于`cookies`更详细的讲解。|  
|`FILES`| 包含所有上传文件的类字典对象。`FILES`中的每个`Key`都是`<input type="file" name="">`标签中`name`属性的值. `FILES`中的每个`value` 同时也是一个标准`Python`字典对象，包含下面三个`Keys`:<br/> - `filename`: 上传文件名,用`Python`字符串表示<br/>- `content-type`: 上传文件的Content type<br/>- `content`: 上传文件的原始内容<br/>注意：只有在请求方法是`POST`，并且请求页面中`<form>`有`enctype="multipart/form-data"`属性时`FILES`才拥有数据。否则，`FILES` 是一个空字典。|  
|`META`| 包含所有可用HTTP头部信息的字典。 例如: <br/>* CONTENT_LENGTH<br/>* CONTENT_TYPE <br/>* QUERY_STRING: 未解析的原始查询字符串<br/>* REMOTE_ADDR: 客户端IP地址<br/>* REMOTE_HOST: 客户端主机名<br/>* SERVER_NAME: 服务器主机名<br/>* SERVER_PORT: 服务器端口<br/>META 中这些头加上前缀HTTP_最为Key, 例如:<br/>* HTTP_ACCEPT_ENCODING<br/>* HTTP_ACCEPT_LANGUAGE<br/>* HTTP_HOST: 客户发送的HTTP主机头信息<br/>* HTTP_REFERER: referring页<br/>* HTTP_USER_AGENT: 客户端的user-agent字符串<br/>* HTTP_X_BENDER: X-Bender头信息|  
|`user`| 是一个`django.contrib.auth.models.User` 对象，代表当前登录的用户。<br/>如果访问用户当前没有登录，`user`将被初始化为`django.contrib.auth.models.AnonymousUser`的实例。<br/>你可以通过`user`的`is_authenticated()`方法来辨别用户是否登录：<br/>if request.user.is_authenticated():<br/>      # Do something for logged-in users.<br/> else:<br/># Do something for anonymous users.<br/>只有激活`Django`中的`AuthenticationMiddleware`时该属性才可用|  
|`session`| 唯一可读写的属性，代表当前会话的字典对象。只有激活`Django`中的`session`支持时该属性才可用。 参见第12章。 |  
|`raw_post_data` | 原始`HTTP POST`数据，未解析过。 高级处理时会有用处。 | 

`Request`对象也有一些有用的方法：


| 方法 | 描述 | 
|:------|:----|
|`__getitem__(key)`  |  返回`GET`/`POST`的键值,先取`POST`,后取`GET`。如果键不存在抛出 `KeyError`。 <br/>这是我们可以使用字典语法访问`HttpRequest`对象。<br/>例如,`request["foo"]`等同于先`request.POST["foo"]` 然后 `request.GET["foo"]`的操作。 | 
|  `has_key()`  |  检查`request.GET or request.POST`中是否包含参数指定的`Key`。| 
|  `get_full_path()`  |  返回包含查询字符串的请求路径。例如，`"/music/bands/the_beatles/?print=true"` | 
|  `is_secure()`  |  如果请求是安全的，返回`True`，就是说，发出的是`HTTPS`请求。 | 

## i.`QueryDict`对象


在`HttpRequest`对象中, `GET`和`POST`属性是`django.http.QueryDict`类的实例。

`QueryDict`类似字典的自定义类，用来处理单键对应多值的情况。

`QueryDict`实现所有标准的词典方法。还包括一些特有的方法：


|  **方法**  |  **描述**  |  
|:-----------|:----------|
|`__getitem__`| 和标准字典的处理有一点不同，就是，如果`Key`对应多个`Value`，`__getitem__()`返回最后一个`value`。|  
|`__setitem__`| 设置参数指定`key`的`value`列表(一个`Python list`)。注意：它只能在一个`mutable` `QueryDict` 对象上被调用(就是通过`copy()`产生的一个`QueryDict`对象的拷贝).|  
|`get()`| 如果`key`对应多个`value`，`get()`返回最后一个`value`。|  
|`update()`| 参数可以是`QueryDict`，也可以是标准字典。和标准字典的`update`方法不同，该方法添加字典 `items`，而不是替换它们:<br/>&gt;&gt;&gt; q = QueryDict('a=1')<br/>&gt;&gt;&gt; q = q.copy() # to make it mutable<br/>&gt;&gt;&gt; q.update({'a': '2'})<br/>&gt;&gt;&gt; q.getlist('a')<br/>  ['1', '2']<br/>&gt;&gt;&gt; q['a'] # returns the last<br/>['2']|  
|`items()`| 和标准字典的`items()`方法有一点不同,该方法使用单值逻辑的`__getitem__():`<br/>&gt;&gt;&gt; q = QueryDict('a=1&amp;a=2&amp;a=3')<br/>&gt;&gt;&gt; q.items()<br/>[('a', '3')] |  
|`values()`| 和标准字典的`values()`方法有一点不同,该方法使用单值逻辑的`__getitem__()`: | 

此外, QueryDict也有一些方法，如下表：

| **方法** | **描述** |                                    
|----|--------|
| `copy()`  | 返回对象的拷贝，内部实现是用`Python`标准库的`copy.deepcopy()`。该拷贝是`mutable`(可更改的) — 就是说，可以更改该拷贝的值。|
| `getlist(key)` | 返回和参数`key`对应的所有值，作为一个`Python list`返回。如果`key`不存在，则返回空`list`。 It's guaranteed to return a list of some sort.. |
| `setlist(key,list_)`      | 设置`key`的值为`list_ (unlike __setitem__())`.                                                                           |
| `appendlist(key,item)`     | 添加`item`到和`key`关联的内部`list`.                                                                                                    |
| `setlistdefault(key,list)` | 和`setdefault`有一点不同，它接受`list`而不是单个`value`作为参数。                                                                       |
| `lists()`                  | 和`items()`有一点不同, 它会返回`key`的所有值，作为一个`list`, 例如:<br/>                                                                                                                                                     &gt;&gt;&gt; q = QueryDict('a=1&a=2&a=3')<br/>                                                                                                                                                                                                                                      &gt;&gt;&gt; q.lists()<br/>                                                                                                                                                                                                                                                                      [('a', ['1', '2', '3'])]                                                                                                       |
| `urlencode()`              | 返回一个以查询字符串格式进行格式化后的字符串(e.g., "a=2&b=3&b=5").                                                                |




[1]: http://www.runoob.com/wp-content/uploads/2015/01/search-form.jpg
[2]: http://www.runoob.com/wp-content/uploads/2015/01/search-form-1.jpg
[3]: http://www.runoob.com/wp-content/uploads/2015/01/search-post.jpg








---

