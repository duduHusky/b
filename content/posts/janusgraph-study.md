---
title: "JanusGraph使用笔记"
date: 2020-03-05T14:39:59+08:00
draft: false
tags: ["janusgraph"]
categories: ["图数据库"]
---

## 下载源码文件(如果你使用Docker也是可以的)
  [下载地址](https://github.com/JanusGraph/janusgraph/releases)

## 解压下载的文件夹, 比如: `janusgraph-0.4.1-hadoop2.zip`

## 配置
例如：存储后端使用 `Cassandra`, 索引使用es,修改janusgraph-cql-es.properties
`vim janusgraph-0.4.1-hadoop2/conf/janusgraph-cql-es.properties`

## 配置文件重点内容
```linux
#storage.hostname=这个地方要配置Cassandra的地址
storage.hostname=host:port

# ES服务的节点地址及端口
index.search.hostname=mhostname1:9200,hostname2:9200,hostname3:9200<i>
```

## 命令行执行`bin/gremlin.sh`
初始化完成后执行`graph = JanusGraphFactory.open('conf/janusgraph-cql-es.properties')`
>配置文件路径写自己机器上的位置

## 创建顶点标签
```linux
mgmt = graph.openManagement();
mgmt.makeVertexLabel('person').make();
mgmt.makeVertexLabel('country').make();
mgmt.makeVertexLabel('weapon').make();
mgmt.getVertexLabels();
mgmt.commit()
```

## 创建边标签
```linux
mgmt = graph.openManagement();
brother = mgmt.makeEdgeLabel("brother").make();
mgmt.makeEdgeLabel("battled").make();
mgmt.makeEdgeLabel("belongs").make();
mgmt.makeEdgeLabel("use").make();
mgmt.getRelationTypes(EdgeLabel.class);
mgmt.commit()
```

## 创建属性
```linux
mgmt = graph.openManagement();
name = mgmt.makePropertyKey('name').dataType(String.class).cardinality(Cardinality.SET).make();
mgmt.buildIndex('nameUnique', Vertex.class).addKey(name).unique().buildCompositeIndex();
age = mgmt.makePropertyKey("age").dataType(Integer.class).make();
mgmt.buildIndex('age2', Vertex.class).addKey(age).buildMixedIndex("sanguo");
mgmt.getGraphIndexes(Vertex.class);
mgmt.commit()<i>
```

## 添加顶点
```linux
g = graph.traversal()

liubei = g.addV("person").property('name','刘备').property('age',45);
guanyu = g.addV("person").property('name','关羽').property('age',42);
zhangfei = g.addV("person").property('name','张飞').property('age',40);
lvbu = g.addV("person").property('name','吕布').property('age',38);
g.addV("country").property('name','蜀国');
g.addV("weapon").property('name','方天画戟');
g.addV("weapon").property('name','双股剑');
g.addV("weapon").property('name','青龙偃月刀');
g.addV("weapon").property('name','丈八蛇矛');

for (tx in graph.getOpenTransactions()) tx.commit();<i>
```

## 添加关系
```linux
g.addE('e').from(g.V(xxx)).to(g.V(xxx))
```

### 遇到事务的错误
```linux
gremlin> graph.getOpenTransactions()
==>standardjanusgraphtx[0x14ba9376]
==>standardjanusgraphtx[0x477aaf55]
```
然后可以回滚事务`graph.getOpenTransactions().getAt(0).rollback()`

或者强行执行
```linux
gremlin> mgmt = graph.openManagement()
gremlin> mgmt.getOpenInstances()
==>0934f2eb69223-Chriss-MacBook-Pro-2-local2
==>0729845962091-remoteMachine1
gremlin> mgmt.forceCloseInstance('0729845962091-remoteMachine1') 
gremlin> mgmt.commit()
```