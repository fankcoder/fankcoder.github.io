---
layout: post
title: Django深入学习笔记
category: 技术
tags: Django drf
keywords: django
description: 
---
## Django REST framework
媒体文件序列化，文档生成，输入检测

web browable API

OAuth1,2   第三方登录

serializaion 序列化ORMdata 类似django form

#### 启用drf

    INSTALLED_APPS = (
    ...
    'rest_framework',
    )

#### 调试api
    from django.conf.urls import url, include
    urlpatterns = [
        ...
        url(r'^api-auth/', include('rest_framework.urls'))
    ]

#### 使用ModelSerializers
    class SnippetSerializer(serializers.ModelSerializer):
    #自定义序列化
    category = CategorySerializer()
    class Meta:
        model = Snippet
        fields = ('id', 'title', 'code', 'linenos', 'language', 'style')
        #将所有字段快速添加方法
        fields = "__all__"
        

#### view层级关系

GenericViewSet(viewset)  -drf
    GenericAPIView  -drf
        APIView  -drf
            View  -django

#### mixin
    CreateModelMixin
    ListModelMixin
    RetrieveModelMixin
    UpdateModelMixin
    DestroyModelMixin
    
#### 如何通过配置分页
    drf源码-> settings -> default ->DEFAULT_PAGINATION_CLASS
    REST_FRAMEWORK = {
        'PAGE_SIZE':10,
    }

    这时接口返回结果会有count，next，previous字段，方便前端分页使用
    
#### 自定义分页

    from rest_framework.pageination import PageNumberPagination

    class GoodsPagination(PageNumberPagination):
        page_size = 10
        page_size_query_ param = 'page_size' #每页大小
        page_query_param = 'p' #第几页p
        max_page_size = 100

#### 目录结构
db_tools    #数据库初始化脚本
extra_apps  #额外第三方库

#### 拓展models.py
from django.contrib.auth.models import AbstractUser 

#### 替换系统用户
settings.py添加AUTH_USER_MODEL = 'user.UserProfile'

#### 编辑器
djangoUeidtor   

#### 返回usermodel
from django.contrib.auth import get_user_mode   

#### makemigrations
当执行makemigrations时，会在对应app下创建migrations文件夹，并生产py文件

#### migrate
执行，可以加app name指定
migrate如何知道该运行哪个py文件，django会查询django_migrations表，如果执行过，则跳过。

django的migration功能在多服务该表时非常好用

修改表结构，尽量使用改代码方式，直接改表结构极易出错

### xadmin
    在installed_apps添加crispy_forms
    
#### 创建超级用户
    登录不了可能是密码设置太简单，就改密码
    python manage.py changepassword admin
    
### 前后端分离优缺点
1. pc, app, pad 多端
2. SPA开发模式开始流行（单页面）
3. template谁来写
4. 开发效率，互相等待
5. 前端一直配合后端
6. 开发语言耦合，换语言困难

缺点
1. 前端学习门槛增加
2. 数据非常依赖文档，详细，及时更新
3. 前端工作量增大
4. seo难度增大

### restful api
restful api目前是前后端分离最佳实践，是一套标准规范

1. 轻量、直接通过http,不需要额外协议， post/get/put/delete等
2. 面向资源，一目了然，具有自解释性
3. 数据描述简单，一般通过json或xml做数据通信

HTTP是一个无状态协议。客户端想要操作服务器，需要通过 post/get/put/delete让服务器发生状态转移化

1. 每个URI代表一种资源
2. 客户端和服务器之间，传递这种资源的某种表现层
3. 客户端通过HTTP动词让服务器发生状态转移化

例子

    GET /zoos：列出所有动物园
    POST /zoos：新建一个动物园
    GET /zoos/ID：获取某个指定动物园的信息
    PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
    PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
    DELETE /zoos/ID：删除某个动物园
    GET /zoos/ID/animals：列出某个指定动物园的所有动物
    DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物

过滤信息

    ?limit=10：指定返回记录的数量
    ?offset=10：指定返回记录的开始位置。
    ?page=2&per_page=100：指定第几页，以及每页的记录数。
    ?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
    ?animal_type_id=1：指定筛选条件

状态码

    200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
    201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
    202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
    204 NO CONTENT - [DELETE]：用户删除数据成功。
    400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
    401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
    403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
    404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
    406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
    410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
    422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
    500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。
        
#### 将model转为dict
    from django.forms.models import model_to_dict
    for good in goods:
        _dict.append(good)
        
#### seriallzers将imagefile和datetime快速序列化
    from django.core import seriallzers
    json_data = serializers.serialize("json", goods)
    json_data = json_data.loads(json_data)
    return json_data
    
#### JsonResponse
简化httpresponse
    
    from django.http import JsonResponse
    return JsonResponse(json_data, safe=False)