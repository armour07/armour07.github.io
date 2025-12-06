---
title: Django CSRF Exempt 理解与应用
tags:
  - Python
  - Django
categories:
  - Python
  - Django
abbrlink: 36356
date: 2025-01-22 15:14:00
---
### Django CSRF Exempt 理解与应用

在使用 Django 框架进行 Web 开发时，跨站请求伪造（CSRF, Cross-Site Request Forgery）是一个常见的安全问题。Django 提供了内置的 CSRF 保护机制，以防止恶意网站通过用户的浏览器向你的网站发送未经授权的请求。然而，在某些情况下，我们可能需要禁用 CSRF 保护，以便 API 或特定的视图能够正常工作。本文将深入讲解如何使用 `csrf_exempt` 装饰器来禁用 CSRF 保护，并讨论其合理应用场景。

#### 什么是 CSRF？

跨站请求伪造是一种攻击技术，攻击者通过诱导用户访问一个恶意网站，利用用户的 cookies、session 或其他身份验证信息，在用户不知情的情况下，向另一个网站发送请求。这种攻击通常发生在用户已经登录了目标网站，而攻击者利用这一点向目标网站发送伪造请求。

#### Django 的 CSRF 保护机制

Django 默认启用了 CSRF 保护机制，通过在每个 POST 请求中插入一个 CSRF token，确保只有从同一网站发起的请求能够修改数据。

在 Django 中，可以通过多种方式启用或禁用 CSRF 保护：

- **全局禁用**：在 `settings.py` 文件中设置 `CSRF_COOKIE_SECURE = False` 和 `CSRF_COOKIE_HTTPONLY = False`，但这并不是推荐的做法，因为它会降低安全性。
- **视图级禁用**：通过视图装饰器 `csrf_exempt` 可以禁用单个视图的 CSRF 保护。

#### 使用 `csrf_exempt` 装饰器

`csrf_exempt` 是 Django 提供的一个装饰器，用于在单个视图中禁用 CSRF 保护。这在处理 AJAX 请求、API 调用或其他外部请求时非常有用。但是，禁用 CSRF 保护意味着该视图可能会面临 CSRF 攻击的风险，因此在使用时需要格外小心。

#### 示例代码

以下是一个使用 `csrf_exempt` 装饰器的示例：

```python
from django.utils.decorators import method_decorator
from django.views import View
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def test_view(request):
    if request.method == 'POST':
        pass

@method_decorator(csrf_exempt, name="dispatch")
class TestView(View):

    def post(self, request, *args, **kwargs):
        pass
```

在这个示例中，`@csrf_exempt` 装饰器被应用到 `write_translate_view` 函数上以及应用到了 `WriteTranslateView` 类的 `dispatch` 函数上。这意味着不会进行 CSRF 检查，可以接受来自任何来源的 POST 请求。这种方法适用于需要暴露给第三方应用或服务的 API 端点。

#### 注意事项

1. **安全性**：在禁用 CSRF 保护时，必须确保视图的安全性。可以通过其他方式，比如使用 API keys 或 OAuth 认证来保护视图。
2. **应用场景**：通常在开发 API 或处理来自外部的请求时需要禁用 CSRF 保护。但在处理敏感操作（如更改用户信息、删除数据等）时，应尽量避免禁用 CSRF 保护。
3. **调试**：在开发和调试阶段，有时会遇到 CSRF 问题。这时可以通过 `csrf_exempt` 装饰器来临时解决，但在生产环境中应慎用。

#### 总结

`csrf_exempt` 是 Django 提供的一个有用的工具，用于在特定情况下禁用 CSRF 保护。然而，使用时需要仔细评估其安全性，并采取适当的措施来保护视图。通过合理应用 `csrf_exempt` 装饰器，可以有效地提高 Django 应用的灵活性和可扩展性。