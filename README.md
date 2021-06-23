# Mongo Replication
   搭建 mongo replica set 环境

* 运行相关 （针对注意事项的问题）
  - 首次运行 将 docker-compose.yml 中的注释去掉
  ```
   # environment:
   #   MONGO_INITDB_ROOT_USERNAME: root
   #   MONGO_INITDB_ROOT_PASSWORD: 123456
  ```
 - 启动后，再将恢复原样，重启容器


# 相关指令

```
// 集群搭建后进行初始
rs.initiate({
  _id:"rstest", // replSet指定的名称
  members:[{
    _id:0,
    host:"192.168.32.137:27017" // 主节点ip与端口
  }]
})

rs.add("192.168.32.137:27018");

// arbiter 不存放数据
rs.addArb("192.168.32.137:27019");

// mongo shell demo
mongo "mongodb://192.168.32.137:27017,192.168.32.137:27018,192.168.32.137:27019/?replicaSet=rstest"

```

* 注意事项
    - https://github.com/docker-library/mongo/issues/329 【针对上面的yml】
        - yml 中 environment 引起的问题
* 潜在的问题（偶现）
  - https://jira.mongodb.org/browse/SERVER-37724
