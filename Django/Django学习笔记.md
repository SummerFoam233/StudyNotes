# Django学习笔记

## 项目基础结构

 项目同名文件夹下:

> - \__init__.py	Python包的初始化文件
> - wsgi.py    WEB服务网关的配置文件
> - urls.py    项目的主路由配置->HTTP请求进入Django时会优先调用的文件
> - settings.py    项目的配置文件

### settings.py

~~~python
import os
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
# abspath(__file__):当前文件的绝对路径
# os.path.dirname(path):path的父级目录
DEBUG = True
# 开发过程中开启可以直接在页面查看报错信息
ALLOWD_HOST = []
# 请求头:只有在该列表中的HOST才可以访问该项目网站
ROOT_URLCONF = 'mysite1.urls'
# urls.py的位置
LANGUAGE_CODE ='en-us'
# Django默认页面的语言设置（如启动成功示例、后台admin管理等）
TIME_ZONE = 'UTC'
# 站点时区，默认为UTC，中国可改为东八区(Asia/Shanghai)

~~~

## URL和视图函数

### URL

URL的一般格式为:`protocol://hostname[:port]/path[?query][#fragmen]`

#### protocol——协议

> - http 直接通过http访问资源
> - https 通过加密的方式通信
> - file 本地文件

#### hostname——主机名

站点域名或者ip

#### port——端口

可有可无，默认http端口为80

#### path——路由地址

一般用来表示主机上的一个目录或者文件地址。

#### query——查询

可有可无，用于给动态网页传递参数（query传参方式），可传多个参数用"&"隔开。

#### fragment——信息片段

可有可无，字符串格式，用于指定网络资源片段。可使用fragment直接将网页定位到某一名词解释（类似书签的效果)。

### Django处理URL请求

![image-20220227144308571](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220227144308571.png)

### 视图函数

~~~python
from django.http import HttpResponse
def xxx_view(request,other parms):
	html = "<h1>这是一个页面</h1>"
    return HttpResponse(html)
~~~

## 路由配置

### path

#### 概述

- 导入 - `from django.urls import path`
- 语法 - `path(route,views,name=None)`
- 参数
  - route:字符串类型，匹配的请求路径。
  - views:指定路径所对应的视图函数的名称
  - name:为地址起别名，在模板中进行地址的反向解析时使用该名。

#### path转换器

- 语法：`<转换器类型:自定义名>`
- 作用：转换器类型匹配到对应类型的数据之后，通过**关键字传参**的方式将其传递给视图函数。
- 例子：`path('page/<int:page>',views.xxx)`

![image-20220227150559517](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220227150559517.png)

#### re_path和正则表达式

##### 正则表达式

###### 限定符

> - ?：匹配0次或1次字符。
> - *：匹配0次或多次字符。
> - +：匹配1次以上的字符。
> - {}：精准匹配，取区间。如：{6}，{2,6}。

 以上方式适用于匹配一个字符，当需要匹配多个要求字符时，可以用`()`将匹配目标括起来，再使用限定符。

###### 或运算

例：`a (cat|dog)` 将会匹配到：`a cat`或者`a dog`。

###### 字符类

用方括号代表要求匹配的字符只能取自于它们。用尖括号表示匹配除此之外的字符。

例：

`[a-z]`所有的小写字符，`[a-zA-Z]`所有的英文字符。

`[^0-9]`表示除了数字以外的所有字符。

###### 元字符（预先定义好的字符类型）

> - \d 数字字符
> - \w 单词字符（英文、数字以及下划线）
> - \s 空白符（Tab字符和换行符）
> - \D 非数字字符
> - \W 非单词字符
> - \S 非空白字符
> - . 任意字符（不包括换行符）
> - 特殊字符：^和&（行首和行尾）

###### 贪婪和懒惰匹配

例：如要匹配一段html代码中的所有标签：

```html
<H1>Chapter 1 – Introduction to Regular Expressions</H1>
```

如果使用`<.+>`则会全部匹配，因为默认为**贪婪匹配**（尽可能多的去匹配），解决方法是：`<.+?>`，即可将贪婪匹配切换为**懒惰匹配**（尽可能少的匹配）。

###### 实例

1. RGB颜色匹配 `#[0-9a-fA-F]{6}\b`
2. IP地址的匹配 `\d+\.d+\.d+\.d+\.`

##### re_path使用正则表达式精确匹配路由

~~~python
from django.urls import path,re_path
re_path(r'^(?P<x>\d{1,2})/(?P<op>\w+)/(?P<y>\d{1,2}$)',views.calculater_2)
# 限定了x和y的位数
~~~

## 请求及响应

### 请求

请求以请求行、请求头、请求体组成。

#### 请求中的方法

![image-20220227170529802](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220227170529802.png)

![image-20220227170545980](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220227170545980.png)

#### Django中的请求

> - Django中的请求实际上就是视图函数的第一个参数`request`，即一个`HttpRequest`对象。
> - Django接收到http协议的请求后，会根据请求数据报文创建`HttpRequest`对象。
> - `HttpRequest`通过**属性**描述了请求的所有相关信息

##### 属性

>- path_info：URL字符串
>
>- method：请求的方法
>
>- GET：为一个`QueryDict`查询字典的对象，包含GET请求方式的所有数据。
>
>- POST：为一个`QueryDict`查询字典的对象，包含POST请求方式的所有数据。
>
>- FILES：类似于字典的对象，包含所有的上传文件信息。
>
>- COOKIES：Python字典，包含所有的cookie，键值都为字符串。
>
>- session：当前会话。
>
>- body：字符串，请求体的内容（POST或PUT）。
>
>- scheme： 请求协议（http/https）
>
>- get_full_path()：获得请求的完整路径（与`path_info`属性对应，其只能拿到当前URL）
>
>- META：请求中的元数据（请求头）。
>
>  如：`request.META['REMOTE_ADDR']`:客户端的IP地址。

### 响应

响应同样包含响应起始行、响应头和响应体。

起始行中状态码比较重要。

#### 响应状态码

> - 200  - 请求成功
> - 301 - 永久重定向
> - 302 - 临时重定向
> - 404 - 请求的资源不存在
> - 500 - 内部服务器错误（代码出错）

#### Content-Type(CT头)

> - text/html - html文件
> - text/plain - 纯文本
> - text/css - css文件
> - text/javascript - js文件
> - multipart/form-data - 文件提交
> - application/json - json传输
> - application/xml - xml文件

#### Django中的响应对象

~~~python
#构造函数格式
HttpResponse(content,content_type,status)
# content:响应体
# content_type:响应体数据类型,默认不指定为html格式
# status:状态码
~~~

#### HttpResponse子类

![image-20220227174936704](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220227174936704.png)

~~~python
from django.http import HttpResponseRedirect
~~~

## GET请求和POST请求

无论是GET还是POST，统一由视图函数接收请求，通过判断`request.method`区分具体的请求动作。

### GET处理

能产生GET请求的场景：

> - 浏览器地址中输入URL，回车后
> - \<a href="地址?参数=值&参数=值">
> - form表单中指定方式为GET

GET请求方式汇总，如果有数据需要传递给服务器，通常会用查询字符串（`Query String`）传递【**注意：不能用于传敏感性数据如密码等**】

方法示例：

~~~python
request.GET['参数名']# 直接返回该参数值，如果没有取到会报错。
request.GET.get('参数名','默认值') # 如果取到则返回，如果没有取到，则返回默认值，可以防止报错。
request.GET.getlist('参数名') # 获取某个键所有的值。
# 应用场景：问卷调查 - 复选框
~~~

### POST处理

一般用于向服务器提交大量/隐私数据。

~~~html
<form method="post" action="/login"> 
    <--!>action需要设置需要操作该动作的路由</--!>
姓名:<input type="text" name="username">
    <input type='submit' value='登陆'>
</form>
~~~

方法示例：
~~~python
request.POST['参数名']
request.POST.get('参数名','默认值') 
request.POST.getlist('参数名') 
# 如果不取消csrf验证，Django将会拒绝客户端发来的POST请求，并报403响应。
~~~

## Django设计模式及模板层

### MVC和MTV

#### MVC模式

传统的Java等使用这样的模式。

![image-20220227194439541](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220227194439541.png)

MVC -> Model-View-Controller

- M 模型层（Model），主要用于对数据库层的封装。
- V 视图层（Views），用于向用户展示结果。（WHAT+HOW：怎样的数据需要显示+如何去显示）
- C 控制层（Controller），用于处理请求、获取数据、**返回结果**。

**作用：降低模块间的耦合度（解耦）**

#### MTV模式

Django使用的模式。

![image-20220227195256718](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220227195256718.png)

MTV -> Model-View-Template

- M 模型层（Model），负责与数据库的交互。
- T 模板层（Template），负责**呈现内容**到浏览器。（HOW）
- V 视图层（Template），核心部分，负责接收请求、获取数据、返回结果。（WHAT）

**作用：降低模块间的耦合度。**

**注意：MTV设计模式中，C（Controller）其实依然存在，即`urls.py`主路由文件，以一种更精简的形式呈现。**

##### 模板层

模板是可以根据**字典数据**动态变化的html网页。

模板可以根据视图中传递的字典数据动态生成相应的HTML网页。

###### 模板配置

创建模板文件夹 `<项目名>/templates`

在`settings.py`中TEMPLATES配置项

> - BACKEND：指定模板的引擎。
> - DIRS：模板的搜索目录（可以是一个或多个）。
> - APP_DIRS：是否要在应用中的templates文件夹中搜索模板文件。
> - OPTIONS：有关模板的选项。

~~~python
'DIRS': [os.path.join(BASE_DIR, 'templates')]
# 这是一个数组，可以指定多个路径来存放模板文件。
~~~

###### 模板的加载方式

**方案一：通过loader获取模板，通过HttpResponse进行响应**

~~~python
from django.template import loader
def loader_template(request):
    # 1.通过loader加载模板
    t = loader.get_template('模板名')
    # 2.通过html加载
    html = t.render(字典数据)
    return HttpResponse(html)
~~~

**方案二：通过render()直接加载并响应模板**

~~~python
from django.shortcuts import render
def render_template(request):
    return render(request,'模板名',字典数据)
# 这里的字典数据可直接输入locals，代表该块下的全部临时变量。
~~~

###### 视图层和模板层的交互

视图函数中可以将Python变量封装到**字典**中传递到模板（即第三个参数字典数据）。

模板中可以使用`{{变量名}}`的语法，调用视图传进来的变量。

### 模板层 - 变量和标签

###### 变量

能传到模板中的数据类型：

> - str
> - int
> - list
> - tuple
> - dict
> - func - 方法
> - obj - 类实例化的对象

在模板中可以使用的变量语法

>- `{{变量名}} `- 直接获取变量
>- `{{变量名}}.index` - 访问索引
>- `{{变量名}}.key` - 在变量为字典的情况下，访问键key对应的值
>- `{{对象.方法}}` - 访问类实例对象的方法
>- `{{函数名}}`- 直接获取Python函数

###### 标签

**作用：将一些服务器端的功能嵌入到模板中，完成一些复杂的工作。**

标签语法：

~~~jinja2
{% 标签 %}
...
{% 结束标签 %} <--!>大部分都需要结束标签，少部分不用</--!>
~~~

if 标签：

~~~jinja2
{% if 条件表达式1 %}
...
{% elif 条件表达式2 %}
...
{% else %}
...
{% endif %}
~~~

for标签:

~~~jinja2
{% for 条件表达式 %}
	... 循环语句
{% empty %}
	... 可迭代对象无数据时执行的语句
{% endfor %}
~~~

内置遍历 - forloop

​	![image-20220228141516061](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220228141516061.png)

### 模板层 - 过滤器和继承

#### 过滤器

定义：在变量输出时对变量的值进行处理。

作用：可以通过使用过滤器来改变变量的输出显示。

语法：`{{变量|过滤器1:'参数值1'|过滤器2:'参数值2'}}`

> - lower - 将字符串转换为全部小写
> - upper - 将字符串转换为全部大写
> - safe - 默认不对变量内的字符串进行html转义(Django默认会对传来的参数进行转义)
> - add:"n" - 对传来的值加上n

#### 继承

通过模板继承基础，将相同的html模板继承给每个html子页面，避免重复操作。

模板继承可以使父模板的内容重用，子模板直接继承父模板的全部内容，并可以覆盖父模板中相应的块。

##### 语法 - 父模板

> - 定义父模板中的`block`标签，标识出哪些子模块中是允许被修改的。
> - `block`标签：在父模板中定义，在子模板中可覆盖。

##### 语法 - 子模板

> - 继承模板使用`extends`标签（写在模板文件中的第一行）。
> - 子模板重写父模板中的内容块`block`标签。
>
> ~~~jinja2
> {% block block_name %}
> 需要覆盖的内容
> {% endblock %}
> ~~~

###### 注意事项

> - 如果不重写，就按父模板的效果显示，反之则按重写内容显示。
> - 继承时无法将动态内容继承（`{{变量名}}`等形式传递的参数）。

### url反向解析

#### 再谈url - 代码中url出现的位置

> - **模板中：**
>
>   - ~~~html
>     <a href='url'>超链接</a>
>     <--!>点击后页面跳转到url</--!>
>     ~~~
>
>   - ~~~html
>     <form action='url' method='post'>
>     <--!>form表单的数据，用post方法提交到url。</--!>
>     ~~~
>
> - **视图函数中:**
>
>   `HttpResponseRedirect(url)` 302跳转

#### 代码中url书写规范

> - **绝对地址**
>
>   http://8.142.64.20:8000/page/1
>
> - **相对地址**
>
>   - 以`/`开头的相对地址，浏览器会把当前地址栏中的**协议、ip、端口**加上这个地址作为最终的访问地址。
>
>   - 不以`/`开头的相对地址，浏览器会把当前url最后一个/之前的内容，加上相对地址作为最终访问地址。

#### url的反向解析

url反向解析是指在视图或模板中，用path定义的名称来**动态查找**或计算出相应的路由。

**url中 - 通过name参数指定唯一别名**

`path`函数的语法：

~~~python
path(route,views,name="别名")
~~~

根据path中的`name`关键字传参给url确定了个**唯一确定**的名字，在Django中的模板或视图中，可根据这个唯一名反向推断出url的信息。（**降低url的写法的复杂度**）。

**模板中 - 通过`url`标签实现地址的反向解析**

~~~jinja2
{% url '别名' %}
{% url '别名' '参数值1' '参数值2' %}
# 这里的参数值传参可以制定关键字
~~~

**视图中 - 通过reverse方法进行反向解析**

~~~python
from django.urls import reverse
reverse("别名",args=[],kwargs={})
# args通过位置传参;kwargs通过关键字键值对传参
~~~

## 静态文件

静态文件：图片类型、css、js、音频类型、视频类型等。

### 静态文件配置 - `settings.py`中

~~~python
STATIC_URL = '/static/'
# 通过该url地址找静态文件，而不走视图函数。
STATICFILES_DIRS = (os.path.join(BASE_DIR,"static"),)
# 保存的是静态文件在服务端的存储位置。
# 注意：STATICFILES_DIRS是一个元组，如果只有一个路径的情况下注意要在该路径元素末尾加一个逗号。
~~~

### 模板中调用静态文件方式

> - 绝对地址
>
> - 相对地址
>
> - `{% static %}`标签调用
>
>   ~~~jinja2
>   {% load static %}
>   <img src="{% static '路径' %}">
>   ~~~
>
>   使用`{% static %}`标签时，路径直接在`settings.py`中设置的静态文件路径的基础上写即可。

## Django应用及分布式路由

### Django应用

应用在Django项目中是一个独立的业务模块，可以包含**自己的路由、视图、模板、模型**。

#### 创建应用

`python manage.py startapp 应用名`

#### 安装应用

`settings.py`中的`INSTALLED_APPS`配置：

~~~python
INSTALLED_APPS = [
    # ....
    'user', #用户模块
    'music', #音乐模块
    # ....
]
~~~

#### 应用目录介绍

> - `migrations`文件夹 - 数据库迁移文件
> - `admin.py` - 后台管理员模块
> - `apps.py` - 应用配置文件
> - `models.py` - 模型层入口代码
> - `tests.py` - 测试模块
> - `views.py` - 视图模块

所以实际上，应用就是一个小的`MTV`设计。

### 分布式路由

![image-20220228210607411](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220228210607411.png)

主路由的配置文件用于请求转发，具体的请求根据各自应用的路由来进行处理。

**分布式路由配置方法**

**步骤1 - 主路由中调用include函数**

导库：

~~~python
from django.urls import include
~~~

语法：`include('app名字.url模块名')`

**步骤2 - 应用下配置urls.py**

应用下要手动创建`urls.py`文件，内容结构同主路由完全一样。

~~~python
# 主路由 urls.py
urlpatterns = [
    path('music/',include('music.urls')),
]
# 子路由 urls.py
url patterns = [
    path('music001/',views.music001),
]
# 各分一半，最后结果即可访问music/music001路由
~~~

### 应用下的模板

应用内部可以配置模板目录，配置步骤如下：

> - 手动在APP目录下创建`templates`文件夹
> - 在`settings.py`文件中的`TEMPLATES`配置`APP_DIRS`为`True`

应用下的`templates`目录和根目录中`templates`目录都存在时，`django`模板寻找规则如下：

> - 优先查找外层`templates`目录下的模板
> - 按`INSTALLED_APPS`配置下的应用顺序逐层查找

**重名模板文件寻找冲突解决方法：**

在应用中的模板目录下再创建一个和应用同名的文件夹，把模板文件放于其中。并在视图函数中更改一下相对路径。

![image-20220228215149121](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220228215149121.png)

![image-20220228215231866](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220228215231866.png)

这样即可防止冲突，各找各的模板文件。

## 模型层及ORM介绍

### 模型层

模型层在`MTV`设计模式中**负责跟数据库之间进行通信。**

**Django配置mysql**

> - 安装`mysqlclient`>1.3.13
>
> - `ubuntu`中需要安装`python3-dev`和`default-libmysqlclient-dev`库。
>
> - 创建数据库
>
>   进入`mysql`数据库，执行
>
>   - ~~~mysql
>     create database 数据库名 default charset utf8
>     ~~~
>
>   - 数据库名通常与项目名保持一致
>   
> - `settings.py`配置`DATABASES`配置项：
>
>   ~~~python
>   DATABASES = {
>       'default': {
>           'ENGINE': 'django.db.backends.mysql',
>           'NAME': 'mysite1',
>           'USER': 'root',
>           'PASSWORD':'root',
>           'HOST':'127.0.0.1',
>           'PORT':'3306',
>       }
>   }
>   # ENGINE指定数据库存储引擎
>   ~~~

**注意：5.7版本mysql更改密码的方式如下：**

~~~mysql
update mysql.user set authentication_string=password('新密码') where user='root' ;
~~~

**什么是模型**

> - 模型是一个`Python`类，它是由`django.db.models.Model`派生出的子类。
> - 一个模型类代表数据库中的一张数据表。
> - 模型类中的每一个类属性都代表数据库中的一个字段。
> - 模型是数据交互的接口，是表示和操作数据库的方法和方式。

### ORM框架

建立模型类和表之间的对应关系，允许我们**直接通过**面向对象的方式来操作数据库。

**优点：**

> - 只需要**面向对象编程**，不需要面向数据库编写代码。
>   - 对数据库的操作转换为对类属性和方法的操作。
>   - 不用编写各种数据库的sql语句。
> - **实现了数据模型与数据库的解耦**，屏蔽了不同数据库操作上的差异。

**缺点：**

> - 对于复杂业务，使用成本较高。
> - 根据对象的操作转换成SQL语句，根据查询的结果转换成对象，**映射过程中有性能损失**。

![image-20220301140610986](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220301140610986.png)

