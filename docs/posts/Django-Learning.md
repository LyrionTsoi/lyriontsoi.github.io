---
title: django Learning
date: 2022-10-29 20:41:36
tags:
- django
- 框架
categoires:
- 工程
- 框架
- django
---

# Django

## 项目创建
1. 创建一个项目`django-admin startproject acapp`，会创建一个文件夹acapp，树形结构如图
2. -acapp
        -__init__.py
        -asgi.py
        -settings.py
        -urls.py
        -wsgi.py
    -anage.py
3. 打开`settings.py`，找到`ALLOWED_HOSTS=[]`，修改成ALLOWED_HOSTS=["自己的服务器的公网IP"]
开启项目，运行网站`python3 manage.py runserver 0.0.0.0:8000`，登上自己的网站自己的服务器公网IP:8000，看到Django的页面的小火箭即成功运行，下文的网站即自己服务器的公网IP:8000
上传到自己的Git上的时候，如果不想上传某些文件比如`__pycache__`，可以在自己的Git路径上创建一个.`gitignore`，按照格式填入不想上传的东西的名字即可，比如`__pycache__/`*

## 创建分支
1. 在一级acapp文件夹下，`python3 manage.py startapp xxx`，`xxx`是可以自定义的app名，这里用game示例，这时候会多一个文件夹game，树形结构如图
2. game
        -__init__.py
        -admin.py，存储管理员页面
        -apps.py，
        -migrations，存数据库的
        -__init__.py
        -models.py，用于定义网站的数据类
        -test.py
        -views.py，写函数的
进入网站/admin进入自己的管理员页面，不过在此之前要将所有修改更新进数据库`python3 manage.py migrate` ，再开启运行网站，之后如果需要更新，Django在即将开启运行网站的时候会提醒，所以不用刻意去记
创建管理员账号，在后台`python3 manage.py createsuperuser`，创建超级用户，之后根据提醒一步一步走即可，在管理页面登录即可
