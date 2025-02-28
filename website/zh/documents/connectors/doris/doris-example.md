# Doris连接器配置示例

父目录: [doris-connector](./doris.md)


## 测试用Doris配置

假设doris集群配置如下:
 - fe: `127.0.0.1:1234`
 - jdbc query地址: `127.0.0.1:4321`

账户信息为: 
 - 用户: `test_user`
 - 密码: `1234567`

要写入的库表为:
 - 库: test_db
 - 表: test_doris_table

该表的建表语句为:

```sql
CREATE TABLE `test_db`.`test_doris_table` ( 
    `id` bigint(20) NULL COMMENT "", 
    `bigint_type` bigint(20) NULL COMMENT "", 
    `string_type` varchar(100) NULL COMMENT "", 
    `double_type` double NULL COMMENT "", 
    `decimal_type` decimal(27, 9) NULL COMMENT "", 
    `date_type` date NULL COMMENT "",
    `partition_date` date NULL COMMENT "" 
) 
ENGINE=OLAP 
DUPLICATE KEY(`id`) 
COMMENT "OLAP" 
PARTITION BY RANGE(`partition_date`) 
(
    PARTITION p20221010 VALUES [('2022-10-10'), ('2022-10-11'))
) 
DISTRIBUTED BY HASH(`id`) BUCKETS 10 
PROPERTIES 
( 
    "replication_num" = "3"
)
```


## Doris写连接器

可用下面的配置写入上述`test_db.test_doris_table`表的 `p20221010` 分区:

```json
{
   "job": {
     "writer": {
       "class": "com.bytedance.bitsail.connector.doris.sink.DorisSink",
       "fe_hosts": "127.0.0.1:1234",
       "mysql_hosts": "127.0.0.1:4321",
       "user": "test_user",
       "password": "1234567",
       "db_name": "test_db",
       "table_name": "test_doris_table",
       "table_model": "unique",
       "partitions": [
         {
           "name": "p20221010",
           "start_range":["2022-10-10"],
           "end_range":["2022-10-11"]
         }
       ],
       "columns": [
         {
           "index": 0,
           "name": "id",
           "type": "bigint"
         },
         {
           "index": 1,
           "name": "bigint_type",
           "type": "bigint"
         },
         {
           "index": 2,
           "name": "string_type",
           "type": "varchar"
         },
         {
           "index": 3,
           "name": "double_type",
           "type": "double"
         },
         {
           "index": 4,
           "name": "decimal_type",
           "type": "double"
         },
         {
           "index": 5,
           "name": "date_type",
           "type": "date"
         },
         {
           "index": 6,
           "name": "partition_date",
           "type": "date"
         }
       ]
     }
   }
}
```