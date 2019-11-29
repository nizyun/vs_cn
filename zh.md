
# 基准

本文档显示了我们做的实验和得到的结果。我们做了两个系列的实验。首先，我们在单个节点上进行了基于faiss的改进的Vearch ivfpq模型的召回率实验。其次，基于Vearch 集群进行了实验。

我们使用检索返回的k个候选值（k∈{1，10，100}）是否包含最近邻的结果来计算召回，其中标签通过暴力搜索获得。

实验数据会因为实现、不同机器等的变化而略有变化。

## 数据集

实验分别在128维SIFT特征和512维VGG特征上进行。

### 数据集SIFT1M

可以从如下网址下载ANN_SIFT1M 

http://corpus-texmex.irisa.fr/

### 数据集VGG1M 和 VGG10M

分别收集100万和1000万的图片数据提取VGG特征得到VGG1M （100万）和VGG10M（1000万），其中VGG1M 和VGG10M并不相关。

### 数据集VGG100M , VGG500M 和 VGG1B

另外收集了10亿图片数据来构建VGG100M(1亿) , VGG500M（5亿） 和 VGG1B（10亿）。

## Nprobe 实验

实验分别在SIFT1M, VGG1M 和 VGG10M上进行。其中ncentroids =256，nbytes = 32，nprobe  ∈{1,5,10,20,30,40,50,80,100,200}。图中数据为recall@1结果。

### 结果

![nprobe](/doc/img/benchs/nprobe.png)

可以看到当nprobe超过25后，召回基本上没有明显的变化了。

## Ncentroids 实验

实验在VGG10M进行。其中nprobe = 50，nbytes = 32， ncentroids∈{64,128,256,512,1024,2048,4096,8192} 。图中数据为recall@1结果。

### 结果

![ncentroids](/doc/img/benchs/ncentroids.png)

可以看到召回基本不随ncentroids变化而变化，但是ncentroids越大，QPS越高。

## Nbytes 实验

实验在VGG10M进行。其中nprobe = 50，ncentroids = 256， nbytes ∈{4,8,16,32,64}。图中数据为recall@1结果。

### 结果

![nbytes](/doc/img/benchs/nbytes.png)

当nbytes越大，召回越高，当然QPS随之降低。

## 对比实验

实验在 SIFT1M, VGG1M 和 VGG10M 上进行，并与faiss中的一些模型进行对比。

### 模型参数

表格中参数为空，则对应模型不包含该参数。其中 links, efSearch 和 efConstruction 为 faiss 中的 hnsw 定义的参数。

|  model  | ncentroids | nprobe | bytes of SIFT | bytes of VGG | links | efSearch | efConstruction |
| :-----: | :--------: | :----: | :-----------: | :----------: | :---: | :------: | :------------: |
|   pq    |            |        |      32       |      64      |       |          |                |
|  ivfpq  |    256     |   20   |      32       |      64      |       |          |                |
|  imipq  |  2^(2*10)  |  2048  |      32       |      64      |       |          |                |
| opq+pq  |            |        |      32       |      64      |       |          |                |
|  hnsw   |            |        |               |              |  32   |    64    |       40       |
| ivfhnsw |    256     |   20   |               |              |  32   |    64    |       40       |
| Vearch  |    256     |   20   |      32       |      64      |       |          |                |

### 结果

SIFT1M的召回:

|  model  | recall@1 | recall@10 | recall@100 |
| :-----: | :------: | :-------: | :--------: |
|   pq    |  0.6274  |  0.9829   |   0.9999   |
|  ivfpq  |  0.6167  |  0.9797   |   0.9960   |
|  imipq  |  0.6595  |  0.9775   |   0.9841   |
| opq+pq  |  0.6250  |  0.9821   |   1.0000   |
|  hnsw   |  0.9792  |  0.9867   |   0.9867   |
| ivfhnsw |  0.9888  |  0.9961   |   0.9961   |
| Vearch  |  0.8649  |  0.9721   |   0.9722   |

VGG1M的召回 :

|  model  | recall@1 | recall@10 | recall@100 |
| :-----: | :------: | :-------: | :--------: |
|   pq    |  0.5079  |  0.8922   |   0.9930   |
|  ivfpq  |  0.4985  |  0.8792   |   0.9704   |
|  imipq  |  0.5077  |  0.8618   |   0.9248   |
| opq+pq  |  0.5213  |  0.9105   |   0.9975   |
|  hnsw   |  0.9496  |  0.9550   |   0.9551   |
| ivfhnsw |  0.9690  |  0.9744   |   0.9745   |
| Vearch  |  0.9536  |  0.9582   |   0.9585   |

VGG10M的召回 :

|  model  | recall@1 | recall@10 | recall@100 |
| :-----: | :------: | :-------: | :--------: |
|   pq    |  0.5842  |  0.8980   |   0.9888   |
|  ivfpq  |  0.5913  |  0.8896   |   0.9748   |
|  imipq  |  0.5925  |  0.8878   |   0.9570   |
| opq+pq  |  0.6126  |  0.9160   |   0.9944   |
|  hnsw   |  0.8877  |  0.9069   |   0.9074   |
| ivfhnsw |  0.9638  |  0.9839   |   0.9843   |
| Vearch  |  0.9272  |  0.9464   |   0.9468   |

## 集群实验

集群实验分别对 VGG100M , VGG500M 和 VGG1B进行实验，并添加是否过滤来进行实验，其中过滤是指在搜索的时候指定过滤条件来缩小搜索范围。VGG100M 搭建了 3 个masters, 3个 routers 和5 个 partition services 的集群。 VGG500M搭建了 3 个masters, 3个 routers 和24个 partition services 的集群。VGG1B搭建了 3 个masters, 6个 routers 和48 个 partition services 的集群。

### 结果

![cluster](/doc/img/benchs/cluster.png)

可以看到当average latency超过一定程度，QPS就不再发生明显变化了。