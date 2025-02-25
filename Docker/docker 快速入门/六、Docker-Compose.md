### 现存问题

在上节，我们运行了两个容器：Web 项目 + Redis
如果项目依赖更多的第三方软件，我们需要管理的容器就更加多，每个都要单独配置运行，指定网络。
这节，我们使用 docker-compose 把项目的多个服务集合到一起，一键运行。

本文档课件配套 [视频教程](https://www.bilibili.com/video/BV11L411g7U1?p=6)

### 安装 Docker Compose

- 如果你是安装的桌面版 Docker，不需要额外安装，已经包含了。
- 如果是没图形界面的服务器版 Docker，你需要单独安装 [安装文档](https://docs.docker.com/compose/install/#install-compose-on-linux-systems)
- 运行docker-compose检查是否安装成功

### 编写脚本

要把项目依赖的多个服务集合到一起，我们需要编写一个docker-compose.yml文件，描述依赖哪些服务
参考文档：https://docs.docker.com/compose/

```plain
version: "3.7"

services:
  app:
    build: ./
    ports:
      - 80:8080
    volumes:
      - ./:/app
    environment:
      - TZ=Asia/Shanghai
  redis:
    image: redis:5.0.13
    volumes:
      - redis:/data
    environment:
      - TZ=Asia/Shanghai

volumes:
  redis:
```

容器默认时间不是北京时间，增加 TZ=Asia/Shanghai 可以改为北京时间

### 跑起来

在docker-compose.yml 文件所在目录，执行：docker-compose up就可以跑起来了。
命令参考：https://docs.docker.com/compose/reference/up/

在后台运行只需要加一个 -d 参数docker-compose up -d
查看运行状态：docker-compose ps
停止运行：docker-compose stop
重启：docker-compose restart
重启单个服务：docker-compose restart service-name
进入容器命令行：docker-compose exec service-name sh
查看容器运行log：docker-compose logs [service-name]现存问题
在上节，我们运行了两个容器：Web 项目 + Redis
如果项目依赖更多的第三方软件，我们需要管理的容器就更加多，每个都要单独配置运行，指定网络。
这节，我们使用 docker-compose 把项目的多个服务集合到一起，一键运行。



本文档课件配套 视频教程



安装 Docker Compose
如果你是安装的桌面版 Docker，不需要额外安装，已经包含了。
如果是没图形界面的服务器版 Docker，你需要单独安装 安装文档
运行docker-compose检查是否安装成功
编写脚本
要把项目依赖的多个服务集合到一起，我们需要编写一个docker-compose.yml文件，描述依赖哪些服务
参考文档：https://docs.docker.com/compose/



version: "3.7"



services:
app:
build: ./
ports:
\- 80:8080
volumes:
\- ./:/app
environment:
\- TZ=Asia/Shanghai
redis:
image: redis:5.0.13
volumes:
\- redis:/data
environment:
\- TZ=Asia/Shanghai



volumes:
redis:
容器默认时间不是北京时间，增加 TZ=Asia/Shanghai 可以改为北京时间



跑起来
在docker-compose.yml 文件所在目录，执行：docker-compose up就可以跑起来了。
命令参考：https://docs.docker.com/compose/reference/up/



在后台运行只需要加一个 -d 参数docker-compose up -d
查看运行状态：docker-compose ps
停止运行：docker-compose stop
重启：docker-compose restart
重启单个服务：docker-compose restart service-name
进入容器命令行：docker-compose exec service-name sh
查看容器运行log：docker-compose logs [service-name]