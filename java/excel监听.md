---
layout: default
title: Excel监听
nav_order: 3
parent: JAVA
---

## excel监听

- 继承了AnalysisEventListener并重写了相应的方法后，它会自动执行。
  在你的代码中，你可以看到你的监听器被创建并传递给了ExcelReader，这意味着ExcelReader将使用你的监听器来处理Excel文件。
  当ExcelReader读取Excel文件时，它将调用你的监听器的相应方法来处理Excel文件中的数据。
  因此，只要你的监听器被正确地配置和传递给ExcelReader，它就会自动执行。
  

