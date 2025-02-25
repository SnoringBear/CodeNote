# 1 、倘若没有maven

新建了一个项目，然后项目中写了一些代码

需求：需要大家写一段jdbc代码，去读取某张表中的数据

一定少不了的东西是要mysql的驱动包

需求：请你用spring去开发一个简单的crud

有一种困惑？

很烦，当你在做不同的项目的时候，你都需要导入一些基本的重复的jar包

Spring 基本开发的时候，需要的jar包是
## Maven可以做的事情

### jar包管理

Com.gupao.dao

Com.gupao.service

Com.gupao.web

Com.gupao.util

contextConfiguration.xml

Struts-config.xml

Hibernate-cfg.xml

Properties.xml

### 项目的构建和管理

# **2 maven介绍**
![Untitled.png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947366101-b32b2bbb-f488-426f-a3de-5ed01a780f41.png#averageHue=%23f8f8f8&clientId=u503d9aab-4940-4&from=paste&height=443&id=u59f9a393&originHeight=443&originWidth=878&originalType=binary&ratio=1&rotation=0&showTitle=false&size=47393&status=done&style=none&taskId=u28d3ece6-8284-4a38-9190-cb776257da1&title=&width=878)

# 3 、maven初体验
正常使用maven有两种形式，一种是命令行形式（手动创建maven项目），另外一种是集成开发工具创建maven项目。
因为手动创建过程比较麻烦一点，我担心大家听的时候会觉得枯燥。
带大家使用一下maven的感觉。Maven怎么去进行jar包管理的，或者是怎样去进行项目的构建和管理的。
MyEclipse    Idea
Cmd    javac    java
# 4、 maven官网
大家在学习一门新的技术的时候，会不会想找最官网最权威的答案？
只有学会看官网，你才能得到最完善最牛逼的答案。
maven官网：[http://maven.apache.org/](http://maven.apache.org/)
其实英文文档并没有那么难，只要你多看多理解，也就是那么回事，而且在英文官网文档中你可以学习到最权威的知识。
# 5、 maven下载与安装
jar包管理，以及项目构建与管理的。
用一用maven，怎么用？

- 下载 download

System Requirements

![Untitled (1).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947397178-7615d182-e411-46cb-b2cc-20e6dab97016.png#averageHue=%23fbf9f9&clientId=u503d9aab-4940-4&from=paste&height=199&id=u3992ce4c&originHeight=199&originWidth=767&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18381&status=done&style=none&taskId=udc994bc8-ed2c-4c85-8677-62c8e70f236&title=&width=767)

![Untitled (2).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947412114-9271888e-c7e2-499d-a72d-8af0b4435271.png#averageHue=%23fbf8f5&clientId=u503d9aab-4940-4&from=paste&height=213&id=uda0fb8c6&originHeight=213&originWidth=319&originalType=binary&ratio=1&rotation=0&showTitle=false&size=13329&status=done&style=none&taskId=uc5a1c3fa-ac56-4b00-aa45-1471aa7515f&title=&width=319)

- 安装

注意：**在maven安装之前，一定要已经安装了jdk。**

只需要将maven所在的目录的bin文件夹配置到Path环境变量中，这样做的目的是在命令行的任意目录下，都可以使用“mvn”命令来操作。

MAVEN_HOME类比JAVA_HOME     把maven所在的目录配置到这个MAVEN_HOME中

# 6 maven配置

查看maven的版本信息：mvn -version

那maven我到底该怎么用？如何去用？如何进行jar包项目的管理等等？

mvn [options] [<goal(s)>] [<phase(s)>]

mvn命令有一个更好的理解，就是不要觉得太突兀  mvn -v   mvn package   javac Person.java   java Person

maven的安装目录的conf文件夹中有一个settings.xml文件，而官网中又说在.m2文件夹下也有这样一个文件，但是我们没有发现，原因是？

将conf中的settings.xml文件复制到.m2文件夹下之后，会不会有冲突？也就是说，当两边都存在settings.xml文件的时候，我们以哪个为准？

# **7、 maven目录结构**
![Untitled (3).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690947489126-81c32e52-7256-4865-9515-5d6f4a4d6fb9.png#averageHue=%23fcfaf8&clientId=u503d9aab-4940-4&from=paste&height=193&id=u267b3e68&originHeight=193&originWidth=459&originalType=binary&ratio=1&rotation=0&showTitle=false&size=15219&status=done&style=none&taskId=u7c3f28a1-2b68-400f-934a-5a723bdf5cd&title=&width=459)

bin目录:包含了maven运行的一些命令脚本
boot目录：包含的是类加载器的一些东西
conf目录：configuration配置的，保存一些配置文件的
lib目录：保存jar包的

原因是希望大家对maven目录结构有一个整体的认知，至少知道maven各个文件夹是干什么的，这样接下来学习maven的时候你才会不慌

# 8 maven项目目录结构

项目目录结构，实际上指的是maven要求你的项目必须的一个目录层次。

约定优于配置  (Convention Over Configuration)

要去使用maven帮你进行jar包的管理，以及项目的构建和管理等等，你就要遵循maven的规定/约定

maven要求的你的工程结构必须是哪样哪样的。

MyEclipse/IDEA  中想怎么建就怎么建，这边放这个，这边放那个。

有了maven之后，你就不能那么随心所欲的。
![Untitled (4).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690948632245-14d98780-5e92-478e-b499-e1938c345710.png#averageHue=%23f9f9f9&clientId=u503d9aab-4940-4&from=paste&height=462&id=ub2200f48&originHeight=462&originWidth=878&originalType=binary&ratio=1&rotation=0&showTitle=false&size=43565&status=done&style=none&taskId=ube88037e-afd6-48bc-a5bf-c973f5edd61&title=&width=878)

# 9 、手动创建maven项目
按照之前的图解，我们手动创建了maven项目，那其实target目录还是没有的，怎么呢？
需求：我想要在命令行中打印出一句话，比如叫做hello gupao maven！
**传统的方式**
![Untitled (5).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690948669688-e595a562-c524-4ff2-8992-76a0785687f2.png#averageHue=%23121110&clientId=u503d9aab-4940-4&from=paste&height=513&id=u68ecd98e&originHeight=513&originWidth=735&originalType=binary&ratio=1&rotation=0&showTitle=false&size=51517&status=done&style=none&taskId=u4813289b-d4f3-410b-b8f8-6aa8fa03975&title=&width=735)

**Maven的做法**

- 使用命令行进入到maven的工程中“GupaoMaven”

mvn compile  对maven项目进行编译

![Untitled (6).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690948701777-e8f10031-5a4b-4cb8-908f-016926c3b917.png#clientId=u503d9aab-4940-4&from=paste&height=468&id=ub825cb5f&originHeight=468&originWidth=603&originalType=binary&ratio=1&rotation=0&showTitle=false&size=295984&status=done&style=none&taskId=u1c933fcb-783a-462c-81cf-f8b7e1ff7e5&title=&width=603)

- target目录是项目输出的一个目录，这个输出现在应该多少有点感觉了

# 10 开发工具创建maven项目

其实无论是myeclipse还是idea里面都集成了maven，默认安装了maven

maven默认帮你写好了一些项目的目录结构

创建一个简单的maven项目必备的内容

com.gupao.maven

# 11 maven配置文件之settings.xml

该文件是maven的全局配置文件  -web项目中的web.xml

（1）mirrors标签，表示配置镜像所在的路径的

默认的地址是国外的：

[https://repo.maven.apache...，maven要帮你管理jar，这些jar包下载的地址是从该地址下的，当然这个下载地址可以改变，就是通过mirrors标签。](https://repo.maven.apache...xn--%2Cmavenjar%2Cjar%2C%2Cmirrors-lz68aab91fe9g6vbgt18iv22be2fwva289fbae99ccf8171cr9qe6kl27d52iqa338ssu2h4g2arszha0116d1pbx469a180atk5bpa143b52aua620b./)

```go
1.	<mirror>  
2.	      <id>alimaven</id>  
3.	      <name>aliyun maven</name>  
4.	      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>  
5.	      <mirrorOf>central</mirrorOf>          
6.	</mirror>
```

将这段话配置到mirrors标签对中即可。

![Untitled (7).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690948740347-4b7200e2-e173-43ca-b7ee-62874eca4f95.png#averageHue=%231d1a17&clientId=u503d9aab-4940-4&from=paste&height=505&id=u0abdd7ba&originHeight=505&originWidth=818&originalType=binary&ratio=1&rotation=0&showTitle=false&size=132850&status=done&style=none&taskId=u75bb6ccd-2d8a-48f1-9b75-940499f3086&title=&width=818)

一方面国外镜像的地址下载速度比较慢，所以可以考虑国内镜像的地址，比如阿里云的。

- localRepository
- interactiveMode 标识maven是否需要和用户交互以获得输入

如果maven需要和用户交互以获得输入，那么则设置成true，不然则设置为false

- offline

标识maven是否需要在离线模式下运行

- pluginGroups

当插件的组织id(groupId)没有显示提供时，供搜寻插件组织Id的列表

- proxies

用来配置不同的代理的

（7）servers

仓库的下载和部署是在pom.xml文件中进行定义的。比如说用户名，密码需要访问远程仓库的时候，有时候需要安全认证，这个认证就可以配置在servers标签中

（8）profiles

根据环境参数来调整构建配置的列表

# 12 maven仓库

maven把jar包自动下载下来之后，这个保存jar包的目录是由我们自己指定，你自己可以指定这个目录存储的位置，当然，它也有一个默认的位置。

补充：在maven构建项目的时候，它会帮我们把项目中所需要的一些基础的jar包下载下来，这个下载的地址就是由settings.xml文件里面的mirror标签所指定的。

maven仓库，实际上指的就是jar包下载下来之后保存的目录。

maven仓库保存在自己电脑中的叫做本地仓库，localRepository的含义本地仓库。

E://temp//myrepo   自己指定了一个本地maven仓库所在的地址，而不是之前默认的当前用户的.m2下面的repository文件夹了。

剧透：如果一个jar包已经在本地仓库中有了，注意这个jar包版本一定是要一致的才算有了，如果版本不一致，不能算作有，比如3.8.1   3.8.2

其他如果有了，那么就不会再次去重复下载。

优势：可以节约本地的一个空间存储。

# 13 maven配置文件之pom.xml

pom   project object model   项目对象模型，它为唯一标识该项目的

（1）3个必填字段

由groupId   artifactId   version所组成的就可以唯一确定一个项目

groupId标识的项目组（填写公司的域名）  组织

artifactId项目名称

version版本号

上面的三个标签是必填标签

（2）pom里面还有会dependencies标签，这个标签就是用来配置我们项目中具体需要哪些jar包

（3）properties标签，用来定义pom中的一些属性的

（4）build    指定如何构建当前项目的

source:    指定了当前构建的source目录

plugin:    指定了进行构建时使用的插件

- packging 指定当前构建项目的类型，war jar pom

补充：pom文件是可以继承的，超级pom文件等等。

比如一定pom文件中定义了一些东西，另外一个pom文件也想要使用，这时候就可以使用继承的方式。

# 14 maven依赖

dependencies标签，里面可以包含很多的dependency标签

[http://mvnrepository.com/](http://mvnrepository.com/)    maven项目中要想下载指定的jar包，实际上就是在pom.xml文件的dependencies标签对中进行配置，那这个dependency怎么写？具体就是在这个网址中进行搜索。
org.mybatis

mybatis

3.4.5
比如上面这段配置的确可以下载具体的jar包，怎么去验证它会下载下来呢？

不仅可以把当前指定的jar包下载下来，而且它所依赖的其他包也会自动下载下来。

maven比如已经下载过的jar包，还会再下载吗？

如果已经下载过的jar包，maven不会帮我们再次下载（前提是maven仓库是同一个目录，如果换了目录，它还是会重新下载的），而且对于不同的版本的同一个jar包，maven默认它是不同的jar包。

# 15 maven插件

什么是maven插件？

maven实际上是一个依赖插件执行的框架，每个人物都是由插件完成的。

两种插件类型：

Build plugins    在构建时执行，需要在pom.xml文件中进行配置
reporting plugins    在网站生成过程中执行，也需要在pom.xml文件中进行配置
常用的插件列表

clean    项目构建之后用于清理项目的

compiler    编译java源代码的

jar      构建jar文件的

war     构建war文件的

tomcat   ....

![Untitled (8).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690948781910-cc40bc62-2d36-418d-9503-386169e5aedd.png#averageHue=%23fcfbfb&clientId=u503d9aab-4940-4&from=paste&height=572&id=u44aeb481&originHeight=572&originWidth=647&originalType=binary&ratio=1&rotation=0&showTitle=false&size=114372&status=done&style=none&taskId=u8fc0b50b-8f8d-464e-b7b5-9d072734ae1&title=&width=647)

# 16 maven之archetype

maven本身来说，它会帮我们已经定义好一些archetype，这些archetype是我们开发中常用的一些项目模板，你新建maven项目的时候，只需要选择对应的archetype即可。

![Untitled (9).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690948807028-d8d7aa33-05b2-4930-9a4e-feb051997665.png#averageHue=%23f3f3f2&clientId=u503d9aab-4940-4&from=paste&height=602&id=ubbd58039&originHeight=602&originWidth=734&originalType=binary&ratio=1&rotation=0&showTitle=false&size=145679&status=done&style=none&taskId=u7bd65927-7d40-4951-81ec-4f168c30ce1&title=&width=734)
 但是往往是官方提供的东西虽然好，不是太符合个人定制化的需要。 大家自己可能有一套自己觉得比较满意的archetype，所以往往我们会自定义自己的archetype。  
![Untitled (10).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690948841270-ecb22d6f-e1c0-4e7e-bbf7-b9c93ae150c1.png#averageHue=%23f8f6f4&clientId=u503d9aab-4940-4&from=paste&height=402&id=u1ce9c47c&originHeight=402&originWidth=879&originalType=binary&ratio=1&rotation=0&showTitle=false&size=233710&status=done&style=none&taskId=u6fe780df-ce81-4ded-8d37-e0ffde0ef04&title=&width=879)

- 在xml文件中定义一个插件
- 在xml文件所在的目录使用mvn archetype:generate
- 在target目录中会生成一个自定义的骨架

![Untitled (11).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690948863066-36f0d53c-d108-4ccf-a873-9f98108a8c56.png#averageHue=%23fefdfd&clientId=u503d9aab-4940-4&from=paste&height=149&id=u60258ef8&originHeight=149&originWidth=470&originalType=binary&ratio=1&rotation=0&showTitle=false&size=6217&status=done&style=none&taskId=ud75b98f4-3314-4435-bd29-878f6b33a2a&title=&width=470)

- 在本地安装一下这个archetype

mvn install

就把刚才的archetype安装到了本地。

# 17 搭建maven私服

如果使用的是国外镜像，那么下载速度比较慢

如果使用的是阿里云镜像，速度还算ok，但是假如网速不好的时候，其实也是比较慢的

如果没有网的情况下更加下载不了

团队开发的时候，是不是每个人在自己的本地仓库都需要下载一份呢？

能不能大家共用一份呢？

当我们某个maven项目需要一份jar包的时候，mysql-connector.jar

先看一下你本地仓库是否有这样jar包（版本也要判断），如果有，则不下载

如果没有，则去下载，并且保存到本地仓库中，然后以后需要的话，就不需要下载了

在项目中进行开发的时候，仅仅就是将本地仓库的jar包导入到工程里面即可，再也不用

像传统原始方式那样，每个项目中都要一份单独的jar包，这样会造成jar包的冗余。

![Untitled (12).png](https://cdn.nlark.com/yuque/0/2023/png/535904/1690948889400-7b76912b-2737-466d-875c-0a802a3681c9.png#averageHue=%23faf9f9&clientId=u503d9aab-4940-4&from=paste&height=362&id=u4b3cee59&originHeight=362&originWidth=878&originalType=binary&ratio=1&rotation=0&showTitle=false&size=34205&status=done&style=none&taskId=u2edd4ca1-618c-46e3-896e-cb4c5fd5e87&title=&width=878)

写好一个web资源（HTML页面）想要供外界去访问，你会在服务器上装一个web服务器比如tomcat服务器

# 18 maven项目实战

如果说你使用的maven自定义的archetype发现里面的工程目录结构并不是严格按照maven所要求的，这时候你需要把缺少的目录补全

需求：使用maven项目搭建一个springmvc的简单流程

- 新建一个maven工程
- 根据你需要创建的项目类型，导入你需要的jar包

根据dependency标签去导入jar包，并不是太难的一个过程，很方便

- xml文件
- springmvc-serlvet.xml

师傅领进门，修行靠个人
