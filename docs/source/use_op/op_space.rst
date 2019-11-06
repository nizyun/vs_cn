表空间操作
=================


创建表空间
--------

::
   
  curl -XPUT -H "content-type: application/json" -d'
  {
      "name": "tpy",
      "dynamic_schema": "strict",
      "partition_num": 2,
      "replica_num": 1,
      "engine": {"name":"gamma", "max_size":1000000,"nprobe":10,"metric_type":-1,"ncentroids":-1,"nsubvector":-1,"nbits_per_idx":-1},
      "properties": {
          "image_type": {
              "type": "keyword"
          },
          "fku": {
              "type": "integer",
              "index":"false"
          },
          "tags": {
              "type": "keyword",
              "array":true,
              "index":"true"
          },
          "image_vec": {
              "type": "vector",
              "model_id": "img",
              "dimension": 5000
          }
      }
  }
  ' http://xxxxxx/space/tpy/_create


查看表空间
--------
::
  
  curl -XGET http://xxxxxx/space/$db_name/$space_name


删除表空间
--------
::
 
  curl -XDELETE http://xxxxxx/space/$db_name/$space_name

