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

一个经典的返回所有节点的匹配语句：

```cypher
MATCH (n) RETURN n;
```



```cypher
(:Person {name;'Rosa'})-[:LIVES_IN]->(:Place {city:'Berlin',country:'DE'}) 
                                             
```

在知识图谱中，信息就在图谱中，而非构成图谱的规定，上述语句表明了一段信息,如之前所说，`()`表示一个节点，`:`之后表示该节点的标签，而`{}`表示该节点的属性，`-[:LIVES_IN]->`表示两节点间名字为LIVES_IN(`:`表示名字)的关系，这么解释的话，可以翻译为：一个name为Rosa的Person LIVES_IN 一个city为Berlin，country为DE的Place。如果使用CREATE的话，就可以创建这么一段信息。

`DELETE`用于节点，DETACH DELETE 删除附加在删除节点上的关系（如果没有DETACH遇到该情况，删除会终止），使用MATCH和DELETE可以删除特定内容。

CREATE并不会将产生的同样的节点合并，使用 `MERGE`来实现增加并合并（事实上，需要记录中的内容完全存在于原来的图，只存在部分是不会合并的）。在neo4j中也存在约束和主键，使用

```cypher
CREATE CONSTRAINT no_duplicate_cities FOR (p:PLACE) REQUIRE (p.country,p.city) IS NODE KEY
```

非常遗憾的是需要企业版才能使用。设置约束后，只需要找到（未找到则创建）需要的节点，然后创建关系:

```cypher
MERGE (p:PERSON {name:'dd'})
MERGE (d:PLACE {city:'Berlin',country:'DE'})
MERGE (p)-[:LIVES_IN{since:1999}]->(d)
```

这样就会将p与已存在的d建立起关系。这种写法并不需要建立约束。

---

上面已经介绍了Cypher的增加，删除，查找操作，关于修改操作，只需要找出相应的节点或关系，通过`SET`和`REMOVE`关键字修改相应值即可,WHERE则是判断条件(也可以全都写到MATCH中去)。

```cypher
MATCH (p.Person) WHERE p.name ='Rosa' SET p.dob = 111111;
MATCH (p.Person) WHERE p.name ='Rosa' REMOVE p.dob;
```

关于WHERE的判断，有很多判断情况，如：

- `<>`不等于
- `STARTS WITH` 前缀为
- `CONTAINS`包含
- `ENDS WITH`后缀为
- `NOT`否定
- `IN`在之中,`AND`并且

在关系中，`-[xxx*2..2]->`表示路径为2~2，表示路径长度区间。

---

上述操作为`Graphs local`操作，下面为`Graphs global`操作：

cypher也支持别名以及函数调用，排序等。

```cypher
MATCH (p:PLACE)<-[L:LIVES_IN]-(:PERSON) RETURN p as place , count(l) AS rels ORDER BY rels DESC
```

`AS`使用别名，`ORDER BY`表示按照某内容排序，`DESC`表示描述内容。

关于`APOC`:APOC是一个neo4j中很受欢迎的函数库,最好别自己重复造轮子。

`EXPLAIN`和`PROFILE`
