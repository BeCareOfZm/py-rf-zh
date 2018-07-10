# rest-framework
***
这是第三版的rest-framework
***

Django REST framework 在构建web api上是一个强大且灵活的工具包

下面的优点，可能使你选择rest framework

* 可浏览性的web api
* 验证策略包括oauth1a和oauth2
* 序列化支持orm和非orm
* 可以任意定制方法 如果你不需要更多的功能，你可以使用基础的视图

***

## 要求

环境要求：

* Python(2.7. 3.2, 3.3, 3.4, 3.5, 3.6)
* Python(1.10, 1.11, 2.0)

以下包可选：

* coreapi(1.32.0+) - 模式生成支持
* Markdown（2.1.0+） - markdown支持可浏览的api
* django-filter(1.0.1+)  - 搜索
* django-crispy-forms  - 改进搜索展示
* django-guardian - 对象等级支持

***

## 安装

使用pip安装

> pip install djangorestframework

或者从git下载, 进行安装

> git clone git@github.com:encode/django-rest-framework.git

将rest_framework加入django的settings中 `INSTALLED_APPS`

    INSTALLED_APPS = (
        ...
        'rest_framework',

    )
如果你想要使用可视化的api， 你大概要加入rest-framework的登录和登出视图， 把下面的代码加入的你的根部`urls.py`文件中

    urlpatterns = [
        ...
        url(r'^api-auth/', include('rest_framework.urls'))
    ]

注意url的路径可以任意设置

***

## 样例

让我们来一个使用rest-framework快速构建简单的model-back API

我们将在项目中创建一个可读写用户信息的api

rest-framework配置是在`settings.py`中， 一个名为`REST_FRAMEWORK`的字典， 我们首先加这个配置加入`settings.py`中

    REST_FRAMEWORK = {
        # Use Django's standard `django.contrib.auth` permissions,
        # or allow read-only access for unauthenticated users.
        'DEFAULT_PERMISSION_CLASSES': [
            'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
        ]
    }

同时也不要忘记将`rest-framework`加入`INSTALLED_APPS`变量中

现在开始创建我们的api， 下面就是我们项目中url.py的内容

    from django.conf.urls import url, include
    from django.contrib.auth.models import User
    from rest_framework import routers, serializers, viewsets

    # Serializers define the API representation.
    class UserSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = User
            fields = ('url', 'username', 'email', 'is_staff')

    # ViewSets define the view behavior.
    class UserViewSet(viewsets.ModelViewSet):
        queryset = User.objects.all()
        serializer_class = UserSerializer

    # Routers provide an easy way of automatically determining the URL conf.
    router = routers.DefaultRouter()
    router.register(r'users', UserViewSet)

    # Wire up our API using automatic URL routing.
    # Additionally, we include login URLs for the browsable API.
    urlpatterns = [
        url(r'^', include(router.urls)),
        url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
    ]

现在你可以在浏览器中输入 `http://127.0.0.1:8000`来查看新的'users' api，如果你使用顶部的登录控件登录成功， 你将能增加、删除用户，
可能需要你使用`django`中的`migrate`命令在数据库中生成相应的数据表

## 快速开始

是不是有点迫不及待了？快速开始能使你快速地使用rest-framework建立、运行、构建api

* [快速开始]('./')

