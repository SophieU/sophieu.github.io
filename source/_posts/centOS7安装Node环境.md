---
title: CentOS7安装Node环境
date: 2019-01-28
tag: [CentOS,Node]
categories: 后端学习
---

## CentOS下安装Node
```bash
# 1、获取资源
cd /usr/local/src
wget  https://npm.taobao.org/mirrors/node/v10.13.0/node-v10.13.0.tar.gz
# 2、解压
tar xvf node-v10.13.0.tar.gz
# 3、进入解压后node文件夹开始编译
cd node-v10.13.0/
./configure
make

# 4、安装Node
make install
# 5、验证是否正确安装
node -v
```
注意：
- 第3步编译过程中若报错：` C++ Compiler too old, need g++ 4.9.4 or clang++ 3.4.2 (CXX=g++)`，那需要先升级一下 gcc，编译正常的话请忽略直接跳至第五步开始安装,通过`gcc -v`查看版本

## 安装PM2
```bash
# 安装
npm install pm2 -g
# 查看安装后版本
pm2 -v
```