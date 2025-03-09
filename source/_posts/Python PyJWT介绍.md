---
title: Python PyJWT介绍
tags:
  - Python
  - PyJWT
categories:
  - Python
  - module
abbrlink: 44995
date: 2025-01-24 16:21:00
---

PyJWT 是一个用于生成和验证 JSON Web Tokens (JWT) 的 Python 库。它支持多种加密算法，并且易于使用。以下是 PyJWT 的一些主要功能和使用示例。
### 安装 PyJWT
你可以使用 pip 安装 PyJWT：
```sh
pip install pyjwt
```

### 生成 JWT
使用 PyJWT 生成 JWT 非常简单。以下是一个使用对称加密算法（HS256）生成 JWT 的示例：
```python
import jwt
import datetime

def generate_jwt(user_id):
    payload = {
        'user_id': user_id,
        'exp': datetime.datetime.now(datetime.timezone.utc) + datetime.timedelta(hours=1)
    }
    token = jwt.encode(payload, 'secret_key', algorithm='HS256')
    return token

# 示例用法
token = generate_jwt(user_id=123)
print(f"Generated Token: {token}")
```
### 验证 JWT
验证 JWT 也很简单。以下是一个验证 JWT 的示例：
```python
import jwt

def verify_jwt(token):
    try:
        payload = jwt.decode(token, 'secret_key', algorithms=['HS256'])
        return payload
    except jwt.ExpiredSignatureError:
        return 'Token has expired'
    except jwt.InvalidTokenError:
        return 'Invalid token'

# 示例用法
payload = verify_jwt(token)
print(f"Payload from Token: {payload}")
```
### 其他
在JWT（JSON Web Token）中，payload的格式并不是固定的，但有一些标准的字段（称为“声明”）可以使用。exp 是一个标准的声明，用于表示令牌的过期时间。以下是一些常见的标准声明：

- iss（Issuer）：签发者
- sub（Subject）：主题
- aud（Audience）：受众
- exp（Expiration Time）：过期时间
- nbf（Not Before）：生效时间
- iat（Issued At）：签发时间
- jti（JWT ID）：唯一标识

在使用JWT时，algorithms 参数用于指定加密和解密JWT时使用的算法。常见的算法包括对称加密算法（如HS256）和非对称加密算法（如RS256）。以下是一些常见的JWT算法：

- HS256：HMAC using SHA-256
- HS384：HMAC using SHA-384
- HS512：HMAC using SHA-512
- RS256：RSASSA-PKCS1-v1_5 using SHA-256
- RS384：RSASSA-PKCS1-v1_5 using SHA-384
- RS512：RSASSA-PKCS1-v1_5 using SHA-512
- ES256：ECDSA using P-256 and SHA-256
- ES384：ECDSA using P-384 and SHA-384
- ES512：ECDSA using P-521 and SHA-512

### 使用非对称加密算法（RS256）
```python
import jwt
import datetime

# 生成 JWT
def generate_jwt_rs256(user_id, private_key):
    payload = {
        'user_id': user_id,
        'exp': datetime.datetime.utcnow() + datetime.timedelta(hours=1)
    }
    token = jwt.encode(payload, private_key, algorithm='RS256')
    return token

# 验证 JWT
def verify_jwt_rs256(token, public_key):
    try:
        payload = jwt.decode(token, public_key, algorithms=['RS256'])
        return payload
    except jwt.ExpiredSignatureError:
        return 'Token has expired'
    except jwt.InvalidTokenError:
        return 'Invalid token'

# 示例用法
private_key = """-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----"""
public_key = """-----BEGIN PUBLIC KEY-----
...
-----END PUBLIC KEY-----"""

token_rs256 = generate_jwt_rs256(user_id=123, private_key=private_key)
print(f"Generated Token (RS256): {token_rs256}")

payload_rs256 = verify_jwt_rs256(token_rs256, public_key=public_key)
print(f"Payload from Token (RS256): {payload_rs256}")
```

