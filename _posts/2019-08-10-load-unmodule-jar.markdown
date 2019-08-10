---
layout:     post
title:      "装载非模块化的包文件"
subtitle:   "在java11中使用legacy的包"
date:       2019-08-10
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - java 11
    - module
    - automatic module
---

正在写一个装载并显示obj模型的例子，在网上找了半天，只找到一个叫“JavaFX™ 3D Model Importers”的库，“[InteractiveMesh.org](http://www.interactivemesh.org/index.html)”出品。点进去一看，只支持java 8。当时就觉得有点不妙，搞不好就会碰到无法访问的问题。不管了，先倒入再说，相信甲骨文会把这个兼容问题搞定的。

我用的是maven，自然得用maven导入，写了下面的脚本做这事：

```
mvn install:install-file -Dfile=jimObjModelImporterJFX.jar \
                         -DgroupId=com.interactivemesh.jfx.importer \
                         -DartifactId='objmodel-importer' \
                         -Dversion=0.8 \
                         -Dpackaging=jar
```

导入后，在项目pom文件中加入下面的引用：

```
<dependency>
    <groupId>com.interactivemesh.jfx.importer</groupId>
    <artifactId>objmodel-importer</artifactId>
    <version>0.8</version>
</dependency>
```

注意，里面的groupId和artifactId要一一对应。

重新编译，果然，尽管这个包已经成功导入，但是eclipse还是报出`not accessible`的告警。这个是个典型的和module有关问题，意思是相关的module没有导入，无法访问。问题是这个包不是模块化的包，哪来的module可以导入啊。

继续谷歌大法。翻来翻去，发现网上关于这个问题的讨论很少。看来不是个热点问题。既然不是个热点问题，那么就说明应该不是什么大问题，甲骨文的java11应该默认就兼容这种老旧的包文件。

继续翻，找到个叫做“Automatic Module“的东西。这东西说java 11会为每一个非模块化的包生成一个默认模块。这个默认模块很特殊，它会exports它包含的所有的包，同时requires所有在module path中的模块。而它的名字就是这个jar包文件的名字。

刚刚我们导入的jar包叫`jimObjModelImporterJFX.jar`，那么就应该有个叫做`jimObjModelImporterJFX`的模块，不过在module path中却没有找到。这中间肯定出了什么岔子。

忽然想到这个jar包并非直接导入，而是由maven间接导入，那么问题可能就在这里。有可能maven割裂了jar包文件和模块之间的联系。那么模块的名字应该就在pom文件里面。看到pom文件，里面有groupId和artifactId，其中一个必是模块名。到eclipse查找了一下，果然，发现一个模块叫做`objmodel.importer`，和artifactId一致，除了把减号换成小数点。

把这个模块名加入到module-info.java文件中，问题解决。


