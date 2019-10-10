---
layout: post
title: Python密码处理
category: 技术
tags: python
description:
---

python密码处理(可用于生产模式)
　　
```
import os
from hashlib import sha256
from hmac import HMAC

def encrypt_password(password, salt=None):
    """Hash password on the fly."""
    if salt is None:
        salt = os.urandom(8) # 64 bits.

    assert 8 == len(salt)
    assert isinstance(salt, str)

    if isinstance(password, unicode):
        password = password.encode('UTF-8')

    assert isinstance(password, str)

    result = password
    for i in xrange(10):
        result = HMAC(result, salt, sha256).digest()

    return salt + result
```
 

这里先随机生成 64 bits 的 salt，再选择 SHA-256 算法使用 HMAC 对密码和 salt 进行 10 次叠代混淆，最后将 salt 和 hash 结果一起返回。

使用的方法很简单：

```
hashed = encrypt_password('secret password')
``` 

下面是验证函数，它直接使用 encrypt_password 来对密码进行相同的单向转换并比较

```
def validate_password(hashed, input_password):
    return hashed == encrypt_password(input_password, salt=hashed[:8])

assert validate_password(hashed, 'secret password')
```

密码项目代码查看[show me the code](https://github.com/fankcoder/ShowMeTheCode)下 s21/ 目录
