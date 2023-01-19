---
layout: post
title: django初体验
date: 2020-04-21 22:20:52
description: 
tags: python
categories: programming
---

### 创建项目

```
$ django-admin.py startproject myblog
$ cd myblog/
$ tree
.
├── manage.py
└── myblog
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

manage.py - 项目的命令管理器

```
$ python manage.py runserver
Validating models...

0 errors found
April 21, 2020 - 14:21:57
Django version 1.6.11, using settings 'myblog.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

这样就可以访问网站了

### 项目下创建应用

```
myblog$ python manage.py startapp blog
myblog$ tree -I *.pyc      # 不列出pyc文件
.
├── blog
│   ├── __init__.py
│   ├── admin.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
└── myblog
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

```

一定记得在settings.py中把新创建的应用加入你刚刚创建的应用。

{% highlight python %}
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
)
{% endhighlight %}

### 编写传说中的Hello World

编写blog/views.py，添加以下内容：

{% highlight python %}
from django.http import HttpResponse

def index(request):
    return HttpResponse('Hello, world!')
{% endhighlight %}

修改myblog/urls.py

{% highlight python %}
import blog.views as bv

admin.autodiscover()

urlpatterns = patterns('',
    # Examples:
    # url(r'^$', 'myblog.views.home', name='home'),
    # url(r'^blog/', include('blog.urls')),

    url(r'^admin/', include(admin.site.urls)),
    url(r'^index/', bv.index),
)
{% endhighlight %}

启动服务器，Hello world就完成了

```
$ python manage.py runserver
```
