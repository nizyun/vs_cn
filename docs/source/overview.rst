概述
========

Vearch 是对大规模深度学习向量进行高效相似搜索的弹性分布式系统。

整体架构
-----------------------

.. image:: pic/vearch-arch.png
   :align: center
   :scale: 50 %
   :alt: Architecture

数据模型: 空间，文档，向量，标量。

组件: Master，Router，PartitionServer。

Master: 负责schema管理，集群级别的源数据和资源协调。

Router: 提供RESTful API: create、delete、search、update; 请求路由转发及结果合并。

PartitionServer(PS): 基于raft复制的文档分片; Gamma向量搜索引擎,它提供了存储、索引和检索向量、标量的能力。


功能简介
-----------------------

1、支持CPU与GPU两种版本。

2、支持单个文档定义多个向量字段, 添加、搜索批量操作。

3、支持数值字段范围过滤与string字段标签过滤。

4、支持IVFPQ、HNSW、二进制等索引方式(HNSW、二进制方式在测试中)。

5、支持Python SDK本地快速开发验证。

6、支持机器学习算法插件方便系统部署使用。


系统特性
-----------------------
1、C++实现的gamma引擎，保证向量的快速检索。

2、支持内积(InnerProduct)与欧式距离(L2)方法计算向量距离。

3、支持内存、磁盘、jimdb三种数据存储方式，支持超大数据规模(jimdb测试中)。

4、基于raft协议实现数据多副本存储。

