### 一、Linux

### 1、使用wget命令下载

```
wget <http://download.redis.io/releases/redis-5.0.7.tar.gz>
```

### 2、解压到指定路径

```
tar -xvf redis-5.0.7.tar.gz
mv redis-5.0.7 /usr/local/redis
```

### 3、编译

```
cd /usr/local/redis
make
```

### 4、安装

```
# 方法一
cd src
make PREFIX=/usr/local/redis install
# 方法二 在redis目录下
make install
```

### 5、启动

```
./bin/redis-server ./redis.conf
```

### 6、**将redis做成一个服务**

（1）复制脚本到/etc/nit.d目录

```
cp /usr/local/src/redis/utils/redis_init_script /etc/init.d/redis
```

将redis_init_script复制到/etc/rc.d/init.d/，同时易名为redis。

如果这时添加注册服务：chkconfig --add redis，将报错误：redis服务不支持chkconfig。为此，我们需要更改redis脚本。

（2）更改redis脚本

打开使用vi打开脚本，查看脚本信息：vim /etc/rc.d/init.d/redis

看到的内容如下(下内容是**更改好**的信息)：

```
#!/bin/sh
#chkconfig: 2345 80 90
# Simple Redis init.d script conceived to work on Linux systems
# as it does use of the /proc filesystem.

REDISPORT=6379
EXEC=/usr/local/redis/bin/redis-server
CLIEXEC=/usr/local/redis/bin/redis-cli

PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="/etc/redis/${REDISPORT}.conf"

case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
                echo "$PIDFILE exists, process is already running or crashed"
        else
                echo "Starting Redis server..."
                $EXEC $CONF &
        fi
        ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
                echo "$PIDFILE does not exist, process is not running"
        else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
                $CLIEXEC -p $REDISPORT shutdown
                while [ -x /proc/${PID} ]
                do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
        fi
        ;;
    *)
        echo "Please use start or stop as first argument"
        ;;
esac
```

和原配置文件相比：

1、文件是没有以下第2行的内容的，#chkconfig: 2345 80 90

2、文件EXEC、CLIEXEC参数，也是有所更改。

```
EXEC=/usr/local/redis/bin/redis-server
CLIEXEC=/usr/local/redis/bin/redis-cli
```

3、edis开启的命令，以后台运行的方式执行。$EXEC $CONF &

4、edis配置文件拷贝到/etc/redis/${REDISPORT}.conf

```
mkdir /etc/redis
```

5、注册服务

```
mkdir /etc/redis

cp /usr/local/redisXXX/redis.conf /etc/redis/6379.conf

# 注册redis服务
chkconfig --add redis

# Ubuntu 中 `chkconfig` 已经被 `sysv-rc-conf` 所替代
apt-get install  sysv-rc-conf
sysv-rc-conf redis on
```

**（3）启动redis服务**

```
service redis start
# centos7以后使用方式
systemctl  start redis.service
```

### 7、edis的命令所在目录添加到系统参数PATH中

修改profile文件：vi /etc/profile

在最后行追加: `export PATH=``"$PATH:/usr/local/redis/bin"`
