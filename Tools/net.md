---
layout: default
title: NET
nav_order: 2
parent: Tools
---

###### 想要网站访问速度更快，可以在本地的---->
###### C:\Windows\System32\drivers\etc 下的hosts文件中
###### 添加想要访问的网站的ip+空格+域名  ip获取可以通过cmd中ping 域名获得。      

### HttpResponse需要引入依赖
````
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.5.7</version>
</dependency>
````
### 获取其他系统对应方法的地址，进行请求响应
````        
    @Test       
    public void test_471(){     
        RiskSurveyTaskProduceRequest request = new RiskSurveyTaskProduceRequest();      
        request.setTaskNo("10q4ii");        
        request.setCustomerName("固化3");     
        request.setCompanyCode("Q0001");        
        request.setBranchCode("分");     
        request.setMidBranchCode("中支");     
        request.setSubBranchCode("支");      
        request.setOperator("操作人3");        
        request.setRiskSurveyor("勘员3");     
        request.setVehicleCount("221");     
        request.setSurType("1");    
        String url = "http://ip:端口号/**/**/produceTask";  
        HttpResponse responseData = HttpUtil.createPost(url)  
                .setConnectionTimeout(3000)  
                .setReadTimeout(3000)  
                .contentType("application/json")  
                .body(JSON.toJSONString(request)).execute();  
        System.out.println("请求响应："+responseData.toString());  
        System.out.println("请求报文："+JSON.toJSONString(request));  
        System.out.println("body:"+responseData.body());  
        if(responseData.isOk()){  
            String body = responseData.body();  
            RiskSurveyNoticeResponse response = JSONObject.parseObject(body, RiskSurveyNoticeResponse.class);  
            System.out.println("响应："+response);  
        }  
  
    }  
````

#### test_471方法运行结果，如下：
````
请求响应：Response Headers:   
    Keep-Alive=[timeout=60]  
    Transfer-Encoding=[chunked]  
    null=[HTTP/1.1 200]              //responseData.isOk()为此200
    Connection=[keep-alive]  
    Date=[Wed, 26 Oct 2022 07:37:16 GMT]  
    Content-Type=[application/json]  
Response Body:   
    {"code":"200","message":"风勘任务生成成功"}   

请求报文：{"branchCode":"分","companyCode":"Q0001","customerName":"固化3","midBranchCode":"中支","operator":"操作人3","riskSurveyor":"勘员3","subBranchCode":"支","surType":"1","taskNo":"10q4ii","vehicleCount":"221"}  
body:{"code":"200","message":"成功"}  
响应：RiskSurveyNoticeResponse{code='200', message='成功'}  
````

### json相关方法
#### 引入依赖
````
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.4</version>
</dependency>
````

#### json语法规则

````
数组（Array）用方括号(“[]”)表示。
对象（Object）用大括号（”{}”）表示。
名称/值对（name/value）组合成数组和对象。
名称（name）置于双引号中，值（value）有字符串、数值、布尔值、null、对象和数组。
并列的数据之间用逗号（“,”）分隔
````
##### json语法举例子
````
{
    "age":1211,
    "bool":true,
    "jsonJ":{
                "tes":"1111"
                },
    "name":"张三",
    "sex":["男","女"]
}
````

##### 待测试
````
#### 使用ip传信息（操作系统必须是Windwos 2000或Windows XP）
##### 步骤：双方开启信息messenger服务，在服务中手动启动。之后在cmd中net send ip地址 "Are you OK?!"

````

