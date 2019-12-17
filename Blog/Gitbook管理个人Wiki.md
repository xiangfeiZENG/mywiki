# Gitbook管理个人Wiki

参考文章：

gitbook使用：<http://www.chengweiyang.cn/gitbook/index.html>

持续集成：<https://www.jianshu.com/p/ec5f57d96d66>

markdown Toc显示问题：<https://www.twblogs.net/a/5cb95fc6bd9eee0f00a23ad9>

gitbook生成：<https://my.oschina.net/yugj/blog/3031020>



[TOC]

## 安装与使用

```bash
$ npm install gitbook -g
```

gitbook 的基本用法非常简单，基本上就只有两步：

1. 使用 `gitbook init` 初始化书籍目录
2. 使用 `gitbook serve` 编译书籍

下面将结合一个非常简单的实例，来介绍 gitbook 的基本用法。

### gitbook init

首先，创建如下目录结构：

```bash
$ tree book/
book/
├── README.md
└── SUMMARY.md

0 directories, 2 files
```

## README.md 和 SUMMARY.md 是两个必须文件，README.md 是对书籍的简单介绍：

```bash
$ cat book/README.md 
# README

This is a book powered by [GitBook](https://github.com/GitbookIO/gitbook).
```

SUMMARY.md 是书籍的目录结构。内容如下：

```bash
$ cat book/SUMMARY.md 
# SUMMARY

* [Chapter1](chapter1/README.md)
  * [Section1.1](chapter1/section1.1.md)
  * [Section1.2](chapter1/section1.2.md)
* [Chapter2](chapter2/README.md)
```

创建了这两个文件后，使用 `gitbook init`，它会为我们创建 SUMMARY.md 中的目录结构。

```bash
$ cd book
$ gitbook init
$ tree
.
├── README.md
├── SUMMARY.md
├── chapter1
│   ├── README.md
│   ├── section1.1.md
│   └── section1.2.md
└── chapter2
    └── README.md

2 directories, 6 files
```

注意：在我的实验中，`gitbook init` 只支持**两级**目录！

### gitbook serve

书籍目录结构创建完成以后，就可以使用 `gitbook serve` 来编译和预览书籍了：

```bash
$ gitbook serve
Press CTRL+C to quit ...

Live reload server started on port: 35729
Starting build ...
Successfully built!

Starting server ...
Serving book on http://localhost:4000
```

`gitbook serve` 命令实际上会首先调用 `gitbook build` 编译书籍，完成以后会打开一个 web 服务器，监听在本地的 4000 端口。

现在，可以用浏览器打开 [http://127.0.0.1:4000](http://127.0.0.1:4000/) 查看书籍的效果，如下图：



现在，gitbook 为我们创建了书籍目录结构后，就可以向其中添加真正的内容了，文件的编写使用 markdown 语法，在文件修改过程中，每一次保存文件，`gitbook serve` 都会自动重新编译，所以可以持续通过浏览器来查看最新的书籍效果！





## 发布到GitPages

除了能够将书籍发布到 GitBook.com 外，还可以将书籍发布到 GitHub Pages，由于没有找到官方文档，所以这里记录的是我自己正在使用的一种方法。

如果读者不了解 GitHub Pages 为何物，简单说就是一个可以托管静态网站的 Git 项目，支持使用 markdown 语法以及 Jekyll 来构建，或者直接使用已经生成好的静态站点。详细可以参考 [GitHub Pages 主页](https://pages.github.com/)。

由于 gitbook 书籍可以通过 `gitbook` 本地构建出 site 格式，所以可以直接将构建好的书籍直接放到 GitHub Pages 中托管，之后，可以通过如下地址访问书籍：

```
<username>.github.io/<project>
```

例如：这本书中使用的例子 'test' 项目可以通过地址：*chengweiv5.github.io/test* 来访问。

当访问 *xiangfeiZENG.github.io/test* 时，会访问 *xiangfeiZENG/test* 项目的 *gh-pages* 分支的内容，所以需要为项目创建一个 *gh-pages* 分支，并且将静态站点内容放入其中。也就是说，test 项目将有如下两个分支：

- master, 保存书籍的源码
- gh-pages, 保存书籍编译后的 HTML 文件





## Gitbook 生成Summary

#### 方案1，[julianxhokaxhiu](https://github.com/julianxhokaxhiu)/**gitbook-plugin-summary**

Google基本上用的是 gitbook-plugin-summary

<https://github.com/julianxhokaxhiu/gitbook-plugin-summary#readme>

安装

```
npm i gitbook-plugin-summary --save
```

配置book.json

```
{
  "plugins": [
    "summary"
  ]
}
```

本地使用方案一后报错，有异常字符，未找到解决方案，采用的是方案二。

#### 方案2，[imfly](https://github.com/imfly)/**gitbook-summary**

github地址：<https://github.com/imfly/gitbook-summary>

安装

```
npm install -g gitbook-summary
```

简单使用：

```
cd /path/to/your/book/
book sm
```

demo：

```
tree .
.
├── ORDER
│   ├── 0-README.md
│   └── 1-orderInfo.md
├── README.md
├── SUMMARY.md
└── USER
    ├── 0-README.md
    └── 1-userInfo.md
```

result:

```
cat SUMMARY.md
# Your Book Title

- ORDER
  * [0 README](ORDER/0-README.md)
  * [1 Order Info](ORDER/1-orderInfo.md)
- USER
  * [0 README](USER/0-README.md)
  * [1 User Info](USER/1-userInfo.md)
```



## travis-ci自动化构建部署GitHub Pages（gitbook）

### Github Pages

github pages可以当做你或者你的项目的 Websites，那么我们可以知道 GitHub Pages 有两种最基本的用法：

- 作为你自己（或者组织）的网站或者博客（访问地址示例：[http://username.github.io](https://links.jianshu.com/go?to=http%3A%2F%2Fusername.github.io)）
- 作为你某一个项目的网站（访问地址示例：[http://username.github.io/projectname](https://links.jianshu.com/go?to=http%3A%2F%2Fusername.github.io%2Fprojectname)）
   

如果是第二种的话部署的时候需要在源仓库的基础上再创建一个gh-pages的分支，然后部署的时候需要将在源仓库生成的静态文件单独拎出来放到gh-pages分支上push上去，整个操作流程比较麻烦，而[travis-ci](https://links.jianshu.com/go?to=https%3A%2F%2Ftravis-ci.org%2F)可以很方便帮们解决这个痛点。

### 配置travis-ci

#### 登录 travis

- [https://travis-ci.org/](https://links.jianshu.com/go?to=https%3A%2F%2Ftravis-ci.org%2F)（免费）
- [https://travis-ci.com/](https://links.jianshu.com/go?to=https%3A%2F%2Ftravis-ci.com%2F)（收费）

登录时使用GitHub账号登录，此时为了让travis获取调用GitHub Api的权限需要在GitHub上生成一个token。
 在github([https://github.com/settings/tokens](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fsettings%2Ftokens))   Settings/ Developer settings 新生成一个travis专用的token.



![14955720-2036d680eee0053b](/Users/zengxiangfei/Documents/mywiki/Blog/images/14955720-2036d680eee0053b.png)image

### 在travis 配置token环境变量

在你需要构建的仓库的设置里添加GITHUB_TOKEN环境变量。



![14955720-2a6be421558755a3](/Users/zengxiangfei/Documents/mywiki/Blog/images/14955720-2a6be421558755a3.png)

image

### 创建 配置文件

在你的github pages 项目里面新建.travis.yml配置文件。

```yml
language: node_js # 构建所需的语言环境
node_js:
  - "v11.14.0"  # 对应的版本
branches:
  only:
  - master    # 构建的分支
cache:
  directories:
  - node_modules # 依赖缓存的目录
before_install:
- export TZ='Asia/Shanghai'  # 设置时区
install:
 - npm install -g gitbook-cli # 安装编译工具
script:
- gitbook build
deploy:
  provider: pages
  skip-cleanup: true
  github-token: $GITHUB_TOKEN # github 上的token环境变量
  local-dir: ./_book/ ## 根据情况自定义到静态文件输出目录
  target-branch: gh-pages
  verbose: true
  on:
    branch: master
```

### 测试

在github page的项目里面修改一点东西，push到GitHub上去就会在travis上看到触发build。



![14955720-9954a23b85c3d420](/Users/zengxiangfei/Documents/mywiki/Blog/images/14955720-9954a23b85c3d420.png)

image

大概等个2min你会发现你的github pages 已经更新了。

作者：朗月清风hyj

链接：https://www.jianshu.com/p/ec5f57d96d66

来源：简书

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。