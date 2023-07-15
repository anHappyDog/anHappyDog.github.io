---
title: neo4j和cypher
author: lonelywatch
date: 2023-7-14 22:25 +0800
categories: [SQL]
tags: [SQL,NEO4J,CYPTHER,知识图谱]
---
本来以为neo4j就是个跟mysql这种差不多的DBMS,结果发现用的是不同的cypher语言，还跟啥知识图谱挂钩，突然发现python的课设要求还挺多的，要学pyqt，爬虫(带着点前端)，还要学这个，想做好点还要点网络开发知识。&_&

## Neo4j与Cypher介绍

Neo4j是一种高性能的、具有图结构数据库特点的数据库管理系统。它使用图作为数据模型，通过节点和关系来表示和存储数据。Neo4j可以处理大规模的图数据，并支持复杂的图查询和分析(chatgpt)。

Cypher是Neo4j数据库的查询语言，专门用于在Neo4j图数据库中进行数据查询和操作。它提供了一种简洁、直观的方式来描述和操作图数据模型。Cypher被设计为类似于自然语言的查询语言，易于学习和使用(chatgpt)。

在python中使用neo4j和使用mysql差不多，大致为：

```python
from neo4j import GraphDatabase
with GraphDatabase.driver(uri,auth=(username,password)) as driver:
	with driver.session() as session: #创建会话对象
		result = session.run('xx') #运行cypther语句
		for recodd in result:
    		xxx
```

### Neo4j

Neo4j大致由节点，关系，属性，标签四部分组成。

一个节点可以有若干属性，若干标签。标签用于分类。关系用于连接记录（节点），关系也可以有若干属性。

### Cypher

从官方指导开始：

```cypher
CREATE (ee:Person {name;'Emil',from:'Sweden',kloutScore:99})
```

`CREATE`创建节点，`()`表示一个节点,`ee`表示节点变量,`Person`表示标签,`{}`表示属性。

```cypher
MATCH (ee:Person) WHERE ee.name = 'Emil' RETURN ee;
```

`MATCH`表示匹配，`()`表示节点 `WHERE`表示判断条件，`RETURN`表示返回结果。
