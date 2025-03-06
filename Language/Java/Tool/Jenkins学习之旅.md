# 1 Jenkins简介

Jenkins

开源

Java开发的   提到是**Java开发的**，一定联想云心Jenkins  jdk环境

持续集成工具，用于监控持续重复的工作

假设没有Jenkins的话会是怎样？

《Git学习之旅》《Maven学习之旅》

平时去开发一个项目

每当修改了功能或者完善了某个功能之后，想要测试一下，就是手动去部署我们的项目。

接触过Maven，Build，war包的，**手动**去build项目，以及**手动**部署项目

Low   手动的过程比较麻烦

发生了一些错误的信息，这时候你通常会去手动查看日志信息。

完善了某个功能，我们只需要进行一步手动操作，就有这样一个工具来帮我们自动去做那些事情，甚至还可以做更多的事情。

人类偷懒

有一个官网的介绍，学习一门新的技术，如果不去看官网。

# 2 Jenkins官网

[https://jenkins.io/](https://jenkins.io/)

![Untitled.png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946691438-e12cf504-415c-4553-87d6-50ac2eb4647d.png#averageHue=%23e1e3e6&clientId=uad0ef327-28ce-4&from=paste&height=1041&id=uab6a163c&originHeight=1041&originWidth=2000&originalType=binary&ratio=1&rotation=0&showTitle=false&size=838683&status=done&style=none&taskId=ue28750a4-ae89-4fda-9e3e-9bd1830ab07&title=&width=2000)
![Untitled (1).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946707987-cf6692c1-2a78-4f14-8adf-422bb9b5cb0d.png#averageHue=%23fcfaf9&clientId=uad0ef327-28ce-4&from=paste&height=401&id=u6fd039f8&originHeight=401&originWidth=2000&originalType=binary&ratio=1&rotation=0&showTitle=false&size=289944&status=done&style=none&taskId=ua1377069-ad64-4b6d-a357-ae5eb8c641f&title=&width=2000)
![Untitled (2).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946720191-10fd82ad-e448-4098-a511-140d7b3fe075.png#averageHue=%23fdfbf9&clientId=uad0ef327-28ce-4&from=paste&height=205&id=uf1652d36&originHeight=205&originWidth=567&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23249&status=done&style=none&taskId=u396ed568-6cee-44b8-b156-a36a0309d1d&title=&width=567)
# **3 Jenkins的下载与安装**
![Untitled (3).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946767921-1fac304e-adb0-489f-973c-5a72e156ed8c.png#averageHue=%23fafafa&clientId=uad0ef327-28ce-4&from=paste&height=210&id=u7b560621&originHeight=210&originWidth=357&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9693&status=done&style=none&taskId=u0d3227db-6505-4575-8f81-ce7c3e5e51e&title=&width=357)
 Windows 平台中，直接双击安装的，比较方便 Jenkins.war web项目 java -jar 命令去执行它/war包直接放到tomcat的web-app下面，启动tomcat 【 必须有java环境，而且这个版本一定要是jdk1.8 】 命令的方式去运行jenkins java -jar jenkins.war --httpPort=8080  
![Untitled (4).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946787533-03ce81ca-5018-491e-a138-c89fedf7e1c1.png#averageHue=%23efefee&clientId=uad0ef327-28ce-4&from=paste&height=410&id=ue17c6ea1&originHeight=410&originWidth=878&originalType=binary&ratio=1&rotation=0&showTitle=false&size=115013&status=done&style=none&taskId=uc2a0c1da-848f-472f-8be6-49dc3dc551e&title=&width=878)

 .war扔到tomcat中的情况  

![Untitled (5).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946806275-cc530b62-f33e-4ce5-be73-ab9ab41d05d7.png#averageHue=%23cdb6b6&clientId=uad0ef327-28ce-4&from=paste&height=389&id=u9f3c0a2d&originHeight=389&originWidth=499&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18319&status=done&style=none&taskId=u6e68291d-ebc5-4576-9c9a-01cf107a4ab&title=&width=499)

![Untitled (6).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946822917-5e5e3fcd-3f03-4791-92b9-7c8b5995e041.png#averageHue=%23f6f2f0&clientId=uad0ef327-28ce-4&from=paste&height=282&id=ue6d39c60&originHeight=282&originWidth=631&originalType=binary&ratio=1&rotation=0&showTitle=false&size=38314&status=done&style=none&taskId=udb1f580c-35de-4e16-9922-c27709bc433&title=&width=631)

# 4 登录和界面功能介绍
大家平时一般如果在Linux环境中进行安装的话，步骤也是类似
Java环境
这里就是Jenkins帮你去新建一个对应的项目，这个项目就是关联你需要自动build，deploy

![Untitled (7).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946848143-c311cad6-ceb0-4690-a9c1-d84799ec7ff7.png#averageHue=%23f0efef&clientId=uad0ef327-28ce-4&from=paste&height=443&id=ue598ef52&originHeight=443&originWidth=840&originalType=binary&ratio=1&rotation=0&showTitle=false&size=51504&status=done&style=none&taskId=uee87debc-ab33-476f-bf2e-c3ab05bbc18&title=&width=840)

Git   Maven不知道的话，可以去腾讯课堂上搜索“咕泡学院”，Git学习之旅    Maven学习之旅

具体的内容在后面的视频中还会给大家讲解

# 5 Jenkins的工作流程

如何帮我们自动完成这些工作的呢？----》Jenkins工作流程

Jenkins中应该有一个类似于“上传”按钮的操作，如果我想要把代码上传到Jenkins中让它帮我来进行自动的build和deploy的操作。

代码    合作开发代码的话，这些代码在每个人的机器上可能是不一样的

代码-----》github/码云/gitlab       github拥有的就是最新的代码

![Untitled (8).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946872295-7ad21603-2fb6-44e4-b4d5-90fbd1857970.png#averageHue=%23f7f6f6&clientId=uad0ef327-28ce-4&from=paste&height=293&id=uf5e5c316&originHeight=293&originWidth=878&originalType=binary&ratio=1&rotation=0&showTitle=false&size=35660&status=done&style=none&taskId=ue542b661-a643-4f7a-acda-002d794c97d&title=&width=878)

# **6 创建任务和功能介绍**
![Untitled (9).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946919193-14588358-fe9e-493e-92d8-9b4372427728.png#averageHue=%23eeeeed&clientId=uad0ef327-28ce-4&from=paste&height=435&id=u3d659769&originHeight=435&originWidth=877&originalType=binary&ratio=1&rotation=0&showTitle=false&size=68030&status=done&style=none&taskId=u60a6dcd9-3d23-4a8b-add6-00175606089&title=&width=877)

# 7 拉取代码

让该任务对应到某个远程上面的代码，github，码云，gitlab

在git界面填写github项目的地址

[git@github.com](mailto:git@github.com):itcrazy2016/gupaogit.git

**Failed to connect to repository : Command "git.exe ls-remote -h **[**git@github.com**](mailto:git@github.com)**:itcrazy2016/gupaogit.git HEAD" returned status code 128: stdout:  stderr: ssh: Could not resolve hostname github.com: Name or service not known  fatal: Could not read from remote repository.  Please make sure you have the correct access rights and the repository exists.**

![Untitled (10).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946945837-560d54e8-745f-4206-82f7-5fc2c2300466.png#averageHue=%23e4d8c7&clientId=uad0ef327-28ce-4&from=paste&height=293&id=u2bf116b7&originHeight=293&originWidth=879&originalType=binary&ratio=1&rotation=0&showTitle=false&size=123083&status=done&style=none&taskId=ue114ebe3-0b8f-4d8e-ba7d-5614c91d883&title=&width=879)

# 8 莫慌之思路再梳理

这时候虽然也能跟得上，总觉得缺少了点什么，老师能不能再帮我好好梳理一下。

下载好了Jenkins之后，安装，访问8080端口，管理的账号，密码自己去Jenkins的安装目录寻找

默认会安装一些插件，git，maven，github。如果没有安装成功，你可以进行插件的选择自定义安装。

想要通过git拉去github上面的代码，本地安装了git

git clone [git@github.com:itcrazy2016/gupaogit.git:](mailto:git@github.com:itcrazy2016/gupaogit.git:)

使用Jenkins拉取代码，本地的git需要配置成功。

ssh  key   public key

![Untitled (11).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946973123-d6e2dec9-12bd-45d9-8cdf-27ac01c0ccee.png#averageHue=%23fcfcfb&clientId=uad0ef327-28ce-4&from=paste&height=124&id=u70c418b7&originHeight=124&originWidth=909&originalType=binary&ratio=1&rotation=0&showTitle=false&size=19675&status=done&style=none&taskId=u7b35517f-a66d-401c-9f8e-9d27255157b&title=&width=909)

 拉取代码  

![Untitled (12).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690946991650-cb51e9b3-bbfb-4c9d-a46e-eb3bf31c1b94.png#averageHue=%23ece7e6&clientId=uad0ef327-28ce-4&from=paste&height=145&id=ucc28375f&originHeight=145&originWidth=878&originalType=binary&ratio=1&rotation=0&showTitle=false&size=65355&status=done&style=none&taskId=u162fd850-4cc0-47ab-8beb-af10914d373&title=&width=878)

没有拉取成功

- git的插件和git的全局管理工具是否已经配置【本地git环境有没有搭建成功】
- 安全性的考虑，配置一个Credential 证书

![Untitled (13).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947013689-30368df8-d2e0-4c24-9713-9e7a273a2089.png#averageHue=%23f1f1f1&clientId=uad0ef327-28ce-4&from=paste&height=214&id=u29bd1fc3&originHeight=214&originWidth=887&originalType=binary&ratio=1&rotation=0&showTitle=false&size=26654&status=done&style=none&taskId=u9857e8e3-631e-472f-a259-4f8678716c5&title=&width=887)

 没有报错  

# 9 项目构建和发布(上)

- 新建一个maven的web工程
- 代码Push到了github上
- Jenkins对其进行集成发布

![Untitled (13).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947059381-f77f9005-7669-4a4f-b203-350fda020d7f.png#averageHue=%23f1f1f1&clientId=uad0ef327-28ce-4&from=paste&height=214&id=u74bec03d&originHeight=214&originWidth=887&originalType=binary&ratio=1&rotation=0&showTitle=false&size=26654&status=done&style=none&taskId=u887e67c0-632d-4283-aaf8-34b8902499b&title=&width=887)

![Untitled (15).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947081372-d67d0e39-3dac-4f67-822d-f984019d7654.png#averageHue=%23efefee&clientId=uad0ef327-28ce-4&from=paste&height=99&id=u78bc9179&originHeight=99&originWidth=878&originalType=binary&ratio=1&rotation=0&showTitle=false&size=14233&status=done&style=none&taskId=ufea2c18f-4794-40de-b3fe-71fcaee6317&title=&width=878)

这里可以指定针对不同的分支进行集成发布操作

- Maven对其进行了build操作（pre-step，post-step）
- 项目进行一个发布tomcat容器

1. 有一个tomcat容器 本机 远程机器 url
2. 有需要发布项目的war maven----->xx.war target---->xx.war

![Untitled (16).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947109487-8b42fc98-aa5a-4072-b923-4b9bc9962576.png#averageHue=%23f9f8f7&clientId=uad0ef327-28ce-4&from=paste&height=212&id=u9d55c5b9&originHeight=212&originWidth=560&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18673&status=done&style=none&taskId=u07e37bec-e635-4987-8e7b-5ad91be24f6&title=&width=560)

1. 把前面的拉取代码和maven构建操作先执行一下 jenkins

# 10 项目构建和发布(下)

gpjenkins放到了github

Jenkins---->

- 代码从github上拉取下来
- 项目进行build maven

![Untitled (17).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947134891-80947084-4f81-4cac-8c92-1b49cfdbe09f.png#averageHue=%23fcfaf9&clientId=uad0ef327-28ce-4&from=paste&height=64&id=u789c18c8&originHeight=64&originWidth=260&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4526&status=done&style=none&taskId=ub6442db4-23f3-428b-93d9-f218525acc3&title=&width=260)

- 需要把这样一个war发布到tomcat上去
- 看图说话

![Untitled (18).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947158412-e2f30207-fc05-4fc5-aebf-e95340120fe6.png#averageHue=%23f1f1f0&clientId=uad0ef327-28ce-4&from=paste&height=418&id=u35b68ea9&originHeight=418&originWidth=879&originalType=binary&ratio=1&rotation=0&showTitle=false&size=67984&status=done&style=none&taskId=u2eea424f-c1e0-426b-a859-ef5709e8473&title=&width=879)

本地   127.0.0.1:8088    tomcat   但是出于安全性的一个考虑

进行认证   sshkey

1. 在tomcat中进行账号的管理
2. 需要告诉jenkins你的一个tomcat的账号

持续交付与发布

- 拉取代码
- Maven build
- Deploy 容器

需要手动在jenkins中点击立即构建的按钮

自动

# 11 Webhook的配置

本地：gpjenkins

Github：gpjenkins

本地进行开发，add  commit  push------>

本地的代码push----github上面

Jenkins----->立即构建（手动）

自动

Push------>触发jenkins的立即构建的操作

也就是github上面一旦收到push代码的请求----->触发立即构建的操作

中间桥梁

Jenkins项目中有一个地址http://localhost:8080/project/gpjenkins

能不能再github上的gpjenkins项目的某个位置，配置上这个url，一旦有push操作的时候，我就希望它能够对应之前jenkins上的url进行触发立即构建

外网和内网需要统一

Test
