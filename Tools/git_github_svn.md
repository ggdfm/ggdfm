---
layout: default
title: git_github_svn说明
nav_order: 5
parent: Tools
---

### git_github_svn
#### git
- 命令：
````
通过分支及网址拉项目：git clone -b 分支名 网址
````
#### github

##### github网站找不到的解决方法
- 查询ip的网址，请[点击](https://www.ip138.com/)
````
通过域名查询相应的ip地址，如以下：
20.205.243.166 github.com
199.232.69.194 github.global.ssl.fastly.net
185.199.109.153 assets-cdn.github.com
185.199.110.153 assets-cdn.github.com
185.199.108.153 assets-cdn.github.com
````
- windows可以将查询到的以上ip与域名填入本地C:\Windows\System32\drivers\etc的hosts文件中。
  assets-cdn.github.com的ip是不会改变的，主要是其他两个会被变动。
  各ip地址需要ping的通才行
#### svn
