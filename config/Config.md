---
layout: default
title: Config
nav_order: 4
has_children: true
permalink: docs/config
---

### Config
{: .no_toc }

#### yml配置固定值，且被java项目调用，可以进行以下操作
- 1.在对应的yml环境中添加    
````yaml
image: 
  content: #image.content根据需要可任意换名称
    pathtextid: all
    appname: 七七五一
    appcode: QQWY
    systemId: 751
    roleCode: qwy
    lists： 
      - xiaozeng
      - xiaobai
    maps： {k1：v1,k2:v2}
    person: 
        name: qq
        age: 18
````    
- 2.写上java类对应上列字段
`````java
@Data
//@Component
@ConfigurationProperties(           //是springboot提供读取配置文件的一个注解
        prefix = "image.content"
)
@EnableConfigurationProperties // 与@Component任选其一
public class ImageContentProperties {

    private String appname;
    private String appcode;
    private String roleCode;
    private String systemId;
    private String pathtextid;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Person person;
}
`````   
- @ConfigurationProperties注解的注意事项：  
`````   
    - 前缀定义了哪些外部属性将绑定到类的字段上
    - 根据 Spring Boot 宽松的绑定规则，类的属性名称必须与外部属性的名称匹配
    - 我们可以简单地用一个值初始化一个字段来定义一个默认值
    - 类本身可以是包私有的
    - 类的字段必须有公共 setter 方法
    - 这个prefix 必须小写，不然会报错
`````   
- 如果一个配置类只配置@ConfigurationProperties注解，而没有使用@Component，那么在IOC容器中是获取不到properties 配置文件转化的bean。
  说白了 @EnableConfigurationProperties 相当于把使用 @ConfigurationProperties 的类进行了一次注入。