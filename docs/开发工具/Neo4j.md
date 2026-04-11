# Neo4j 图数据库

Neo4j 是一个高性能的 NoSQL 图数据库，使用图结构存储数据，非常适合处理复杂的关系数据。

## 安装

### Windows

1. 下载 Neo4j Desktop
2. 安装并启动
3. 创建新的数据库项目

### Docker 安装

```bash
docker run \
    --name neo4j \
    -p7474:7474 -p7687:7687 \
    -d \
    -v $HOME/neo4j/data:/data \
    -v $HOME/neo4j/logs:/logs \
    -v $HOME/neo4j/import:/var/lib/neo4j/import \
    -v $HOME/neo4j/plugins:/plugins \
    --env NEO4J_AUTH=neo4j/password \
    neo4j:latest
```

访问：http://localhost:7474

## 基本概念

- **节点（Node）**：图中的实体
- **关系（Relationship）**：节点之间的连接
- **属性（Property）**：节点和关系的键值对
- **标签（Label）**：节点的分类

## Cypher 查询语言

### 创建节点

```cypher
// 创建单个节点
CREATE (n:Person {name: '张三', age: 30})

// 创建多个节点
CREATE (n1:Person {name: '李四', age: 25}),
       (n2:Person {name: '王五', age: 28})

// 返回创建的节点
CREATE (n:Person {name: '赵六', age: 35})
RETURN n
```

### 创建关系

```cypher
// 创建节点和关系
CREATE (a:Person {name: '张三'})-[:KNOWS]->(b:Person {name: '李四'})

// 为已存在的节点创建关系
MATCH (a:Person {name: '张三'}), (b:Person {name: '王五'})
CREATE (a)-[:KNOWS {since: 2020}]->(b)

// 创建双向关系
MATCH (a:Person {name: '张三'}), (b:Person {name: '李四'})
CREATE (a)-[:FRIENDS]-(b)
```

### 查询节点

```cypher
// 查询所有节点
MATCH (n) RETURN n

// 按标签查询
MATCH (n:Person) RETURN n

// 按属性查询
MATCH (n:Person {name: '张三'}) RETURN n

// 条件查询
MATCH (n:Person)
WHERE n.age > 25
RETURN n

// 限制返回数量
MATCH (n:Person)
RETURN n
LIMIT 10
```

### 查询关系

```cypher
// 查询所有关系
MATCH (a)-[r]->(b)
RETURN a, r, b

// 查询特定关系
MATCH (a:Person)-[:KNOWS]->(b:Person)
RETURN a.name, b.name

// 查询多层关系
MATCH (a:Person {name: '张三'})-[:KNOWS*1..3]->(b:Person)
RETURN b.name

// 查询最短路径
MATCH path = shortestPath(
  (a:Person {name: '张三'})-[:KNOWS*]-(b:Person {name: '王五'})
)
RETURN path
```

### 更新数据

```cypher
// 更新属性
MATCH (n:Person {name: '张三'})
SET n.age = 31
RETURN n

// 添加属性
MATCH (n:Person {name: '张三'})
SET n.email = 'zhangsan@example.com'
RETURN n

// 添加标签
MATCH (n:Person {name: '张三'})
SET n:Employee
RETURN n

// 删除属性
MATCH (n:Person {name: '张三'})
REMOVE n.email
RETURN n
```

### 删除数据

```cypher
// 删除节点（必须先删除关系）
MATCH (n:Person {name: '张三'})
DETACH DELETE n

// 删除关系
MATCH (a:Person {name: '张三'})-[r:KNOWS]->(b:Person {name: '李四'})
DELETE r

// 删除所有数据
MATCH (n)
DETACH DELETE n
```

## Python 连接 Neo4j

### 安装驱动

```bash
pip install neo4j
```

### 基本使用

```python
from neo4j import GraphDatabase

class Neo4jConnection:
    def __init__(self, uri, user, password):
        self.driver = GraphDatabase.driver(uri, auth=(user, password))
    
    def close(self):
        self.driver.close()
    
    def query(self, query, parameters=None):
        with self.driver.session() as session:
            result = session.run(query, parameters)
            return [record for record in result]

# 连接数据库
conn = Neo4jConnection("bolt://localhost:7687", "neo4j", "password")

# 创建节点
query = """
CREATE (n:Person {name: $name, age: $age})
RETURN n
"""
conn.query(query, parameters={"name": "张三", "age": 30})

# 查询节点
query = """
MATCH (n:Person)
WHERE n.age > $age
RETURN n.name, n.age
"""
results = conn.query(query, parameters={"age": 25})
for record in results:
    print(record)

# 关闭连接
conn.close()
```

## 索引和约束

### 创建索引

```cypher
// 创建索引
CREATE INDEX person_name FOR (n:Person) ON (n.name)

// 查看索引
SHOW INDEXES

// 删除索引
DROP INDEX person_name
```

### 创建约束

```cypher
// 唯一性约束
CREATE CONSTRAINT person_email FOR (n:Person) REQUIRE n.email IS UNIQUE

// 存在性约束
CREATE CONSTRAINT person_name FOR (n:Person) REQUIRE n.name IS NOT NULL

// 查看约束
SHOW CONSTRAINTS

// 删除约束
DROP CONSTRAINT person_email
```

## 聚合函数

```cypher
// 计数
MATCH (n:Person)
RETURN count(n)

// 求和
MATCH (n:Person)
RETURN sum(n.age)

// 平均值
MATCH (n:Person)
RETURN avg(n.age)

// 最大值和最小值
MATCH (n:Person)
RETURN max(n.age), min(n.age)

// 分组统计
MATCH (n:Person)
RETURN n.city, count(n) as count
ORDER BY count DESC
```

## 高级查询

### 可选匹配

```cypher
// OPTIONAL MATCH（类似 SQL 的 LEFT JOIN）
MATCH (a:Person {name: '张三'})
OPTIONAL MATCH (a)-[:KNOWS]->(b:Person)
RETURN a.name, b.name
```

### 条件表达式

```cypher
// CASE 表达式
MATCH (n:Person)
RETURN n.name,
  CASE
    WHEN n.age < 20 THEN '青少年'
    WHEN n.age < 60 THEN '成年人'
    ELSE '老年人'
  END as age_group
```

### 集合操作

```cypher
// 收集为列表
MATCH (a:Person {name: '张三'})-[:KNOWS]->(b:Person)
RETURN a.name, collect(b.name) as friends

// 列表过滤
MATCH (n:Person)
WHERE n.age IN [25, 30, 35]
RETURN n
```

## 导入数据

### CSV 导入

```cypher
// 导入节点
LOAD CSV WITH HEADERS FROM 'file:///persons.csv' AS row
CREATE (n:Person {name: row.name, age: toInteger(row.age)})

// 导入关系
LOAD CSV WITH HEADERS FROM 'file:///relationships.csv' AS row
MATCH (a:Person {name: row.from}), (b:Person {name: row.to})
CREATE (a)-[:KNOWS]->(b)
```

## 性能优化

1. **使用索引**：为常查询的属性创建索引
2. **使用参数化查询**：避免 Cypher 注入
3. **限制返回结果**：使用 LIMIT
4. **使用 PROFILE**：分析查询性能
5. **批量操作**：使用 UNWIND 批量处理

```cypher
// 使用 PROFILE 分析
PROFILE
MATCH (n:Person)
WHERE n.age > 25
RETURN n

// 批量创建
UNWIND [{name: '张三', age: 30}, {name: '李四', age: 25}] AS person
CREATE (n:Person)
SET n = person
```

## 应用场景

- 社交网络分析
- 推荐系统
- 知识图谱
- 欺诈检测
- 网络拓扑
- 权限管理
- 路径规划
