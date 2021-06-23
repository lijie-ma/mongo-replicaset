## Mongo Replication
   搭建 mongo replica set 环境

* 第一步 创建安装目录，示例使用docker-compose.yml 中的目录
```
mkdir -p /data/mongo/conf /data/mongo/log /data/mongo/db
mkdir /data/mongo/log/m1 /data/mongo/log/m2 /data/mongo/log/m3
chmod a+w /data/mongo/log/m1 /data/mongo/log/m2 /data/mongo/log/m3

mkdir /data/mongo/db/m1 /data/mongo/db/m2 /data/mongo/db/m3
chmod a+w /data/mongo/db/m1 /data/mongo/db/m2 /data/mongo/db/m3

```
* 第二步 复制配置文件，设置运行文件权限
```
cp conf/mongod.conf conf/keyfile /data/mongo/conf
# conf/keyfile 要求400 或者600的权限
chown polkitd:polkitd /data/mongo/conf/keyfile 
chmod 0400 /data/mongo/conf/keyfile
```
* 第三步 取消 docker-compose.yml 中下面配置的注释
 ```
   # environment:
   #   MONGO_INITDB_ROOT_USERNAME: root
   #   MONGO_INITDB_ROOT_PASSWORD: 123456
 ```
* 第四步 运行
 ```
   # 首次运行
   docker-compose up -d
   
   # 查看容器
   docker ps
   
   # 此时重启容器
   docker-compose restart && docker ps
 ```
 * 第五步 错误修复
  ```
   # 此时重启容器
   docker-compose restart && docker ps
   # 在status 栏会看到类似的错误“Restarting (1) Less than a second ago” 
   # 通过 docker logs -f m1 看到详细日志
        # “QUERY    [js] uncaught exception: Error: couldn't add user: command createUser requires authentication :”
   # 此时还原 第三步 对docker-compose.yml 修改即可
   # 即 注释掉文件中的
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: 123456
   # 再次执行
    docker-compose up -d
   # 错误已被修正
 ```

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
