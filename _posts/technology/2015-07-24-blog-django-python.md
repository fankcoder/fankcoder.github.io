---
layout: post
title: Django练习项目之搭建博客
category: 技术
tags: python,django
keywords: django
description: django blog
---

一,环境

1.Python

2.Django

二,安装

1.Python

2.Django安装,推荐先装个pip吧,easyinstall也可以,然后终端下输入:
　　
```
pip install Django
```

强烈推荐用新的Django版本,我用的是1.8.1

```
pip install Django==1.8.1
```
 
测试一下安装成功否

终端下:

```
python

import django

django.VERSION

(1, 8, 1, 'final', 0) 
```

三,开始

1.创建博客项目

找个干净的文件夹,终端下:

```
django-admin.py startproject nameblog
```

这里nameblog是示例,自己记得换哦

打开文件夹,或者cd nameblog/  输入 ls

发现项目创建好了

 
2.创建博客app

终端下:cd nameblog/  刚才已经cd进去的同学这步省了

然后输入:

```
python manage.py startapp blog
```

打开文件夹瞅一眼,发现多了个blog文件夹,

这时候blog项目app就创建好了

3.简单设置一下

打开nameblog/nameblog/settings.py

进行如下设置,也就是加上一条blog:

```
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
)
```

顺便换下Django默认语言把,你要是英文大神,请别鸟我,第二句是换时区,不换你就自个倒时差吧,好像永远差8小时~

```
LANGUAGE_CODE = 'zh_CN'
 
TIME_ZONE = 'Asia/Shanghai'
```

四,测试Django运行成功否

终端下输入,

注意现在的终端目录还应该是nameblog/下,而不是nameblog/nameblog下,请参考有manage.py的这层目录

好的,终端下输入:

```
python manage.py runserver
```

打开浏览器,访问地址:127.0.0.1:8000或者localhost:8000

看到淡蓝色提示,Django工作了，项目搭建成功~

如果 python manage.py runserver跑不起来

可能是8000端口被占用了？输入:

```
python manage.py runserver 8001
```
 

五,博客数据库

首先要知道

Django是一个基于MVC构造的框架,模型M，视图V和控制器C。
但是在Django中，控制器接受用户输入的部分由框架自行处理，
所以 Django 里更关注的是模型（Model）、模板(Template)和视图（Views），称为 MTV模式。
它们各自的职责如下：
 
层次，职责，模型（Model），即数据存取层处理与数据相关的所有事务： 如何存取、如何验证有效性、包含哪些行为以及数据之间的关系等。模板(Template)，即表现层处理与表现相关的决定： 如何在页面或其他类型文档中进行显示。视图（View），即业务逻辑层存取模型及调取恰当模板的相关逻辑。模型与模板之间的桥梁。
 
了解了Django先进的框架结构,就需要一层一层的进行操作,

这里注意一下,我们并没有配置数据库,Django高级一点的版本会默认配置一个sqlite3,在settings.py下

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
```    

配置模型,首先打开模型文件nameblog/blog/models.py

models.py是博客数据库的模型,每个类都是数据库中的一张表,

配置如下:

```
class Tag(models.Model):
    tag_name = models.CharField(max_length=20)
    create_time = models.DateTimeField(auto_now_add=True)
 
    def __unicode__(self):
        return self.tag_name
 
class Classification(models.Model):
    name = models.CharField(max_length=20)
 
    def __unicode__(self):
        return self.name
 
class Author(models.Model):
    name = models.CharField(max_length=30)
    email = models.EmailField(blank=True)
    website = models.URLField(blank=True)
 
    def __unicode__(self):
        return u'%s' % (self.name)
 
class Article(models.Model):
    caption = models.CharField(max_length=30)
    subcaption = models.CharField(max_length=50,blank=True)
    publish_time = models.DateTimeField(auto_now_add=True)
    update_time = models.DateTimeField(auto_now = True)
    author = models.ForeignKey(Author)
    classification = models.ForeignKey(Classification)
    tags = models.ManyToManyField(Tag, blank=True)
    content = models.TextField()
```    

我们要做博客app,就要写文章,文章需要的一些基本信息,这里基本都涵盖了（标签,分类,作者信息,发布时间等）,当然也可以加入自己想要的表,

保存退出,

同步数据库,进入 manage.py 所在的文件夹，在终端输入下:
 
注意：Django 1.7 及以上的版本需要用以下命令

```
python manage.py makemigrations
python manage.py migrate
```

注意：非Django 1.7 及以上的版本需要用以下命令

```
python manage.py syncdb
```

第一次同步数据库会让用户输入管理员帐号密码

 

六,配置视图

打开nameblog/blog/views.py

```
from django.shortcuts import render
from blog.models import Article, Tag, Classification
from django.template import RequestContext
 
def blog_list(request):
    blogs = Article.objects.all().order_by('-publish_time')
    return render(request,'index.html',{"blogs":blogs})
```    
    
这里可以看到视图函数返回index.html,现在我们需要创建html首页

 
七,创建模板首页

模板在app中默认没有这个文件夹,需要手动创建blog/templates/*.html

注意：文件最好放在blog/templates/下

简单写点html欢迎代码

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
 
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<title>fank</title>
</head>
<body>
<h1>欢迎光临xx的博客</h1>
</body>
</html>
```

这样就可以显示博客页面了，但这是静态页面还用Django搭建起来未必太Low了吧?

加上我们将会发布的博客文章

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
 
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<title>fank</title>
</head>
<body>
<h1>欢迎光临xx的博客</h1>
<div>
{% for blog in blogs %}
  <div class="blog_body">
    <div class="blog_title"><a href="/blog/detail/?id={{ blog.id }}">{{ blog.caption }}</a></div>
    <div class="blog_info">
      <ul class="blog_info_list">
        <li>分类：{{ blog.classification }}</li>
        <li>发表时间：{{ blog.publish_time|date:"Y-m-d H:i" }}</li>
        <li>标签：
          {% for tag in blog.tags.all %}
            {{ tag.tag_name }}
          {% endfor %}
        </li>
      </ul>
    </div>
    <div class="blog_description">
      {{ blog.content }}
    </div>
  </div>
{% endfor %}
</div>
</body>
</html>
```

保存退出.

八,配置url

现在我们重新启动项目

```
python manage.py runserver
```

你会发现,还是萌萌哒Django欢迎界面,说好的博客app呢?!

这是因为我们还没配置Url

打开nameblog/nameblog/urls.py

配置:

```
urlpatterns = [
    url(r'^admin/', include(admin.site.urls)),
    url(r'^$','blog.views.blog_list',name='blog_list'),
]
```

这时候,再把项目跑起来

```
python manage.py runserver
```

你会发现,

欢迎光临xx的博客

成功了~

 
九,美化网页

没有css,js的网站不是好网站

创建blog/static/images; blog/static/css; blog/static/js

美化html,自己折腾吧,或者可以下载现成的模板,大家各有品味,这里就不上我的例子了

注意:静态文件加载需要设置settings.py

详情看我的另一篇博客[django添加静态文件](http://fankcoder.com/2016/01/20/add-static-django.html)

收集静态文件,终端下:

```
python manage.py collectstatic
```

再次打开漂亮的首页,眼前一亮～

 
十,Django后台

博客app做好了,可是到现在一篇文章都木有啊

Django给我们提供了一个强大的后台管理,我们通过它来管理文章

打开blog/admin.py,没有则新建

```
from django.contrib import admin
from .models import Article
  
 
admin.site.register(Article)
```

只需要这三行代码，我们就可以拥有一个强大的后台！

提示：urls.py中关于 admin的已经默认开启

 

运行服务器,

```
python manage.py runserver
```

访问 http://localhost:8000/admin/ 输入设定的帐号和密码

点击 Articles，动手输入 添加几篇文章,

再访问首页

发现文章出现在首页了~

 

十一,代码托管到Github

在nameblog/目录

```
$ git init    //初始化

$ git add  ./

$ git commit -m 'first commit'   //提交更新，并注释信息“first commit”

$ git remote add origin git@github.com:yourproject/nameblog.git   //连接远程github项目  

$ git push -u origin master   //将本地项目更新到github项目上去
```
 

:) 谢谢！
