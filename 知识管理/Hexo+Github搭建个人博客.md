# Hexo+Github搭建个人博客



Hexo官方文档：<https://hexo.io/docs/>



安装Hexo需要：

- [Node.js](http://nodejs.org/) (Should be at least Node.js 8.10, recommends 10.0 or higher)
- [Git](http://git-scm.com/)

## 安装Hexo

```
$ npm install -g hexo-cli
```

-g : 全局安装

或者采用局部安装：

```
$ npm install hexo
```

安装位置为执行命令目录`./node_modules/.bin`



## 使用hexo

### 初始化目录

```
`$ hexo init <folder>$ cd <folder>$ npm install`
```

初始化后：

```
`.├── _config.yml├── package.json├── scaffolds├── source|   ├── _drafts|   └── _posts└── themes`
```

#### _config.yml

Site [configuration](https://hexo.io/docs/configuration) file. You can configure most settings here.

#### package.json

Application data. The [EJS](https://ejs.co/), [Stylus](http://learnboost.github.io/stylus/) and [Markdown](http://daringfireball.net/projects/markdown/) renderers are installed by default. If you want, you can uninstall them later.

```
package.json{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": ""
  },
  "dependencies": {
    "hexo": "^3.8.0",
    "hexo-generator-archive": "^0.1.5",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.1",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.3.1",
    "hexo-renderer-stylus": "^0.3.3",
    "hexo-renderer-marked": "^0.3.2",
    "hexo-server": "^0.3.3"
  }
}
```

#### scaffolds

[Scaffold](https://hexo.io/docs/writing#Scaffolds) folder. When you create a new post, Hexo bases the new file on the scaffold.

#### source

Source folder. This is where you put your site’s content. Hexo ignores hidden files and files or folders whose names are prefixed with `_` (underscore) - except the `_posts` folder. Renderable files (e.g. Markdown, HTML) will be processed and put into the `public` folder, while other files will simply be copied.

#### themes

[Theme](https://hexo.io/docs/themes) folder. Hexo generates a static website by combining the site contents with the theme.



具体配置参考官网文档。