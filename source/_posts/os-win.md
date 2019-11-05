---
title: windows常用操作指令
---

# 端口
 - 查看所有端口占用：netstat -ano
 - 查看指定端口占用：netstat -ano|findstr 7070
 TCP    0.0.0.0:7070           0.0.0.0:0              LISTENING       77304
    TCP    [::]:7070              [::]:0                 LISTENING       77304
 - 查看端口占用程序名：tasklist|findstr 77304
javaw.exe                    77304 Console                   10    333,288 K