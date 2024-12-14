---
title: Linux scp
category: Computer
tag:
    - Linux
---

Linux scp 命令用于 Linux 之间复制文件和目录。

### 命令格式

```bash
scp [可选参数] file_source file_target 
```

### 从本地复制到远程

复制文件：

```bash
scp local_file remote_username@remote_ip:remote_folder 
# an example
scp /home/space/music/1.mp3 root@www.runoob.com:/home/root/others/music
```

复制目录：

```bash
scp -r local_folder remote_username@remote_ip:remote_folder
```

### 从远程复制到本地

```bash
scp root@www.runoob.com:/home/root/others/music/1.mp3 /home/space/music/
```

### 使用端口

```bash
scp -P 22 remote@www.runoob.com:/usr/local/sin.sh /home/administrator
```
