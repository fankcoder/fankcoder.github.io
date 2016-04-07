# TodoList说明文档

## 介绍
TodoList是一个便捷的事项管理应用,它帮助我们管理待办任务,梳理工作思路,来提高工作效率

TodoList包含账户注册登录等功能,来方便记录管理你的任务事项

## Introduce
Todolist,a quick and convenient management application for items that are about to be done,which helps us manage the to-do tasks, combine working train of thought and improve work efficency. 

Todolist consists of kinds of functions,including the registration and login for an account,so that it can make your tasks record and items management much easier.

界面:

![index](https://github.com/fankcoder/django-todolist/blob/master/doc/index.png)

![user_index](https://github.com/fankcoder/django-todolist/blob/master/doc/user_index.png)

![user_done](https://github.com/fankcoder/django-todolist/blob/master/doc/user_done.png)


##安装&运行
在用户目录下执行,将代码clone到本地
```
git clone https://github.com/fankcoder/django-todolist.git
```

所需环境,请看 django-todolist/doc/requirements.txt

注:为了部署方便应用默认采用了sqlite3数据库,需更换为MySQL数据库,请更改settings.py如下代码
```
'''
#use mysql databases
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'todolist',
        'USER': 'root',    #replace your mysql username
        'PASSWORD': 'xxxxx',  #replace your mysql password
        'HOST': '127.0.0.1',
        'PROT': '3306',
    }
}
'''
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```
为,请注意替换为自己的MySQL用户密码.
```
#use mysql databases
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'todolist',
        'USER': 'root',    #replace your mysql username
        'PASSWORD': 'xxxxx',  #replace your mysql password
        'HOST': '127.0.0.1',
        'PROT': '3306',
    }
}
'''
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
'''
```

##安装&配置完成后

建立本地数据库,在django-todo/todo/目录下
```
python manage.py makemigrations
python manage.py migrate
```
运行
```
python manage.py runserver 127.0.0.1:8001
```

如果没有报错,打开浏览器并访问
```
127.0.0.1:8001
```
即可看到应用已经部署成功!

##目录说明
doc 为文档目录   
todolist 为应用目录

```
django-todo/
├── doc
│   ├── index.png
│   ├── login.png
│   ├── register.png
│   ├── user_done.png
│   └── user_index.png
├── README.md
├── requirements.txt
├── requirements.txt~
└── todo
    ├── db.sqlite3
    ├── manage.py
    ├── test.py
    ├── test.py~
    ├── todo
    │   ├── __init__.py
    │   ├── __init__.pyc
    │   ├── settings.py
    │   ├── settings.pyc
    │   ├── urls.py
    │   ├── urls.pyc
    │   ├── wsgi.py
    │   └── wsgi.pyc
    └── todolist
        ├── admin.py
        ├── admin.pyc
        ├── forms.py
        ├── forms.pyc
        ├── __init__.py
        ├── __init__.pyc
        ├── models.py
        ├── models.pyc
        ├── static
        │   ├── css
        │   │   ├── create_sytle.css
        │   │   └── style.css
        │   └── js
        ├── templates
        │   ├── complete.html
        │   ├── create.html
        │   ├── index.html
        │   ├── login.html
        │   ├── nav.html
        │   └── register.html
        ├── tests.py
        ├── views.py
        └── views.pyc
```
