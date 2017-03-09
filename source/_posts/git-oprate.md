---
title: 常用git操作命令
date: 2017-02-10 21:20:22
---
## 前言
由于linux功底薄弱，平时使用git管理项目时候常借助工具Git Extensions操作，在windows平台还是挺方便的，但是对于linux无界面化操作，那是相当无奈，最近心血来潮购了一款低配[腾讯云](http://www.qcloud.com)，闲来练手，在上面装了git方便操作，在此记录下操作备忘，闲话不扯了上干货。
## 常用git命令

### 远程操作
**拉代码到本地**
```
git clone https://github.com/dawnyu/dawns.git
```
**查看远程仓库**
```
git remote -v
```
**添加远程仓库**
```
git remote add [name] [url]
```
例：git remote add demo https://github.com/dawnyu/demo.git 运行命令git remote -v 查看远程仓库，会看到已经创建成功
```
git remote -v
demo    https://github.com/dawnyu/demo.git (fetch)
demo    https://github.com/dawnyu/demo.git (push)
origin  https://github.com/dawnyu/dawns.git (fetch)
origin  https://github.com/dawnyu/dawns.git (push)
```
**删除仓库**
```
git remote rm [name]
```
例：git remote rm demo

**修改远程仓库**
```
git remote set-url --push [name] [newUrl]
例：
git remote add demo https://github.com/dawnyu/demo.git
git remote set-url --push demo1 https://github.com/dawnyu/demo1.git
```
**拉取远程仓库**
```
git pull [remoteName] [localBranchName]
```
可以直接在本地项目根目录执行git pull拉取
**推送远程仓库**
```
git push [remoteName] [localBranchName]
```
不带后面参数直接`git push`也可以

*如果想把本地的某个分支test提交到远程仓库，并作为远程仓库的master分支，或者作为另外一个名叫test的分支，如下*
```
$git push origin test:master         // 提交本地test分支作为远程的master分支
$git push origin test:test              // 提交本地test分支作为远程的test分支
```
### 分支操作
**查看本地分支**
```
git branch
```
**查看远程分支**
```
git branch -r
```
**创建本地分支**
```
git branch [name]

example: git branch demo  //这里需要注意创建后当前分支不会自动切换
```
**切换分支**
```
git checkout [name]
example: 比如切换到上文创建的demo分支 git checkout demo
```
**创建新分支并切换**
```
git checkout -b [name]
example: git branch -b demo1
```
**删除分支-d**
```
git branch -d/ [name] 

```
**合并分支**
```
git merge [name]
example: git merge master //把master 分支合并到当前分支
```
**创建远程分支**
```
git push origin [name]
注意 这个name要在本地已经创建
```
**删除远程分支**
```
git push origin :heads/[name] or git push origin :[name]
```
**创建空的分支(注意执行前需要提交代码，否则清空)**
```
$git symbolic-ref HEAD refs/heads/[name]
$rm .git/index
$git clean -fdx
```

### 版本操作相关
**查看版本**
```
git tag
```
**删除版本**
```
git tag -d [name]
```
**创建版本**
```
git tag [name]
```
**查看提交状态**
```
git status
```
**提交**
```
git commit
```
**带注释提交**
```
git commit -am "first commit"
```
**将文件推到服务器上**
```
git push origin master
```
**显示远程库里面的资源**
```
git remote show origin
```
**将本地库与服务器上的库进行关联 **
```
git push prigin master:dev
```
**切换远程分支**
```
git checkout --track origin/dev
```
**将分支与当前分支合并**
```
git merge origin/master
```
**提交本地全部文件**
```
git add .
```
**查看用户所有配置**
```
git config --list
```
**查看所有提交**
```
git ls-files
```
**删除一个文件**
```
git rm [file name]
```
**添加一个文件**
```
git add [file name]
```
**git commit -v 查看提交差异**
```
git commit -v
```
**git commit -m 添加提交信息**
```
git commit -m
```
**添加文件并提交差异**
```
git commit -a -v
```
**查看提交日志**
```
git log
```
**查看未提交本地的更新文件**
```
git diff
```
**移除文件**
```
git rm [name] //缓存区和工作区都会移除
git rm -f [name] //强行移除
git rm --cache [name] //从缓存区移除
git commit -m "remove" [name]
```
**git stash push 提交文件到一个临时空间**
```
git stash push
```
**将文件从临时空间pop下来**
```
git stash pop
```
```
git remote add origin git@github.com:username/Hello-World.git
git push origin master 将本地项目给提交到服务器中
```
**本地与服务器端同步**
```
git pull 
```

```
git push (远程仓库名) (分支名) 将本地分支推送到服务器上去。
```
**拉取全部**
```
git fetch 相当于是从远程获取最新版本到本地，不会自动merge,
```
```
git branch branch_0.1 master 从主分支master创建branch_0.1分支
```
```
git branch -m branch_0.1 branch_1.0 将branch_0.1重命名为branch_1.0
```


### 简单例子
```
mkdir demoApp //创建文件夹
cd demoApp //切换文件夹
git init // git 初始化项目
touch README //生成空README文件
git add README//添加文件到git
git commit -m 'first commit' //提交本地
git remote add origin [远程地址] //添加远程分支
git push -u origin master//推送远程分支

```


参考文章：[Git 常用命令大全](http://blog.csdn.net/dengsilinming/article/details/8000622)