---
title: Python Django 接入 MySQL
tags:
  - Python
  - Django
  - MySQL
categories:
  - Python
  - module
date: 2025-01-21 21:15:00
---

# Django 接入 MySQL 教程

本文将详细介绍如何在 Django 项目中接入 MySQL 数据库，跳过 Django 的基础介绍，直接进入主题。

## 1. 安装 MySQL 数据库驱动

首先，确保已安装 MySQL 数据库驱动。Django 官方推荐使用 `mysqlclient`，可以通过以下命令安装：

```bash
pip install mysqlclient
```

如果安装 `mysqlclient` 遇到问题，可以改用 `pymysql`：

```bash
pip install pymysql
```

并在项目的 `__init__.py` 中添加以下代码：

```python
import pymysql
pymysql.install_as_MySQLdb()
```

## 2. 配置 MySQL 数据库连接

在 Django 项目的 `settings.py` 文件中，找到 `DATABASES` 配置项，修改为以下内容：

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'your_database_name',  # 数据库名称
        'USER': 'your_username',       # 数据库用户名
        'PASSWORD': 'your_password',   # 数据库密码
        'HOST': 'localhost',           # 数据库主机地址
        'PORT': '3306',                # 数据库端口
    }
}
```

## 3. 创建数据库并验证连接

在 MySQL 中创建一个与配置中 `NAME` 相同的数据库：

```sql
CREATE DATABASE your_database_name;
```

然后，在 Django 项目中运行以下命令，验证数据库连接是否正常：

```bash
python manage.py migrate
```

如果命令执行成功，说明数据库连接配置正确。

## 4. 注意事项

- 确保 MySQL 服务已启动。
- 如果使用 `pymysql`，可能会遇到一些兼容性问题，建议优先使用 `mysqlclient`。
- 在生产环境中，请将数据库密码等敏感信息存储在环境变量中，避免硬编码。

## 5. 示例代码

以下是一个简单的 Django 模型示例，用于在 MySQL 中创建表：

```python
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name
```

运行以下命令，将模型同步到数据库：

```bash
python manage.py makemigrations
python manage.py migrate
```

## 结语

通过以上步骤，您已成功在 Django 项目中接入 MySQL 数据库。如有任何问题，请参考 Django 官方文档或留言讨论。
