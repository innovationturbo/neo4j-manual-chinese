18.18.1. Send a Gremlin Script - URL encoded
18.18.2. Load a sample graph
18.18.3. Sort a result using raw Groovy operations
18.18.4. Send a Gremlin Script - JSON encoded with table results
18.18.5. Returning nested pipes
18.18.6. Set script variables
18.18.7. Send a Gremlin Script with variables in a JSON Map
18.18.8. Return paths from a Gremlin script
18.18.9. Send an arbitrary Groovy script - Lucene sorting
18.18.10. Emit a sample graph
18.18.11. HyperEdges - find user roles in groups
18.18.12. Group count
18.18.13. Collect multiple traversal results
18.18.14. Collaborative filtering
18.18.15. Chunking and offsetting in Gremlin
18.18.16. Modify the graph while traversing
18.18.17. Flow algorithms with Gremlin
18.18.18. Script execution errors
Gremlin是一个基于Groovy的图形遍历语言。它提供了一个分厂富有边县里的方式显示脚本遍历一个Neo4j图。
这个插件提供了一个端点发送Gremlin脚本到Neo4j服务器。在数据库服务端执行脚本并返回作为Neo4j节点和关系表示的结果。这使整个REST API类型一致。当返回Neo4j Node,Relationshipo或者Graph表示结果相当冗长。另一个方面，知识放回属性就像在Section 18.18.4, “Send a Gremlin Script - JSON encoded with table results”示例响应适合具体的需求。
警告
Gremlin插件让你执行任意Groovy代码通过高级选项。在托管和开发的环境中，这可能构成安全风险。在这些情况下，可以考虑像 Chapter 15, Cypher Query Language使用声明方法或者编写自己的服务端插件执行有趣的Gremlin脚本或Java例子。查看 Section 10.1, “Server Plugins” 或者将强你服务器安全，查看 Section 24.1, “Securing access to the Neo4j Server”。
小技巧
当从像g.v(0).in()的管道返回节后，确保遍历结果为了不返回管道对象的内容，就像g.v(0).in().iterate().更多说明，查看 Gremlin Troubleshooting
18.18.1. 发送一个已经编码的Gremlin脚本
在这个示例中，脚本作为编码的URL将本发送，图已经被Neo4j自动索引，所以我们可以查找节点上的名称属性。
建立脚本源文件
g.idx('node_auto_index')[[name:'I']].out
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/x-www-form-urlencoded
script=g.idx%28%27node_auto_index%27%29%5B%5Bname%3A%27I%27%5D%5D.out
响应示例
200: OK
Content-Type: application/json
[ {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/1/relationships/out",
  "data" : {
    "name" : "you"
  },
  "traverse" : "http://localhost:7474/db/data/node/1/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/1/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/1/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/1",
  "properties" : "http://localhost:7474/db/data/node/1/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/1/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/1/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/1/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/1/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/1/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/1/relationships/in/{-list|&|types}"
} ]
18.18.2. 载入一个示例图
可以通过Gremlin GraphMLReader导入一个图从一个GraphML文件。下面的脚本从一个URI导入一个小的GraphML文件到Neo4j,在描述图中产生结果。底层数据库被自动索引，查看Section 14.12, “Automatic Indexing” 所以该脚本能够返回通过索引查找的接口返回的节点。
建立脚本源文件
g.clear()
g.loadGraphML('https://raw.github.com/neo4j/gremlin-plugin/master/src/data/graphml1.xml')
g.idx('node_auto_index')[[name:'you']]
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.clear();g.loadGraphML('https://raw.github.com/neo4j/gremlin-plugin/master/src/data/graphml1.xml');g.idx('node_auto_index')[[name:'you']];",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
[ {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/10/relationships/out",
  "data" : {
    "name" : "you"
  },
  "traverse" : "http://localhost:7474/db/data/node/10/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/10/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/10/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/10",
  "properties" : "http://localhost:7474/db/data/node/10/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/10/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/10/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/10/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/10/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/10/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/10/relationships/in/{-list|&|types}"
} ]
18.18.3. 使用原始的Groovy操作排序
下面的脚本返回一个排序的列表的所有节点连接到节点1通过外部关系，按照他们呢的名字属性。
建立脚本源文件
g.idx('node_auto_index')[[name:'I']].out.sort{it.name}
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.idx('node_auto_index')[[name:'I']].out.sort{it.name}",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
[ {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/16/relationships/out",
  "data" : {
    "name" : "him"
  },
  "traverse" : "http://localhost:7474/db/data/node/16/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/16/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/16/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/16",
  "properties" : "http://localhost:7474/db/data/node/16/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/16/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/16/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/16/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/16/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/16/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/16/relationships/in/{-list|&|types}"
}, {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/15/relationships/out",
  "data" : {
    "name" : "you"
  },
  "traverse" : "http://localhost:7474/db/data/node/15/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/15/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/15/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/15",
  "properties" : "http://localhost:7474/db/data/node/15/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/15/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/15/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/15/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/15/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/15/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/15/relationships/in/{-list|&|types}"
} ]
18.18.4. 发送一个带有表结果的Gremlin脚本JSON编码
发送一个被编码的脚本JSON，设置有效的内容类型文件头。在这个例子中，找到所有的东西，像我的朋友喜欢，和返回一个我的朋友们的名字的表列表，和他们的名字就像带有两列的表，副略第三命名表两的步骤。记住，一切都在Gremlin是一个迭代其-为了填充结果表t,通过带有iterate()管道迭代。
生成脚本源代码
t= new Table()
g.v(23).as('I').out('know').as('friend').out('like').as('likes').table(t,['friend','likes']){it.name}{it.name}.iterate()
t
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "t= new Table();g.v(23).as('I').out('know').as('friend').out('like').as('likes').table(t,['friend','likes']){it.name}{it.name}.iterate();t;",
  "params" : {
  }
}

响应示例
200: OK
Content-Type: application/json
{
  "columns" : [ "friend", "likes" ],
  "data" : [ [ "Joe", "cats" ]
}
18.18.5 返回嵌套管道
原始脚本
g.v(27).as('I').out('know').as('friend').out('like').as('likes').table(new Table()){it.name}{it.name}.cap
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.v(27).as('I').out('know').as('friend').out('like').as('likes').table(new Table()){it.name}{it.name}.cap;",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
[ {
  "data" : [ [ "I", "Joe", "cats" ], [ "I", "Joe", "dogs" ] ],
  "columns" : [ "I", "friend", "likes" ]
} ]
18.18.6. 设置脚本变量
为服务器上的Gremlin脚本引擎在绑定中设置变量，你可以包含一个带有一个表示JSON map变量的字符串params参数设置为初始值。
原始脚本
meaning_of_life
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "meaning_of_life",
  "params" : {
    "meaning_of_life" : 42.0
  }
}
响应示例
200: OK
Content-Type: application/json
42.0
18.18.7. 在一个JSON Map中发送一个带有变量的Gremlin脚本
发送一个Gremlin脚本，作为JSON有效载荷和额外的参数。
原始脚本
g.v(me).out
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.v(me).out",
  "params" : {
    "me" : "6"
  }
}

响应示例
200: OK
Content-Type: application/json
[ {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/5/relationships/out",
  "data" : {
    "name" : "you"
  },
  "traverse" : "http://localhost:7474/db/data/node/5/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/5/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/5/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/5",
  "properties" : "http://localhost:7474/db/data/node/5/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/5/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/5/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/5/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/5/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/5/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/5/relationships/in/{-list|&|types}"
} ]
18.18.8. 从一个Gremlin脚本返回路径
下面的脚本返回路径。
Gremlin的路径有从开始管道著称的路径。服务器返回的JSON内容作为一个嵌套列表。
原始脚本源代码
g.v(20).out.name.paths
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.v(20).out.name.paths",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
[ [ {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/20/relationships/out",
  "data" : {
    "name" : "I"
  },
  "traverse" : "http://localhost:7474/db/data/node/20/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/20/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/20/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/20",
  "properties" : "http://localhost:7474/db/data/node/20/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/20/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/20/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/20/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/20/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/20/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/20/relationships/in/{-list|&|types}"
}, {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/18/relationships/out",
  "data" : {
    "name" : "you"
  },
  "traverse" : "http://localhost:7474/db/data/node/18/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/18/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/18/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/18",
  "properties" : "http://localhost:7474/db/data/node/18/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/18/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/18/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/18/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/18/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/18/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/18/relationships/in/{-list|&|types}"
}, "you" ], [ {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/20/relationships/out",
  "data" : {
    "name" : "I"
  },
  "traverse" : "http://localhost:7474/db/data/node/20/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/20/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/20/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/20",
  "properties" : "http://localhost:7474/db/data/node/20/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/20/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/20/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/20/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/20/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/20/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/20/relationships/in/{-list|&|types}"
}, {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/19/relationships/out",
  "data" : {
    "name" : "him"
  },
  "traverse" : "http://localhost:7474/db/data/node/19/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/19/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/19/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/19",
  "properties" : "http://localhost:7474/db/data/node/19/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/19/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/19/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/19/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/19/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/19/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/19/relationships/in/{-list|&|types}"
}, "him" ] ]
18.18.9. 发送一个任意的Groovy脚本-进行Lucene排序
这个例子演示，你通过Groovy运行嵌入了具有完全访问所有服务器的的API的服务。下面的示例在数据库中创建节点都通过Blueprints和Neo4j API所以节点通过本机Neo4j索引API构造一个订制Lucene排序和搜索返回一个Neo4j IdexHits结果迭代器。
原始脚本源文件
'******** Additional imports *********'
import org.neo4j.graphdb.index.*
import org.neo4j.graphdb.*
import org.neo4j.index.lucene.*
import org.apache.lucene.search.*
 
'**** Blueprints API methods on the injected Neo4jGraph at variable g ****'
meVertex = g.addVertex([name:'me'])
meNode = meVertex.getRawVertex()
 
'*** get the Neo4j raw instance ***'
neo4j = g.getRawGraph()
 
 
'******** Neo4j API methods: *********'
tx = neo4j.beginTx()
 youNode = neo4j.createNode()
 youNode.setProperty('name','you')
 youNode.createRelationshipTo(meNode,DynamicRelationshipType.withName('knows'))
 
'*** index using Neo4j APIs ***'
 idxManager = neo4j.index()
 personIndex = idxManager.forNodes('persons')
 personIndex.add(meNode,'name',meNode.getProperty('name'))
 personIndex.add(youNode,'name',youNode.getProperty('name'))
tx.success()
tx.finish()
 
 
'*** Prepare a custom Lucene query context with Neo4j API ***'
query = new QueryContext( 'name:*' ).sort( new Sort(new SortField( 'name',SortField.STRING, true ) ) )
results = personIndex.query( query )
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "'******** Additional imports *********';import org.neo4j.graphdb.index.*;import org.neo4j.graphdb.*;import org.neo4j.index.lucene.*;import org.apache.lucene.search.*;;'**** Blueprints API methods on the injected Neo4jGraph at variable g ****';meVertex = g.addVertex([name:'me']);meNode = meVertex.getRawVertex();;'*** get the Neo4j raw instance ***';neo4j = g.getRawGraph();;;'******** Neo4j API methods: *********';tx = neo4j.beginTx(); youNode = neo4j.createNode(); youNode.setProperty('name','you'); youNode.createRelationshipTo(meNode,DynamicRelationshipType.withName('knows'));;'*** index using Neo4j APIs ***'; idxManager = neo4j.index(); personIndex = idxManager.forNodes('persons'); personIndex.add(meNode,'name',meNode.getProperty('name')); personIndex.add(youNode,'name',youNode.getProperty('name'));tx.success();tx.finish();;;'*** Prepare a custom Lucene query context with Neo4j API ***';query = new QueryContext( 'name:*' ).sort( new Sort(new SortField( 'name',SortField.STRING, true ) ) );results = personIndex.query( query );",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
[ {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/30/relationships/out",
  "data" : {
    "name" : "you"
  },
  "traverse" : "http://localhost:7474/db/data/node/30/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/30/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/30/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/30",
  "properties" : "http://localhost:7474/db/data/node/30/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/30/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/30/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/30/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/30/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/30/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/30/relationships/in/{-list|&|types}"
}, {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/29/relationships/out",
  "data" : {
    "name" : "me"
  },
  "traverse" : "http://localhost:7474/db/data/node/29/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/29/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/29/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/29",
  "properties" : "http://localhost:7474/db/data/node/29/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/29/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/29/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/29/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/29/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/29/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/29/relationships/in/{-list|&|types}"
} ]
18.18.10. 发出一个示例图
可以通过发出适当的字符串，就可以到处一个图。
原始脚本文件
writer = new GraphMLWriter(g)
out = new java.io.ByteArrayOutputStream()
writer.outputGraph(out)
result = out.toString()
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "writer = new GraphMLWriter(g);out = new java.io.ByteArrayOutputStream();writer.outputGraph(out);result = out.toString();",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
"<?xml version=\"1.0\" ?><graphml xmlns=\"http://graphml.graphdrawing.org/xmlns\"><key id=\"name\" for=\"node\" attr.name=\"name\" attr.type=\"string\"></key><graph id=\"G\" edgedefault=\"directed\"><node id=\"12\"><data key=\"name\">you</data></node><node id=\"13\"><data key=\"name\">him</data></node><node id=\"14\"><data key=\"name\">I</data></node><edge id=\"6\" source=\"14\" target=\"12\" label=\"know\"></edge><edge id=\"7\" source=\"14\" target=\"13\" label=\"know\"></edge></graph></graphml>"
18.18.11. 超边-在组中找到用户角色
想象一个用户参与不同的群体。一个组可以有不同的角色，和一个用户可以属于不同组，除了会员，他还可以在不同的组有不同的角色。该联合的用户，一个用户组，一个角色都可以称为超边。然而它可以很容易在一个属性图建模一个节点n-ary捕捉这一关系，如下面U1G2R1节点。找出一个特定用户组的角色（第2组），下面的脚本可以遍历这个超边并提供答案。
原始脚本
g.v(37).out('hasRoleInGroup').as('hyperedge').out('hasGroup').filter{it.name=='Group2'}.back('hyperedge').out('hasRole').name
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.v(37).out('hasRoleInGroup').as('hyperedge').out('hasGroup').filter{it.name=='Group2'}.back('hyperedge').out('hasRole').name",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
[ "Role1" ]
18.18.12. 组计数
这个例子展示了在Gremlin中的一组计数，比如计数的不同关系类型连接到一些开始节点。比如计数的不同关系类型连接到一些开始节点。结果被收集到一个变量，然后返回。
原始脚本
m = [:]
g.v(41).bothE().label.groupCount(m).iterate()
m
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "m = [:];g.v(41).bothE().label.groupCount(m).iterate();m",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
{
  "knows" : 2,
  "likes" : 1
}
18.18.13. 搜集多个遍历结果
多个遍历可以合并到一个结果，使用分开和合并在一个lazy fasion。管道。
原始脚本
g.idx('node_auto_index')[[name:'Peter']].copySplit(_().out('knows'), _().in('likes')).fairMerge.name
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.idx('node_auto_index')[[name:'Peter']].copySplit(_().out('knows'), _().in('likes')).fairMerge.name",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
[ "Ian", "Marie" ]

18.18.14. 协作筛选

这个例子掩饰了届本的协作筛选，订购一个遍历发送计数和对照组对象不是有趣的最终结果。
在这里，我们正在朋友的朋友，但不是joes的朋友，同样可以引用到图表的用户和其他人喜欢的事情。
原始脚本
x=[]
fof=[:]
g.v(63).out('knows').aggregate(x).out('knows').except(x).groupCount(fof).iterate()
fof.sort{a,b -> b.value <=> a.value}
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "x=[];fof=[:];g.v(63).out('knows').aggregate(x).out('knows').except(x).groupCount(fof).iterate();fof.sort{a,b -> b.value <=> a.value}",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
{
  "v[61]" : 2,
  "v[60]" : 1,
  "v[62]" : 1
}
18.18.15. 在Gremlin归类和位移
原始脚本
g.v(51).out('knows').filter{it.name == 'Sara'}[0..100]
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.v(51).out('knows').filter{it.name == 'Sara'}[0..100]",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
[ {
  "outgoing_relationships" : "http://localhost:7474/db/data/node/49/relationships/out",
  "data" : {
    "name" : "Sara"
  },
  "traverse" : "http://localhost:7474/db/data/node/49/traverse/{returnType}",
  "all_typed_relationships" : "http://localhost:7474/db/data/node/49/relationships/all/{-list|&|types}",
  "property" : "http://localhost:7474/db/data/node/49/properties/{key}",
  "self" : "http://localhost:7474/db/data/node/49",
  "properties" : "http://localhost:7474/db/data/node/49/properties",
  "outgoing_typed_relationships" : "http://localhost:7474/db/data/node/49/relationships/out/{-list|&|types}",
  "incoming_relationships" : "http://localhost:7474/db/data/node/49/relationships/in",
  "extensions" : {
  },
  "create_relationship" : "http://localhost:7474/db/data/node/49/relationships",
  "paged_traverse" : "http://localhost:7474/db/data/node/49/paged/traverse/{returnType}{?pageSize,leaseTime}",
  "all_relationships" : "http://localhost:7474/db/data/node/49/relationships/all",
  "incoming_typed_relationships" : "http://localhost:7474/db/data/node/49/relationships/in/{-list|&|types}"
} ]
18.18.16. 当遍历的时候修改图
这个例子掩饰了在Gremlin的一组计数，比如计数的不同类型的连接到一些开始节点。结果被收集到一个变量，然后返回。

原始脚本
g.v(44).bothE.each{g.removeEdge(it)}
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.v(44).bothE.each{g.removeEdge(it)}",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
[ ]

18.18.17. 带有Gremlin的跟随算法
这是一个基本的存根示例实现跟随算法在例如Flow Networks带有一个基于管道和脚本，这里，source和sink之间使用了capacity属性在关系上作为跟随函数的基础和在计算的时候修改图。
原始脚本
source=g.v(72)
sink=g.v(73)
maxFlow = 0
source.outE.inV.loop(2){!it.object.equals(sink)}.paths.each{flow = it.capacity.min()
 maxFlow += flow
it.findAll{it.capacity}.each{it.capacity -= flow}}
maxFlow
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "source=g.v(72);sink=g.v(73);maxFlow = 0;source.outE.inV.loop(2){!it.object.equals(sink)}.paths.each{flow = it.capacity.min(); maxFlow += flow;it.findAll{it.capacity}.each{it.capacity -= flow}};maxFlow",
  "params" : {
  }
}
响应示例
200: OK
Content-Type: application/json
4
18.18.18. 脚本执行错误
脚本错误将返回一个导致HTTP错误的响应代码。
请求示例
POST http://localhost:7474/db/data/ext/GremlinPlugin/graphdb/execute_script
Accept: application/json
Content-Type: application/json
{
  "script" : "g.addVertex([name:{}])"
}
响应示例
400: Bad Request
Content-Type: application/json
{
  "message" : "javax.script.ScriptException: java.lang.IllegalArgumentException: Unknown property type on: Script25$_run_closure1@c4179ba, class Script25$_run_closure1",
  "exception" : "BadInputException",
  "stacktrace" : [ "org.neo4j.server.plugin.gremlin.GremlinPlugin.executeScript(GremlinPlugin.java:88)", "java.lang.reflect.Method.invoke(Method.java:597)", "org.neo4j.server.plugins.PluginMethod.invoke(PluginMethod.java:57)", "org.neo4j.server.plugins.PluginManager.invoke(PluginManager.java:168)", "org.neo4j.server.rest.web.ExtensionService.invokeGraphDatabaseExtension(ExtensionService.java:300)", "org.neo4j.server.rest.web.ExtensionService.invokeGraphDatabaseExtension(ExtensionService.java:122)", "java.lang.reflect.Method.invoke(Method.java:597)" ]
}
















