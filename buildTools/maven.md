---
layout: default
title: Maven
nav_order: 1
parent: BuildTools
---

## Maven

### maven命令         
````    
clean： 删除项目路径下的target文件，但是不会删除本地的maven仓库已经生成的jar文件。
compile：编译命令，只编译选定的目标，不管之前是否已经编译过，会在你的项目路径下生成一个target目录，在该目录中包含一个classes文件夹，里面全是生成的class文件及字节码文件。
package：会在你的项目路径下一个target目录，并且拥有compile命令的功能进行编译，同时会在target目录下生成项目的jar/war文件。
        注意：如果a项目依赖于b项目，打包b项目时，只会打包到b项目下target下，编译a项目时就会报错，因为找不到所依赖的b项目，说明a项目在本地仓库是没有找到它所依赖的b项目，这时就用到install命令了
install：包含了package命令功能，不但会在项目路径下生成class文件和jar包，同时会在你的本地maven仓库生成jar文件，供其他项目使用。
Build：是对整个工程进行彻底的重新编译，而不管是否已经编译过。
````

### pom 项目对象模型（project object model）   
- 内置属性      
````    
${basedir}表示当前项目根目录
${version}表示当前项目版本
````    
- 常用的pom属性
````
<project> 是 pom.xml 的根节点，project.basedir 就是根节点的下级节点
    - ${project.build.sourceDirectory}：项目的主源码目录，默认为 src/main/java
    - ${project.build.testSourceDirectory}：项目的测试源码目录，默认为 src/test/java
    - ${project.build.directory}：项目构件输出目录，默认为 target/
    - ${project.outputDirectory}：项目主代码编译输出目录，默认为 target/classes/
    - ${project.testOutputDirectory}：项目测试代码编译输出目录，默认为 target/test-classes/
    - ${project.groupId}：项目的 groupId
    - ${project.artifactId}：项目的 artifactId
    - project.version：项目的version，与project.version：项目的version，与{version}等价
    - project.build.fianlName：项目打包输出文件的名称。默认为{project.build.fianlName}：项目打包输出文件的名称。默认为project.build.fianlName：项目打包输出文件的名称。默认为{project.artifactId}-${project.version}
````
- 自定义属性：用户可以在pom的元素下自定义Maven属性
````
- 1.在<project>标签下自定义变量：
<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<spring.version>4.1.3.RELEASE</spring.version>
		<lombok.version>1.16.16</lombok.version>
</properties>
    自定义了三个属性，例如：lombok.version	
- 2.变量的调用:使用${变量名}来调用
  <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
  </dependency>
````