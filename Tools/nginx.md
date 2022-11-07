---
layout: default
title: Nginx
nav_order: 6
parent: Tools
---

#### nginx配置

#### nginx启动/停止命令
````
启动：start nginx /重新载入：nginx.exe -s reload/停止：nginx.exe -s stop
````

##### 出现错误：Sorry, the page you are looking for is currently unavailable. Please try again later.
###### 看nginx的log文档查看错误，语法错误可以尝试将单斜杠换成双斜杠。

````
更改前：
location / {
            root   D:\workspace\normalize\cfms-web;
            index  indexall.html indexall.htm;
        }
更改后：
location / {
            root   D:\\workspace\\normalize\\cfms-web;
            index  indexall.html indexall.htm;
        }
````