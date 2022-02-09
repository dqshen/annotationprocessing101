# 工程技术背景
本项目是java注解处理器的实现和调试方法演示项目  
注解是jdk5引入的注释机制，对于所有注解而言，有两个重要的元注解，分别是ElementType和RetentionPolicy  
一个典型的自定义注解需要至少用这两个元注解来注释，注释形式如下：  
  
@Target(ElementType.TYPE)  
@Retention(RetentionPolicy.RUNTIME)  
public @interface MyAnnotation1 {  
}  
  
其中，ElementType和RetentionPolicy都是枚举类型，  
ElementType标记自定义注解可以注释的对象范围(类、方法、字段、参数、注解等等)  
RetentionPolicy标记注解可以被访问的作用域  
RetentionPolicy.SOURCE 注解仅在编译阶段可被访问，主要由编译器处理  
RetentionPolicy.CLASS 默认配置，注解将被保留在CLASS文件中，可以通过Annotation Processor机制处理  
RetentionPolicy.RUNTIME 注解可在运行中的jvm中访问，常用的用反射方法获取的注解都需要用这个配置  
从处理级别来看，SOURCE < CLASS < RUNTIME，用在低级别的处理机制也可以用于高级别，反之不可，例如编译器  
能够处理所有三个级别的注解，而反射方法只能用于处理RUNTIME标记的注解。  
  
Annotation Processor机制是用于处理默认配置CLASS的，它能够在javac处理注解对象时，引入额外的processor  
处理被注解的对象信息，生成新的.java文件，并和原始的java程序一起编译，简单来说是在javac过程中动态生成程序。  
  
# 工程概况说明
|----factory  
|       |  
|       └-----annotation  
|       └-----processor  
|       └-----pom.xml(parent)  
|  
|  
└----factory-sample  
        |  
        └-----pizzastore  
  
  
本工程共有4个子工程，都是maven工程  
1.factorypattern工程，一个pom类型的工程，是annotation和processor的parent工程，仅由一个pom.xml文件构成，定义了两个子工程的公用配置和参数；  
2.annotation工程，创建了自定义注解Factory,这个注解在pizzastore工程中使用，在编译pizzastore时，processor工程创建的注解处理器被引入到编译过程中处理这个注解，生成MealFactory.java文件；  
3.processor工程，创建了注解处理器(annotation processor)Factory Processor，通过maven打包后，可以被引入到pizzastore工程的javac过程中，处理Factory注解，生成MealFactory.java文件；  
4.pizzastore工程，使用了annotation注解，并在pom.xml中引用了processor工程，在编译时让processor处理Factory注解生成相关java文件用于完成编译；  
  
工程使用(调试)步骤(以VS Code为例)：  
1.安装annotation、processor两个工程----在factory目录下，执行mvn clean install  
2.调试状态编译pizzastore工程-----------在pizzastore目录下，执行mvnDebug package命令(如果已经成功执行过，需要先mvn clean一下)  
执行后命令打印会提示maven启动并暂停，监听端口，等待调试程序介入绑定(attach)，annotation和processor工程已在pizzastore的pom.xml中引入  
3.调试运行processor-------------------用IDE单独打开processor工程，在主类init函数入口添加断点，使用下面的Debug配置启动远程调试  
        {     
            "type": "java", // 调试环境时java  
            "name": "Debug (Attach)", // 调试的名字，会出现在debug窗口选项中，随便写  
            "projectName": "processor", // 指定ide寻找调试源码的位置，就是本工程的名字  
            "request": "attach", // 调试方式，因为是远程调试，写attach  
            "hostName": "localhost",// 调试主机名，因为是attach本地启动的程序，所以写localhost  
            "port": 8000  
        }  
注意所有的安装 编译 调试运行都需要使用相同的jdk
  
# 程序实现说明  
参考下面的原始工程解说链接  
# annotationprocessing101  
This repository is part of the blog entry I have written about annotation processing.

http://hannesdorfmann.com/annotation-processing/annotationprocessing101
