---
title: ubuntu常用操作指令
---

# 系统操作
 - 更新系统：apt-get update
 - 查看系统中文件的使用情况：df -h
 - 查看当前目录下个文件及目录的使用情况：du -sh *
 - 磁盘使用情况：df -h
# 文件
 - 查看当前文件目录：ls
 - 进入文件目录：cd
 - 返回上级文件目录：cd ..
 - 返回文件根目录：cd
 - 删除当前目录下的所有文件：rm -f *
 - 删除文件：rm -rf yourfilename
# 端口
 - 查看已连接的服务端口：netstat -a
 - 查看所有服务端口：netstat -ap
 - 查看指定端口：netstat -ap | grep 8080 或 lsof -i:8080
 - 关闭指定端口程序：kill yourPID号
# 查看进程信息
 - ps
 - ps -aux 查看所有进程，每行一个程序
 - top 显示当前运行程序
 - kill 98 （98为PID号，）
 - kill -9 98 （强制杀死98）
# 安装软件
 - 安装git：apt-get install git
 - 安装curl：apt-get install curl
# 卸载软件
 - sudo apt-get remove yoursoftname
# 下载
 - curl -O https://dl.google.com/go/go1.12.6.linux-amd64.tar.gz
# 解压
 - tar -C /root/eth -xzf go1.12.6.linux-amd64.tar.gz（PS：路径 “/root/eth” 可以修改为自己的路径）
# 环境变量
 - 配置环境变量
`mkdir -p ~/go; echo "export GOPATH=$HOME/go" >> ~/.bashrc`（PS：进入目录）
`echo "export PATH=$PATH:/root/eth/go/bin" >> ~/.bashrc`（PS：路径 “:/root/eth/go/bin” 为 ”:/自己的安装目录/go/bin“）
`source ~/.bashrc`（PS：立即生效）
 - 查看环境变量：env
 - 修改环境变量：vi ~/.bashrc
# tumx
 - 查看已存在的回话：tmux ls
 - 新建：tmux new -s yournumber
 - 进入指定回话：tmux a -t yournumber