---
layout: default
title: DB--oceanBase
nav_order: 1
parent: DATABASE
---

# OCEANBASE
{: .no_toc }

## OceanBase 开发者中心（ODC）
- 支持连接OceanBase中的mysql租户和Oracle租户的数据库，同时为数据库开发者提供数据库日常开发操作、sql诊断、websql和会话管理以及数据导入导出功能。
-[菜鸟教程 云数据库OceanBase教程](https://www.w3cschool.cn/oceanbase/oceanbase-gu1e3fsx.html)
### oceanbase的六个工具组件
- oms 迁移工具（会使用到）：只做数据迁移  注：数据库对象是使用DBCAT导入导出的（非表对象，如存储过程、序列等）
DBCAT 是一款轻量级的命令行工具，可用于提供源数据库到 OceanBase 数据库的 DDL 转换和 Schema 比对等功能。工具文件名为 dbcat-[版本号]-SNAPSHOT.tar.gz，下载后解压缩即可使用，可执行文件名为 dbcat。
DBCAT 是 OMS 的一个组件，是数据对象采集和转换组件
- odc 开发者中心（开发者重点使用）:白屏工具，日常开发，数据导入导出，sql诊断、会话管理。
- OCP云平台 监控/运维
- 数据迁移命令行工具：脚本  文件导入导出 OBLOADER、OBDUMPER
- OAT(管理者工具)
- OMA（兼容性评估）


### 分布式架构-高级技术
- 一个集群下面有多个zone ，一个zone下面有多个oBserver。   
#### OB资源的分配流程
- 1.查看集群资源由各个节点的聚合情况。 
     ````sql
       select zone,concat(svr_ip,':',svr_port) observer, 
       cpu_capacity,cpu_total,cpu_assigned,cpu_assigned_percent,
       mem_capacity,mem_total,mem_assigned,mem_assigned_percent,
       unit_Num,round(`load`,2) `load`, round(cpu_weight,2) cpu_weight, 
       round(memory_weight,2) mem_weight, 
       leader_countfrom __all_virtual_server_statorder by zone,svr_ip;
     ````
- 2.定义资源规格。(对cpu、Mem、Disk、iops、session进行约束，当前版本只对cpu与Mem进行约束)
     ````sql
        createresource unit S2 max_cpu=20,min_cpu=20,
        max_memory='40G',min_memory='40G',
        max_iops=10000,
        min_iops=1000,
        max_session_num=1000000,
        max_disk_size='1024G';
     ````
     ````sql
        createresource unit S3 max_cpu=20,min_cpu=20,
        max_memory='100G',min_memory='100G',
        max_iops=10000,
        min_iops=1000,
        max_session_num=1000000,
        max_disk_size='1024G';
     ````
  
- 创建租户的资源分配：资源单元（Unit）是资源分配的最小单元，同一个unit不能跨节点（observer）。
        每个租户在一台oBserver上只能有一个Unit。Unit是数据的容器。
  