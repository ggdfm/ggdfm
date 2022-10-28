---
layout: default
title: FileUtils工具类
nav_order: 5
parent: Tools
---

### FileUtils方法
- File getFile([string] 目标地址);
````
    读取符合条件的数据
    File file = FileUtils.getFile(receivePath);
    File[] files = file.listFiles((dir, name) ->  name.startsWith("规定名称") && name.endsWith(".txt"));
````