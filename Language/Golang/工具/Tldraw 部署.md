## Tldraw 部署

Tldraw 是一款开源的矢量绘图工具，可以用于绘制流程图、图表、示意图等。它可以部署在本地服务器或云服务器上，以便多人协作使用。

**部署 Tldraw 的步骤如下：**

**1. 安装依赖项:**

Tldraw 需要 Node.js 和 Yarn 才能运行。

```
# 安装 Node.js
nvm install node

# 安装 Yarn
npm install --global yarn
```

**2. 克隆 Tldraw 代码仓库:**

```
git clone https://github.com/tldraw/tldraw.git
```

**3. 进入 Tldraw 代码目录:**

```
cd tldraw
```

**4. 安装 Tldraw:**

```
yarn install
```

**5. 启动 Tldraw:**

```
yarn start
```

**Tldraw 将在默认端口 5420 上启动。**

**您可以通过以下方式访问 Tldraw:**

- 在浏览器中打开 `http://localhost:5420`。
- 使用 `tldraw` 命令行工具。

**部署 Tldraw 到云服务器:**

**1. 选择一个云服务器提供商，例如阿里云、腾讯云、亚马逊云等。**

**2. 创建一个云服务器实例。**

**3. 将 Tldraw 代码部署到云服务器上。**

**4. 配置 Tldraw 的启动命令。**

**5. 启动 Tldraw。**

**Tldraw 将在云服务器上启动。**

**您可以通过以下方式访问 Tldraw:**

- 在浏览器中打开 `http://<云服务器公网IP>:5420`。
- 使用 `ssh` 工具连接到云服务器，然后使用 `tldraw` 命令行工具。

**以下是一些具体的示例:**

**部署 Tldraw 到本地服务器:**

```
# 安装 Node.js
nvm install node

# 安装 Yarn
npm install --global yarn

# 克隆 Tldraw 代码仓库
git clone https://github.com/tldraw/tldraw.git

# 进入 Tldraw 代码目录
cd tldraw

# 安装 Tldraw
yarn install

# 启动 Tldraw
yarn start
```

**Tldraw 将在浏览器中打开。**

**部署 Tldraw 到阿里云服务器:**

1. 登录阿里云官网，选择 **云服务器** 产品。
2. 创建一个云服务器实例，选择 **Ubuntu 20.04** 操作系统。
3. 将 Tldraw 代码部署到云服务器上。
4. 使用 `vim` 编辑器打开 `/etc/systemd/system/tldraw.service` 文件，添加以下内容：

```
[Unit]
Description=Tldraw

[Service]
Type=simple
ExecStart=/home/user/tldraw/bin/tldraw
Restart=always

[Install]
WantedBy=multi-user.target
```

1. 运行以下命令启动 Tldraw:

```shell
sudo systemctl start tldraw
```

**Tldraw 将在云服务器上启动。**

**您可以通过浏览器访问 Tldraw:**

```shell
http://<云服务器公网IP>:5420
```