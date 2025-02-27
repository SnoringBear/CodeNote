### 1、安装redis

```shell
docker pull redis
  
docker run -itd -e TZ=Asia/Shanghai --name redis -p 6379:6379 redis:latest
```

### 

### 2、安装 mysql

```shell
 docker pull mysql:5.7.37
  
 docker run -itd -e TZ=Asia/Shanghai --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mysql:5.7.37
```

### 

### 3、安装mongo

```shell
docker pull mongo
  
  
docker run -itd -e TZ=Asia/Shanghai --name mongo -p 27017:27017 mongo
```





### 4、windows启动脚本

```shell
@echo off
setlocal

REM 检查Docker Desktop是否已安装
if not exist "C:\Program Files\Docker\Docker\resources\bin\docker.exe" (
    echo Docker Desktop未安装或未正确安装，请先安装Docker Desktop。
    pause
    exit
)

REM 启动Docker Desktop
start "" "C:\Program Files\Docker\Docker\Docker Desktop.exe"

ping 127.0.0.1 -n 40 >nul

REM 启动MySQL容器
"C:\Program Files\Docker\Docker\resources\bin\docker.exe" start mysql
"C:\Program Files\Docker\Docker\resources\bin\docker.exe" start redis

echo MySQL start
```