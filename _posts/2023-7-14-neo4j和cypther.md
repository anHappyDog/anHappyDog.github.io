---
title: neo4j和cypther
author: lonelywatch
date: 2023-7-14 22:25 +0800
categories: [SQL]
tags: [SQL,NEO4J,CYPTHER,知识图谱]
---
本来以为neo4j就是个跟mysql这种差不多的DBMS,结果发现用的是不同的cypther语言，还跟啥知识图谱挂钩，突然发现python的课设要求还挺多的，要学pyqt，爬虫(带着点前端)，还要学这个，想做好点还要点网络开发知识。&_&

## Neo4j与Cypther介绍

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

