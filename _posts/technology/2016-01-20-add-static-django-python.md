---
layout: post
title: Django添加静态文件
category: 技术
tags: python
keywords: django
description: django
---
最近做了一个todolist webapp,需要添加css时候忘记添加方法了,查看了以前的项目才想起来,所以记录一下.

1. settings.py 将以下代码放到最下面

```
STATIC_URL = '/static/'
 
STATICFILES_DIRS = ( 
    os.path.join(BASE_DIR),
)
 
STATICFILES_FINDERS = (
    "django.contrib.staticfiles.finders.FileSystemFinder",
    "django.contrib.staticfiles.finders.AppDirectoriesFinder",
)
```

2. 给html添加静态文件

首行加入(一定要放在首行)，这里和Jekyll冲突了在需要代码前后添加的是“大括号和百分号”

```
/\{\%/load staticfiles/\%\}/
```

然后按照jinjia2或者djagno template的语法加上css等静态文件的路径,如

```
<link rel="stylesheet" type="text/css" href="/\{\%/ static 'css/style.css' /\%\}/" />
```

搞定
