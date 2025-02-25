### 一、uname



uname（unix name的简写）是一个Unix和类Unix操作系统上的程序，可以打印当前计算机和操作系统的名称、版本及其他细节。uname系统调用和命令第一次出现在PWB/UNIX上。这两个由POSIX指定



GNU版本的uname包含在“sh-utils”或“coreutils”包中。uname本身不是一个独立的程序



### 二、解压文件



### 1、解压tar.xz压缩文件



```plain
// 解压成tar.gz
xz -d xxx.tar
// tar 解压
tar -xvf xxx.tar
```



### 三、时间



### 1、设置时间



date -s "2020-3-19 18:00:00"



### 2、设置时区



```plain
timedatectl list-timezones |grep Shanghai    #查找中国时区的完整名称
Asia/Shanghai
timedatectl set-timezone Asia/Shanghai    #设置时区
```



### 3、同步时间



- 使用ntpdate 进行时间同步 

- 命令:ntpdate -u 210.72.145.44
- 中国国家授时中心：210.72.145.44
- 或者ntpdate [0.arch.pool.ntp.org](http://0.arch.pool.ntp.org/)

- ntp自建时间服务器



### 4、修改Docker容器的时间和宿主时间一致



在容器中修改下/etc/localtime文件的名称，避免冲突。



```plain
cd /etc/``mv localtime localtime_bak``cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```



### 四、ssh连接



- ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Root123456';
- ssh -i bole [bole@52.68.191.231](mailto:bole@52.68.191.231) -p 58000



### 五、netstat



### 1、netstat -anp



### 六、systemctl



Linux 服务管理两种方式service和systemctl



### 1、初始化程序



-  centos7之前 

init      服务管理:service

 

-  centos7及以后 

systemd      服务管理:systemctl

 



### 2.管理服务(unit)



systemctl 提供了一组子命令来管理单个的 unit，其命令格式为：



systemctl [command] [unit]



command 主要有：



start：立刻启动后面接的 unit。



stop：立刻关闭后面接的 unit。



restart：立刻关闭后启动后面接的 unit，亦即执行 stop 再 start 的意思。



reload：不关闭 unit 的情况下，重新载入配置文件，让设置生效。



enable：设置下次开机时，后面接的 unit 会被启动。



disable：设置下次开机时，后面接的 unit 不会被启动。



status：目前后面接的这个 unit 的状态，会列出有没有正在执行、开机时是否启动等信息。



is-active：目前有没有正在运行中。



is-enable：开机时有没有默认要启用这个 unit。



kill ：不要被 kill 这个名字吓着了，它其实是向运行 unit 的进程发送信号。



show：列出 unit 的配置。



mask：注销 unit，注销后你就无法启动这个 unit 了。



unmask：取消对 unit 的注销。



### 七、apt-get



apt-get 命令适用于 deb 包管理式的 Linux 操作系统（Debian、Ubuntu等），主要用于自动从互联网软件仓库中搜索、下载、安装、升级、卸载软件或操作系统。



### 1、安装 软件包



```plain
apt-get install PackageName                 // 普通安装
apt-get install PackageName=VersionName     // 安装指定包的指定版本
apt-get --reinstall install PackageName     // 重新安装

apt-get build-dep PackageName               // 安装源码包所需要的编译环境
apt-get -f install                          // 修复依赖关系

apt-get source PackageName                  // 下载软件包的源码
```



### 2、卸载 软件包



```plain
apt-get remove PackageName              // 删除软件包, 保留配置文件

apt-get --purge remove PackageName      // 删除软件包, 同时删除配置文件
apt-get purge PackageName               // 删除软件包, 同时删除配置文件

apt-get autoremove PackageName          // 删除软件包, 同时删除为满足依赖
                                        // 而自动安装且不再使用的软件包

apt-get --purge autoremove PackageName  // 删除软件包, 删除配置文件,
                                        // 删除不再使用的依赖包

apt-get clean && apt-get autoclean      // 清除 已下载的软件包 和 旧软件包
```



### 3、更新 软件包



```plain
apt-get update                  // 更新安装源（Source）
apt-get upgrade                 // 更新已安装的软件包
apt-get dist-upgrade            // 更新已安装的软件包（识别并处理依赖关系的改变）
do-release-upgrade              // Ubuntu官方推荐的系统升级方式,若加参数-d还可以升级到开发版本,但会不稳定
```



### 4、查询 软件包



```plain
dpkg -l                             // 列出已安装的所有软件包

apt-cache search PackageName        // 搜索软件包
apt-cache show PackageName          // 获取软件包的相关信息, 如说明、大小、版本等

apt-cache depends PackageName       // 查看该软件包需要哪些依赖包
apt-cache rdepends PackageName      // 查看该软件包被哪些包依赖

apt-get check                       // 检查是否有损坏的依赖
```



### 八、内存



### 1、查看可用内存



```plain
free -h
```



### 2、清理缓存

```bash
# buff/cache是由于系统读写导致的文件缓存，没有及时释放。
# 清理内存命令：
echo 1 > /proc/sys/vm/drop_caches
echo 2 > /proc/sys/vm/drop_caches
echo 3 > /proc/sys/vm/drop_caches
```