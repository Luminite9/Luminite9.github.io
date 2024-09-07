---
title: Linux设置某个用户在某个文件夹下的权限
category: Computer
tag:
    - server
---

Linux通常管理权限的方法有chmod，但是如果想将对某个文件夹的权限给予某个用户，这种方式可能不够便捷，我们有更便捷的方法：

```bash
sudo setfacl -R -d -m u:username:rwx /path/to/directory
```

这一命令将会给予username用户在/path/to/directory的所有权限，包括其所有子文件夹、文件、未来的文件夹与文件的权限，其中参数的含义如下：

- `-R`：递归地将权限应用到文件夹及其所有子文件和子文件夹。
- `-d`：设置默认ACL，确保新创建的文件和文件夹也继承这些权限。
- `-m`：修改文件/文件夹的ACL。
- `u:username:rwx`：为指定的用户（username）授予读（r）、写（w）、执行（x）权限。
- `/path/to/directory`：目标文件夹的路径。