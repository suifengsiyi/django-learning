# 开始第一个Restful API

## 创建数据表
 
- 在app1下的models.py文件中新建用户表

```python
from django.db import models


class User(models.Model):
    status_choices = ((0, '未激活'), (1, '正常'), (2, '失效'))
    
    username = models.CharField(max_length=64, help_text='用户账号')
    password = models.CharField(max_length=64, help_text='用户密码')
    name = models.CharField(max_length=64, help_text='姓名')
    sex = models.BooleanField(default=True, help_text='性别，默认 False 女性')
    email = models.CharField(max_length=64, blank=True, null=True, help_text='邮箱账号')
    mobile = models.CharField(max_length=11, blank=True, null=True, help_text='手机号码')
    gmt_created = models.DateTimeField(auto_now_add=True, help_text='创建时间')
    gmt_modified = models.DateTimeField(auto_now=True, help_text='最后修改时间')
    describe = models.TextField(blank=True, null=True, help_text='备注')
    status = models.IntegerField(choices=status_choices, default=0, help_text='状态')
    
    class Meta:
        db_table = 'user'
        verbose_name = '用户表'
        verbose_name_plural = verbose_name
        ordering = ['-gmt_created']
```

## 创建序列化器

- 在app1下新建serializers.py文件

```python
from rest_framework import serializers
from apps.app1 import models


class UserSerializer(serializers.ModelSerializer):

    class Meta:
        model = models.User
        fields = '__all__'
```

## 创建过滤器

- 在app1下新建filters.py文件

```python
from django_filters import rest_framework as filters
from apps.app1 import models


class UserFilter(filters.FilterSet):
    username = filters.CharFilter(field_name='username', lookup_expr='icontains', help_text='用户账号模糊搜索')
    sex = filters.BooleanFilter(field_name='sex', lookup_expr='iexact', help_text='用户性别精准搜索')

    class Meta:
        model = models.User
        fields = []
```

## 创建视图

- 在app1下的views.py文件中新建用户视图

```python
from rest_framework.viewsets import ModelViewSet
from apps.app1 import models, serializers, filters


class UserViewSet(ModelViewSet):
    queryset = models.User.objects.all()
    serializer_class = serializers.UserSerializer
    filter_class = filters.UserFilter
```

## 创建路由器

- 在app1下新建urls.py文件

```python
from django.conf.urls import url, include 
from rest_framework import routers
from apps.app1 import views

router = routers.DefaultRouter()
router.register(r'users', views.UserViewSet)


urlpatterns = [
    url(r'', include(router.urls))
]
```

- 加入全局urls.py

```python
from django.conf.urls import url, include


urlpatterns = [
    url(r'^api/app1/', include('apps.app1.urls'), name='app1'),
]
```

## ModelViewSet API 映射关系

- 完成以上操作后运行项目，打开浏览器调用API测试，接口说明如下

```text
Http Method          Url              Function        Describe

get             /api/users/           list            获取用户列表
get             /api/users/1/         retrieve        获取单个用户信息
post            /api/users/           create          创建一个用户
put             /api/users/1/         update          更新单个用户信息
patch           /api/users/1/         update          更新单个用户部分信息
delete          /api/users/1/         destroy         删除单个用户
```
