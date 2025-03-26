---
title: Python Django介绍
tags:
  - Python
  - Django
  - Django REST framework
categories:
  - Python
  - module
abbrlink: 7924
date: 2025-01-20 15:12:00
---

## Django 介绍

Django 是一个高级 Python Web 框架，旨在帮助开发者快速构建复杂且安全的 Web 应用程序。它遵循“不要重复自己”（DRY）原则，提供了许多内置功能，如用户认证、数据库管理、模板引擎等。

### Django 的核心功能
1. **ORM（对象关系映射）**：允许开发者使用 Python 代码操作数据库，而无需直接编写 SQL。
2. **模板引擎**：用于生成动态 HTML 页面。
3. **用户认证**：内置的用户认证系统，支持注册、登录、权限管理等功能。
4. **管理后台**：自动生成的管理界面，方便管理数据库内容。

## Django REST framework 介绍

Django REST framework（DRF）是 Django 的一个扩展库，专门用于构建 Web API。它提供了强大的序列化、视图、路由和认证功能，使得构建 RESTful API 变得更加简单。

### DRF 的核心功能
1. **序列化**：将复杂的数据类型（如模型实例）转换为 JSON 或其他格式。
2. **视图和视图集**：简化 API 视图的编写，支持 CRUD 操作。
3. **认证和权限**：支持多种认证方式（如 Token、Session 等）和权限控制。
4. **路由**：自动生成 API 路由，减少手动配置。

## 示例：构建一个简单的 API

接下来，我们将结合 Django 和 DRF，构建一个简单的 API 示例。

### 1. 安装依赖
首先，确保已安装 Django 和 DRF：
```bash
pip install django djangorestframework
```

### 2. 创建 Django 项目和应用
```bash
django-admin startproject myproject
cd myproject
python manage.py startapp myapp
```

### 3. 定义模型
在 `myapp/models.py` 中定义一个简单的模型：
```python
from django.db import models

class Item(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
```

### 4. 序列化模型
在 `myapp/serializers.py` 中定义序列化器：
```python
from rest_framework import serializers
from .models import Item

class ItemSerializer(serializers.ModelSerializer):
    class Meta:
        model = Item
        fields = '__all__'
```

### 5. 创建视图
在 `myapp/views.py` 中定义视图：
```python
from rest_framework import viewsets
from .models import Item
from .serializers import ItemSerializer

class ItemViewSet(viewsets.ModelViewSet):
    queryset = Item.objects.all()
    serializer_class = ItemSerializer
```

### 6. 配置路由
在 `myproject/urls.py` 中配置路由：
```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from myapp import views

router = DefaultRouter()
router.register(r'items', views.ItemViewSet)

urlpatterns = [
    path('', include(router.urls)),
]
```

### 7. 迁移数据库并启动服务器
```bash
python manage.py makemigrations
python manage.py migrate
python manage.py runserver
```

### 8. 使用 Python 脚本调用 API

以下是一个简单的 Python 脚本，用于调用 API 并获取数据：
```python
import requests

# 获取所有 Item
response = requests.get('http://127.0.0.1:8000/items/')
print(response.json())

# 创建一个新的 Item
new_item = {
    'name': 'New Item',
    'description': 'This is a new item.'
}
response = requests.post('http://127.0.0.1:8000/items/', json=new_item)
print(response.json())
```

通过以上步骤，我们成功构建了一个简单的 API，并使用 Python 脚本调用了该 API。
