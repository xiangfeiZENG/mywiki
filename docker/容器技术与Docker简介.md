# 容器技术与Docker简介

到底什么是Docker？

![image-20190831084442533](/Users/zengxiangfei/Documents/mywiki/docker/images/images.png)





Docker能干什么？

- 简化配置
- 整合服务器
- 代码流水线管理
- 调试能力
- 提高开发效率
- 多租户
- 隔离应用
- 快速部署



kubernetes

![image-20190831084705487](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831084705487.png)



DevOps=文化+过程+工具

![image-20190831084746930](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831084746930.png)





![image-20190831085138280](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831085138280.png)





## 容器技术

### 简介

虚拟化技术：

![image-20190831090052016](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831090052016.png)

优点

- 资源池-物理机资源分配到不同的虚拟机
- 很容易扩展-加物理机or加虚拟机
- 很容易云化-亚马逊AWS，阿里云等



局限性：

- 每一个虚拟机都是一个完整的操作系统，要给其他分配资源，当虚拟机数量增多时，操作系统本身消耗的资源势必增多。

  



![image-20190831090432258](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831090432258.png)



![image-20190831090700988](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831090700988.png)



容器解决了什么问题？

- 解决了开发和运维之间的矛盾
- 在开发和运维之间搭建了一个桥梁，是实现devops的最佳解决方案



什么是容器？

![image-20190831090908640](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831090908640.png)



容器和虚拟机的区别？

![image-20190831091037279](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831091037279.png)



虚拟化+容器

![image-20190831091127249](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831091127249.png)



Docker产生过程

![image-20190831091840227](/Users/zengxiangfei/Documents/mywiki/docker/images/image-20190831091840227.png)



### Docker初体验

安装

community：免费

在mac上安装docker：

<https://docs.docker.com/docker-for-mac/install/>

在centos上安装docker

<https://docs.docker.com/install/linux/docker-ce/centos/>







