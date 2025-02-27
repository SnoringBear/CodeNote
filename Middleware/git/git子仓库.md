1、当使用git clone 下来的工程中带有submodule时，初始的时候，submodule的内容并不会自动下载下来的，此时，只需执行如下命令：

```shell

git submodule update --init --recursive

```

2、所以子仓库一起操作

```shell
# 切换到master分支
git submodule foreach git checkout master

# 设置git参数
git submodule foreach git config core.autocrlf input
```
