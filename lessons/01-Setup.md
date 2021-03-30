# 搭建

**基本环境**

- python3.6+
- django1.11+
- djangorestframework3.6+
- django-filter1.1+
- mysqlclient1.3+
- mysql3.6+

## 创建项目

- 执行创建命令

```commandline
django-admin.py startproject test-service
cd test-service/
python manage.py startapp app1
python manage.py startapp app2
```

- 项目结构

```text
|-- test-service  # 项目根目录
    |-- manage.py  # 项目管理脚本，可运行django内置命令，也支持用户自定义命令
    |-- app1  # 单个app服务包
    |   |-- admin.py  # django自带管理站点（忽略）
    |   |-- apps.py  # app声明（不做操作）
    |   |-- models.py  # 定义数据库表结构（重点）
    |   |-- tests.py  # 单元测试（忽略）
    |   |-- views.py  # API服务（重点）
    |   |-- migrations  # 存储数据库表结构改动文件（不做操作）
    |-- app2  # 同app1
    |   |-- admin.py
    |   |-- apps.py
    |   |-- models.py
    |   |-- tests.py
    |   |-- views.py
    |   |-- migrations
    |-- test-service  # 项目总配置包
    |   |-- settings.py  # 全局配置（重点）
    |   |-- urls.py  # 定义总路由
    |   |-- wsgi.py  # wsgi配置（不做操作）
```

- 更改app路径

```text
# 步骤一、在test-service项目下新建apps包
cd test-service/
mkdir apps
touch apps/__init__.py

# 步骤二、将app1/app2移入apps
mv app1 apps
mv app2 apps

# 步骤三、将项目中app1h和app2的所有引用改为对应的apps.app1或apps.app2
```

## 修改主配置

- 项目主配置在test-service包下的settings.py

```text
# DEBUG
# 是否开启调试模式，开启后API报错会将具体代码信息打印，线上开启容易被攻击
# 一般开发/测试环境开启，生产环境关闭
DEBUG = True

# ALLOWED_HOSTS
# 可以指定能访问此项目的主机IP/域名
ALLOWED_HOSTS = ['*']

# INSTALLED_APPS
# 指定项目使用服务，上面六个为django自带服务，rest_framework等第三方包也是以app服务方式引入
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'django_filters',
    'apps.app1',
    'apps.app2',
]

# MIDDLEWARE
# 中间件，用默认的就ok

# ROOT_URLCONF
# 指定项目总路由配置
ROOT_URLCONF = 'test-service.urls'

# TEMPLATES
# 前端模板配置，此项忽略，建议前后端分离

# WSGI_APPLICATION
# django自带的wsgi模块，遵守Python Web WSGI协议，可以连用支持WSGI的web服务器（Nginx、Apache等）
WSGI_APPLICATION = 'test-service.wsgi.application'

# DATABASES
# 数据库配置，django默认使用自带的sqlite，支持配置mysql、oracle、postgresql等
# 演示项目使用的mysql数据库
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'ATOMIC_REQUESTS': True,
        'NAME': 'test_service',
        'USER': 'XXXXXX',
        'PASSWORD': 'XXXXX',
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'OPTIONS': {
            'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
            'charset': 'utf8',
        },
        'TEST': {
            'NAME': 'test_service',
            'CHARSET': 'utf8',
            'COLLATION': 'utf8_general_ci',
        },
    }
}

# 语言、时区配置
LANGUAGE_CODE = 'en-us'
TIME_ZONE = 'Asia/Shanghai'
USE_I18N = True
USE_L10N = True
USE_TZ = False


# rest_framework配置
REST_FRAMEWORK = {
    # 授权器（不用可注释）
    #'DEFAULT_AUTHENTICATION_CLASSES': (
    #    'rest_framework.authentication.BasicAuthentication',
    #),

    # 权限器
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.AllowAny',
    ),

    # 分页器
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,

    # 过滤器
    'DEFAULT_FILTER_BACKENDS': (
        'django_filters.rest_framework.DjangoFilterBackend',
        'rest_framework.filters.SearchFilter',
        'rest_framework.filters.OrderingFilter',
    ),

    # 序列化日期/时间格式
    'DATETIME_FORMAT': '%Y-%m-%d %H:%M:%S',
    'DATE_FORMAT': '%Y-%m-%d',
    'TIME_FORMAT': '%H:%M:%S',
}
```

## 试运行项目

- 以上基本操作完成后，可通过manage.py运行项目，默认启动localhost:8000，打开本地浏览器访问localhost:8000成功即可

```commandline
python manage.py runserver
```

- 0.0.0.0表示其他机器可访问

```commandline
python manage.py runserver 0.0.0.0 8008
```
