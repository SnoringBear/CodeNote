### 一、Gcc

### 1、安装gcc6

```bash
yum -y install centos-release-scl
yum -y install devtoolset-6-gcc devtoolset-6-gcc-c++ devtoolset-6-binutils
scl enable devtoolset-6 bash
```

###  2、安装gcc7  

```bash
yum -y install centos-release-scl
yum -y install devtoolset-7-gcc devtoolset-7-gcc-c++ devtoolset-7-binutils
scl enable devtoolset-7 bash
```

###  3、安装gcc9  

```bash
yum -y install centos-release-scl
yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
scl enable devtoolset-9 bash
# 需要注意的是scl命令启用只是临时的，退出shell或重启就会恢复原系统gcc版本。
# 如果要长期使用gcc 9的话：
echo "source /opt/rh/devtoolset-9/enable" >>/etc/profile
```

### 二、Ubuntu软件系列---如何安装deb安装包



1. 下载deb包到某文件夹
2. 在某文件夹右键“在这里打开终端”，此时可以看到，终端里的路径就是该文件夹的路径
3. 为了保险起见，我们先执行下面的指令：
   `sudo apt-get update`
4. 安装deb包(tips:)
   `sudo dpkg -i packname`
   输入packname时可以tab补全哦
   若出现依赖关系错误，第5步会自动修复
5. 修复依赖关系
   `sudo apt -f instal`
6. 完成，有时候需要重启才能看到效果



### 三、openjdk

### 1、解压到 /usr/local目录下



### 2、配置文件

```bash
# 例子
export JAVA_HOME=/usr/local/jdk-11
export PATH=$PATH:$JAVA_HOME/bin
```