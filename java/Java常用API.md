##  一、Class  
```java
isAssignableFrom()方法与instanceof关键字的区别总结为以下两个点：

1、isAssignableFrom()方法是从类继承的角度去判断，instanceof关键字是从实例继承的角度去判断。

2、isAssignableFrom()方法是判断是否为某个类的父类，instanceof关键字是判断是否某个类的子类。
```

## 二、URLDecoder.decode()
```java
// url中的特殊字符有这么多.在前后端传输过程中,可能会对这些字符进行特殊处理,导致后台进行使用原字符串时,出现异常
```

##  三、Arrays._fill()_



##  四、**Unable to make protected final java.lang.Class java.lang.ClassLoader.defineClass**

```java
Exception in thread "main" java.lang.ExceptionInInitializerError
	at org.logicalcobwebs.cglib.core.KeyFactory$Generator.generateClass(KeyFactory.java:167)
	at org.logicalcobwebs.cglib.core.DefaultGeneratorStrategy.generate(DefaultGeneratorStrategy.java:25)
	at org.logicalcobwebs.cglib.core.AbstractClassGenerator.create(AbstractClassGenerator.java:216)
	at org.logicalcobwebs.cglib.core.KeyFactory$Generator.create(KeyFactory.java:145)
	at org.logicalcobwebs.cglib.core.KeyFactory.create(KeyFactory.java:117)
	at org.logicalcobwebs.cglib.core.KeyFactory.create(KeyFactory.java:108)
	at org.logicalcobwebs.cglib.core.KeyFactory.create(KeyFactory.java:104)
	at org.logicalcobwebs.cglib.proxy.Enhancer.<clinit>(Enhancer.java:69)
	at org.logicalcobwebs.proxool.ProxyFactory.getProxy(ProxyFactory.java:111)
	at org.logicalcobwebs.proxool.ProxyFactory.getWrappedConnection(ProxyFactory.java:85)
	at org.logicalcobwebs.proxool.ConnectionPool.getConnection(ConnectionPool.java:261)
	at org.logicalcobwebs.proxool.ProxoolDriver.connect(ProxoolDriver.java:89)
	at java.sql/java.sql.DriverManager.getConnection(DriverManager.java:683)
	at java.sql/java.sql.DriverManager.getConnection(DriverManager.java:253)
	at com.bycw.orm.utils.DbHelper.getConnection(DbHelper.java:235)
	at com.bycw.game.GameServer.frameworkInit(GameServer.java:92)
	at com.bycw.game.GameServer.start(GameServer.java:51)
	at com.bycw.game.ServerStartup.main(ServerStartup.java:26)
Caused by: java.lang.reflect.InaccessibleObjectException: Unable to make protected final java.lang.Class java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain) throws java.lang.ClassFormatError accessible: module java.base does not "opens java.lang" to unnamed module @3d24753a
	at java.base/java.lang.reflect.AccessibleObject.checkCanSetAccessible(AccessibleObject.java:354)
	at java.base/java.lang.reflect.AccessibleObject.checkCanSetAccessible(AccessibleObject.java:297)
	at java.base/java.lang.reflect.Method.checkCanSetAccessible(Method.java:200)
	at java.base/java.lang.reflect.Method.setAccessible(Method.java:194)
	at org.logicalcobwebs.cglib.core.ReflectUtils$2.run(ReflectUtils.java:56)
	at java.base/java.security.AccessController.doPrivileged(AccessController.java:318)
	at org.logicalcobwebs.cglib.core.ReflectUtils.<clinit>(ReflectUtils.java:46)
	... 18 more

// 这是由于 JDK 8 中有关反射相关的功能自从 JDK 9 开始就已经被限制了。
// 为了兼容原先的版本，需要在运行项目时添加 --add-opens java.base/java.lang=ALL-UNNAMED 选项来开启这种默认不被允许的行为。
```
