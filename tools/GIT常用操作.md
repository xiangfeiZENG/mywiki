# GIT常用操作

设置name和email

```
xfei-mac:mmall zengxiangfei$ git config --global user.email "xiangfeizeng@outlook.com"
xfei-mac:mmall zengxiangfei$ git config --global user.name "zengxiangfei"

```

首次提交以及建立分支步骤：

```
$ touch .gitignore
$ touch README.md
$ git init
$ git add .
$ git commit -m "first commit,init project"
此时还在本地仓库

# 关联远程仓库
$ git remote add origin git@github.com:xiangfeiZENG/mmall.git
# 创建新分支
$ git checkout -b v1.0 origin/master
# 提交新分支
$ git push origin HEAD -u



```

