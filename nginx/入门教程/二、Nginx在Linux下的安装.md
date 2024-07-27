由于Nginx被大量使用在Web服务器中，所以本教程选择了Centos服务器的7.4版本作为演示！

### 1. 下载

**进入官方下载页面：[**[**http://nginx.org/en/download.html](https://link.zhihu.com/?target=http%3A//nginx.org/en/download.html)**](http://nginx.org/en/download.html](https://link.zhihu.com/?target=http%3A//nginx.org/en/download.html))**，选择Stable version稳定版本进行下载，并上传至服务器。**



![img](二、Nginx在Linux下的安装.assets/1690640219628-c606bcd4-bb9f-4a06-b6de-2146385c33c3.jpeg)



### 2. 安装

上传tar包至服务器后，解压到当前目录

![img](二、Nginx在Linux下的安装.assets/1690640302062-ffab96b1-c3fa-46cb-9440-fe0cf56d044c.jpeg)

进入解压后的目录，执行configure脚本

![img](二、Nginx在Linux下的安装.assets/1690640346831-f96f2c8d-7169-4314-a1d4-248142523740.jpeg)

使用make命令进行安装

![img](二、Nginx在Linux下的安装.assets/1690640425315-a3cd62d8-0827-4089-8c35-91db897cbf15.jpeg)

安装完成后，使用whereis命令查找Nginx的安装位置 (默认安装在/user/local/目录下)

![img](二、Nginx在Linux下的安装.assets/1690640460859-777827d2-0748-4499-af95-09ee6ef65b40.jpeg)

进入安装目录下的bin文件夹，启动nginx服务，访问服务器默认的80端口查看是否安装成功！

![img](二、Nginx在Linux下的安装.assets/1690640501113-33049a6a-a571-4bf8-93ce-e44b8e9c22e5.jpeg)