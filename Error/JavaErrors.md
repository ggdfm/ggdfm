---
layout: default
title: java报错集锦
nav_order: 1
---

#### java 报错集锦

- 使用gradle环境时，在gradle5.0以上版本使用annotationProcessor引入lombok依赖，
- 只有compileOnly时使用gradle的build会报错找不到对应生成的代码。
````
//lombok
   // compileOnly 'org.projectlombok:lombok:1.18.24'//只在编译时有效，不会参与打包
    annotationProcessor 'org.projectlombok:lombok:1.18.24'//会将依赖打包
````
- 扩展
````
compileOnly 'org.projectlombok:lombok:1.18.4'
annotationProcessor 'org.projectlombok:lombok:1.18.4'
testCompileOnly 'org.projectlombok:lombok:1.18.4'
testAnnotationProcessor 'org.projectlombok:lombok:1.18.4'
````

- 在gradle环境中出现错误：Expiring Daemon because JVM heap space is exhausted
````
在gradle.properties文件中
#配置编译时的虚拟机大小
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8

---------------------或者都加上以下部分-----------------
#开启线程守护，第一次编译时开线程，之后就不会再开了
org.gradle.daemon=true

#配置编译时的虚拟机大小
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8

#开启并行编译，相当于多条线程再走
org.gradle.parallel=true

#启用新的孵化模式
org.gradle.configureondemand=true

````
