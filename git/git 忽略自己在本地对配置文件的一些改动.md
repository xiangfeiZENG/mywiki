# git 忽略自己在本地对配置文件的一些改动

场景：
本地开发总是需要在配置文件中填入一些账号信息，而这些在提交到远程的时候如何忽略他们，不把他们提交呢

我的配置文件名称 config.init
输入以下命令

```
git update-index --assume-unchanged config.init
```

 

如果某一天对config.init 做了更改需要提交他

```
git update-index --no-assume-unchanged config.init
```