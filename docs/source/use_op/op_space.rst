表空间操作
=================

http://master_server代表master服务，$db_name是创建的库名, $space_name是创建的表空间名

创建表空间
--------

::
   
  curl -XPUT -H "content-type: application/json" -d'
  {
      "name": "space1",
      "partition_num": 1,
      "replica_num": 1,
      "engine": {
          "name": "gamma",
          "index_size": 70000,
          "max_size": 10000000,
          "nprobe": 10,
          "metric_type": 0,
          "ncentroids": 256,
          "nsubvector": 32
      },
      "properties": {
          "field1": {
              "type": "keyword"
          },
          "field2": {
              "type": "integer"
          },
          "field3": {
              "type": "float",
                  "index": "true"
          },
          "field4": {
              "type": "string",
              "array": true,
              "index": "true"
          },
          "field5": {
              "type": "integer",
              "array": false,
              "index": "true"
          },
          "field6": {
              "type": "vector",
              "dimension": 128
          },
          "field7": {
              "type": "vector",
              "dimension": 256
          }
      }
  }
  ' http://master_server/space/$db_name/_create


参数说明:

+-------------+---------------+---------------+----------+-----------------+
|字段标识     |字段名称       |类型           |是否必填  |备注             | 
+=============+===============+===============+==========+=================+
|name         |空间名称       |sting          |是        |                 |
+-------------+---------------+---------------+----------+-----------------+
|partition_num|分片数量       |int            |是        |                 |
+-------------+---------------+---------------+----------+-----------------+
|replica_num  |副本数量       |int            |是        |                 |
+-------------+---------------+---------------+----------+-----------------+
|engine       |引擎配置       |json           |是        |搜索引擎配置     |
+-------------+---------------+---------------+----------+-----------------+
|properties   |引擎配置       |json           |是        |定义表字段及类型 |
+-------------+---------------+---------------+----------+-----------------+

1、name 不能为空，不能以数字或下划线开头， 尽量不使用特殊字符等。

2、partition_num 指定表空间数据分片数量，不同的分片可分布在不同的机器，来避免单台机器的资源限制。

3、replica_num 副本数量，建议设置成3，表示每个分片数据有两个备份，保证数据高可用。

1、index_size 指定每个分片的记录数量达到多少开始创建索引，不指定则不创建索引

2、max_size  指定每个分片最多存储的记录数量，防止服务器内存占用过多

3、nprobe    指定在索引中搜索的桶的数量，不能大于ncentroids的值

4、metric_type 指定计算方式，内积或L2

5、ncentroids  指定建索引时分桶的数量

6、nsubvector  PQ子向量数量

1、type：支持的类型:  keyword、 integer、 float、 vector  (keyword即string)

2、index 参数指定字段是否创建索引，适用于integer 和 float字段，创建索引的字段可以使用数值过滤，加快检索速度。

3、array 指定字段是否包含多个值。

4、dimension 定义type是vector的字段，指定特征维数大小。

5、vector类型字段可以定义多个，即一条记录支持多个特征字段。



查看表空间
--------
::
  
  curl -XGET http://master_server/space/$db_name/$space_name


删除表空间
--------
::
 
  curl -XDELETE http://master_server/space/$db_name/$space_name

