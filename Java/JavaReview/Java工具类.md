# Java工具类

### 一、类扫描

```java
public static Set<Class<?>> getClasses(String pack, Predicate<Class<?>> filter) {
		Set<Class<?>> result = new LinkedHashSet<Class<?>>();
		// 是否循环迭代
		boolean recursive = true;
		// 获取包的名字 并进行替换
		String packageName = pack;
		String packageDirName = packageName.replace('.', '/');
		// 定义一个枚举的集合 并进行循环来处理这个目录下的things
		Enumeration<URL> dirs;
		try {
      // 如果 packageDirName为"/"  就class生成目录,即classes目录，classes目录下的内容与idea标记为
      // 代码的目录有关,一半src下面都是代码，标记src,maven项目有的Java下面才为代码，标记java
			dirs = Thread.currentThread().getContextClassLoader().getResources(packageDirName);
			// 循环迭代下去
			while (dirs.hasMoreElements()) {
				// 获取下一个元素
				URL url = dirs.nextElement();
				// 得到协议的名称
				String protocol = url.getProtocol();
				// 如果是以文件的形式保存在服务器上
				if ("file".equals(protocol)) {
					// 获取包的物理路径
					String filePath = URLDecoder.decode(url.getFile(), "UTF-8");
					// 以文件的方式扫描整个包下的文件 并添加到集合中
					findAndAddClassesInPackageByFile(packageName, filePath, recursive, result, filter);
				} else if ("jar".equals(protocol)) {
					// 如果是jar包文件
					Set<Class<?>> jarClasses = findClassFromJar(url, packageName, packageDirName, recursive, filter);
					result.addAll(jarClasses);
				}
			}
		} catch (IOException e) {
			logger.error("", e);
		}

		return result;
	}

```



```java
// 自定义类扫描

public static void main(String[] args) throws Exception {
        // 这个路径与标记有关,不同的标记classes下面的内容不一样
        Enumeration<URL> resources = Thread.currentThread().getContextClassLoader().getResources("./database");

        while (resources.hasMoreElements()) {
            URL url = resources.nextElement();
            // 得到协议的名称
            String protocol = url.getProtocol();
            System.out.println(protocol);
            // 获取包的物理路径
            String filePath = URLDecoder.decode(url.getFile(), "UTF-8");
            System.out.println(filePath);
            // 获取此包的目录 建立一个File
            File dir = new File(filePath);
            // 如果存在 就获取包下的所有文件 包括目录
            File[] dirfiles = dir.listFiles(new FileFilter() {
                // 自定义过滤规则 如果可以循环(包含子目录) 或则是以.class结尾的文件(编译好的java类文件)
                @Override
                public boolean accept(File file) {
                    return (file.getName().endsWith(".class"));
                }
            });
            System.out.println(dirfiles);
            for (File file : dirfiles) {
                // 如果是java类文件 去掉后面的.class 只留下类名
                String className = file.getName().substring(0, file.getName().length() - 6);
                // 路径从classes开始
                Class<?> clazz = Thread.currentThread().getContextClassLoader()
                        .loadClass("database" + '.' + className);
                System.out.println(clazz.getSimpleName());
            }
        }
    }
```

