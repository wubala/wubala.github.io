---
layout: post
title: Linux下Git和GitHub使用方法
category: git & github
tags: [git & github]
---

## Linux下Git和GitHub环境的搭建

### 安装前的准备

* 检查是否安装ssh,如果没有则安装
* 检查是否存在ssh公钥

```linux
cd ~/.ssh
```

### 安装Git

```linux
sudo apt-get install git
```

### 生成ssh公钥

```linux
ssh-keygen -t rsa -C "your_email@youremail.com"
```

### 将公钥添加到github

打开github，找到账户里面添加SSH，把~/.ssh/idrsa.pub的内容复制到key里面。

### 测试是否生效

```linux
ssh -T git@github.com
balabala...
//输入yes
Hi username! 
You've successfully authenticated, but GitHub does not provide shell access.
//连接成功
```

### 配置Git的配置文件

```linux
//配置用户名
git config --global user.name "your name" 
//配置email
git config --global user.email "your email" 
```

## github的使用

### 利用Git从本地上传到GitHub

```linux
cd your-repo
//生成.git文件
git init
//创建一个本地仓库origin
git remote add origin git@github.com:yourName/yourRepo.git
//添加xxx到本地仓库，也可使用 'git add .' 来自动判断要添加那些文件
git add xxx 
//添加到本地仓库
git commit -m "提交说明"
//提交到远程仓库
git push origin master 
```

### 从GitHub克隆项目到本地

```linux
//先clone url
git "git clone https://github.com/xxx/xxx.git"
//更新本地仓库
git fetch origin
//更新的内容合并到本地分支
git merge origin/master
//如果不想手动去合并，这个命令可以拉取最新版本并自动合并
git pull <本地仓库> master
```

### GitHub的分支管理

```linux
//创建一个本地分支
git branch <新分支名字>
//将本地分支同步到GitHub上面
git push <本地仓库名> <新分支名>
//切换到新建立的分支
git checkout <新分支名>
//为你的分支加入一个新的远程端
git remote add <远程端名字> <地址>
//查看当前仓库有几个分支
git branch
//从本地删除一个分支
git branch -d <分支名称>
//同步到GitHub上面删除这个分支
git push <本地仓库名>:<分支名>

```
