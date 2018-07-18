# 快速开始
我们将在系统中创建一个api， 用于允许管理员进行查看和编辑用户和用户组信息

## 项目步骤

创建一个名字为`tutorial`项目，在生成一个名字为`quickstart`应用

    # 创建项目文件夹
    mkdir tutorial
    cd tutorial

    # 创建一个虚拟环境
    virtualenv env
    source env/bin/activate  # window使用 `env\Scripts\activate`

    # 现在安装django和djangorestframework
    pip install django
    pip install djangorestframework

    # 创建项目，然后在创建quickstart应用
    django-admin.py startproject tutorial .  #注意 '.'
    cd tutorial
    django-admin.py startapp quickstart
    cd ..

这个文件结构应该是这样的

    $ pwd
    <some path>/tutorial
    $ find .
    .
    ./manage.py
    ./tutorial
    ./tutorial/__init__.py
    ./tutorial/quickstart
    ./tutorial/quickstart/__init__.py
    ./tutorial/quickstart/admin.py
    ./tutorial/quickstart/apps.py
    ./tutorial/quickstart/migrations
    ./tutorial/quickstart/migrations/__init__.py
    ./tutorial/quickstart/models.py
    ./tutorial/quickstart/tests.py
    ./tutorial/quickstart/views.py
    ./tutorial/settings.py
    ./tutorial/urls.py
    ./tutorial/wsgi.py

在项目目录中创建应用程序可能看起来很不寻常，但是使用项目的命名空间能避免与外部拓展的包产生冲突。
第一需要同步生成你的数据库。

    python manage.py migrate

我们将创建一个名为`admin`、密码为`password123`的初始用户， 这个用户将作为我们的认证用户。

    python manage.py createsuperuser --email admin@example.com --username admin

一旦你已经创建了数据库和初始用户，那就打开项目的文件夹，准备我们接下来编程吧

## serializer（序列化）

首相我们打算定义几个serializer， 先创建一个`tutorial/quickstart/serializers.py`文件用于我们的数据表示

    from django.contrib.auth.models import User, Group
    from rest_framework import serializers


    class UserSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = User
            fields = ('url', 'username', 'email', 'groups')


    class GroupSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = Group
            fields = ('url', 'name')

注意在这种情况下，我们使用超链接， 对于`HyperlinkedModelSerializer`，你能使用主键和其他种类的关系， 超链接是一个好的RESTful设计


## 视图

现在我将写一些视图，打开`tutorial/quickstart/views.py`

    from django.contrib.auth.models import User, Group
    from rest_framework import viewsets
    from tutorial.quickstart.serializers import UserSerializer, GroupSerializer


    class UserViewSet(viewsets.ModelViewSet):
        """
        API endpoint that allows users to be viewed or edited.
        """
        queryset = User.objects.all().order_by('-date_joined')
        serializer_class = UserSerializer


    class GroupViewSet(viewsets.ModelViewSet):
        """
        API endpoint that allows groups to be viewed or edited.
        """
        queryset = Group.objects.all()
        serializer_class = GroupSerializer


我们不是将多个视图分组在一起，而是将所有的共同行为分组到称为ViewSets的类中。
如果需要的话，我们可以很容易地把这些分解成单独的视图，但是使用视图集可以使视图逻辑组织得很好，并且非常简洁。

