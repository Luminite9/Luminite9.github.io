---
title: 服务器添加新用户并设置密码
category: Computer
tag:
    - server
---

## 添加新用户

使用`useradd`命令添加新用户。

```bash
sudo useradd -m -d /home/username -s /bin/bash username
```

- useradd，告诉服务器添加该用户
- -m，为用户创建home目录，如果不指定则该用户没有home目录
- -d，指定该用户home目录的路径
- -s，指定该用户的登陆默认shell，这里指定了bash
- username，即为新用户的用户名

## 设置密码

使用`passwd`命令设置密码。

```bash
sudo passwd username
```

之后系统会要求设置并确认该用户的登陆密码。