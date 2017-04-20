# MongoDB
>根据RUNOOB.COM MongoDB教程整理
>网址：http://www.runoob.com/mongodb/mongodb-tutorial.html

**目录**

[TOC]

## MongoDB 安装
Windows平台：  
MongoDB预编译二进制包下载地址：https://www.mongodb.com/download-center#community  
**创建数据目录**
MongoDB将数据目录存储在 db 目录下。但是这个数据目录不会主动创建，我们在安装完成后需要创建它。请注意，数据目录应该放在根目录下（(如： C:\ 或者 D:\ 等 )。
## MongoDb 概念解析
|SQL术语|MongoDB术语|说明|
|--|--|--|
|database|database|数据库|
|table|collection|数据库表/集合|
|row|document|数据记录行/文档|
|column|field|数据字段/域|
|index|index|索引|
|table joins||表连接，MongoDB不支持|
|primary key|peimary key|主键，MongoDB自动将_id字段设置为主键
#### 数据库
一个mongodb中可以建立多个数据库。
MongoDB的默认数据库为"db"，该数据库存储在data目录中。
MongoDB的单个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，不同的数据库也放置在不同的文件中。
- "show dbs" 命令可以显示所有数据的列表。
- 执行 "db" 命令可以显示当前数据库对象或集合。
- 运行"use"命令，可以连接到一个指定的数据库。

数据库也通过名字来标识。数据库名可以是满足以下条件的任意UTF-8字符串。
- 不能是空字符串（"")。
- 不得含有' '（空格)、.、$、/、\和\0 (空宇符)。
- 应全部小写。
- 最多64字节。

有一些数据库名是保留的，可以直接访问这些有特殊作用的数据库。
- admin： 从权限的角度来看，这是"root"数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。
- local: 这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
- config: 当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息

#### 文档
文档是一组键值(key-value)对(即BSON)。MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 MongoDB 非常突出的特点。
一个简单的文档例子如下：
```js
{"site":"www.runoob.com", "name":"菜鸟教程"}
```
需要注意的是：
1. 文档中的键/值对是有序的。
2. 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
3. MongoDB区分类型和大小写。
4. MongoDB的文档不能有重复的键。
5. 文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。

文档键命名规范：
- 键不能含有\0 (空字符)。这个字符用来表示键的结尾。
- .和$有特别的意义，只有在特定环境下才能使用。
- 以下划线"_"开头的键是保留的(不是严格要求的)。

#### 集合
集合就是 MongoDB 文档组，类似于 RDBMS （关系数据库管理系统：Relational Database Management System)中的表格。
集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。
比如，我们可以将以下不同数据结构的文档插入到集合中：
```js
{"site":"www.baidu.com"}
{"site":"www.google.com","name":"Google"}
{"site":"www.runoob.com","name":"菜鸟教程","num":5}
```
当第一个文档插入时，集合就会被创建。
合法的集合名:
- 集合名不能是空字符串""。
- 集合名不能含有\0字符（空字符)，这个字符表示集合名的结尾。
- 集合名不能以"system."开头，这是为系统集合保留的前缀。
- 用户创建的集合名字不能含有保留字符。有些驱动程序的确支持在集合名里面包含，这是因为某些系统生成的集合中包含该字符。除非你要访问这种系统创建的集合，否则千万不要在名字里出现$。

**capped collections**
Capped collections 就是固定大小的collection。
它有很高的性能以及队列过期的特性(过期按照插入的顺序). 有点和 "RRD" 概念类似。
Capped collections是高性能自动的维护对象的插入顺序。它非常适合类似记录日志的功能 和标准的collection不同，你必须要显式的创建一个capped collection， 指定一个collection的大小，单位是字节。collection的数据存储空间值提前分配的。
要注意的是指定的存储大小包含了数据库的头信息。
```js
db.createCollection("mycoll", {capped:true, size:100000})
```
- 在capped collection中，你能添加新的对象。
- 能进行更新，然而，对象不会增加存储空间。如果增加，更新就会失败 。
- 数据库不允许进行删除。使用drop()方法删除collection所有的行。
- 注意: 删除之后，你必须显式的重新创建这个collection。
- 在32bit机器中，capped collection最大存储为1e9( 1X109)个字节。

#### 元数据
数据库的信息是存储在集合中。它们使用了系统的命名空间：
dbname.system.*
在MongoDB数据库中名字空间 <dbname>.system.* 是包含多种系统信息的特殊集合(Collection)，如下:

|集合命名空间|描述
|--|--
|dbname.system.namespace  |列出所有名字空间。
|dbname.system.indexes	  |列出所有索引。
|dbname.system.profile	  |包含数据库概要(profile)信息。
|dbname.system.users	  |列出所有可访问数据库的用户。
|dbname.local.sources	  |包含复制对端（slave）的服务器信息和状态。
对于修改系统集合中的对象有如下限制。
- 在{{system.indexes}}插入数据，可以创建索引。但除此之外该表信息是不可变的(特殊的drop index命令将自动更新相关信息)。
- {{system.users}}是可修改的。 {{system.profile}}是可删除的。

#### MongoDB 数据类型
下表为MongoDB中常用的几种数据类型。

|数据类型	|描述
|--|--
|String	|字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。
|Integer	|整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。
|Boolean	|布尔值。用于存储布尔值（真/假）。
|Double	|双精度浮点值。用于存储浮点值。
|Min/Max |keys	将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。
|Arrays	|用于将数组或列表或多个值存储为一个键。
|Timestamp	|时间戳。记录文档修改或添加的具体时间。
|Object	|用于内嵌文档。
|Null	|用于创建空值。
|Symbol	|符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。
|Date	|日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。
|Object ID	|对象 ID。用于创建文档的 ID。
|Binary Data	|二进制数据。用于存储二进制数据。
|Code	|代码类型。用于在文档中存储 JavaScript 代码。
|Regular expression	|正则表达式类型。用于存储正则表达式。
## MongoDB 连接
#### 启动MongoDB服务
使用命令行在安装目录的bin目录下执行mongod.exe即可
#### 连接MongoDB
可以使用MongoDB shell来连接MongoDB服务，在bin目录下执行mongo.exe即可进入shell模式
标准URI连接语法：
```sql
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]
```
- mongodb:// 这是固定的格式，必须要指定。
- username:password@ 可选项，如果设置，在连接数据库服务器之后，驱动都会尝试登陆这个数据库
- host1 必须的指定至少一个host, host1 是这个URI唯一要填写的。它指定了要连接服务器的地址。如果要连接复制集，请指定多个主机地址。
- portX 可选的指定端口，如果不填，默认为27017
- /database 如果指定username:password@，连接并验证登陆指定数据库。若不指定，默认打开admin数据库。
- ?options 是连接选项。如果不使用/database，则前面需要加上/。所有连接选项都是键值对name=value，键值对之间通过&或;（分号）隔开

## MongoDB 创建数据库
**语法**
MongoDB 创建数据库的语法格式如下：
```sql
use DATABASE_NAME
```
如果数据库不存在，则创建数据库，否则切换到指定数据库。
**实例**
以下实例我们创建了数据库 runoob:
```sql
> use runoob
switched to db runoob
```
使用db查看当前所在数据库名称
```sql
> db
runoob
>
```
如果你想查看所有数据库，可以使用 show dbs 命令：
```sql
> show dbs
local  0.078GB
test   0.078GB
>
```
可以看到，我们刚创建的数据库 runoob 并不在数据库的列表中， 要显示它，我们需要向 runoob 数据库插入一些数据。
```sql
> db.runoob.insert({"name":"菜鸟教程"})
WriteResult({ "nInserted" : 1 })
> show dbs
local   0.078GB
runoob  0.078GB
test    0.078GB
>
```
MongoDB 中默认的数据库为 test，如果你没有创建新的数据库，集合将存放在 test 数据库中。
## MongoDB 删除数据库
**语法**
MongoDB 删除数据库的语法格式如下：
```sql
db.dropDatabase()
```
**删除集合**
集合删除语法格式如下：
```sql
db.collection.drop()
```
以下实例删除了 runoob 数据库中的集合 site：
```sql
> use runoob
switched to db runoob
> show tables
site
> db.site.drop()
true
> show tables
>
```
## MongoDB 插入文档
文档的数据结构和JSON基本一样。
所有存储在集合中的数据都是BSON格式。
BSON是一种类json的一种二进制形式的存储格式,简称Binary JSON。
**语法**
MongoDB 使用 insert() 或 save() 方法向集合中插入文档
```sql
db.COLLECTION_NAME.insert(document)
```
**实例**
以下文档可以存储在 MongoDB 的 runoob 数据库 的 col 集合中：
```sql
>use runoob
switched to db runoob
>db.col.insert({title: 'MongoDB 教程',
    description: 'MongoDB 是一个 Nosql 数据库',
    by: '菜鸟教程',
    url: 'http://www.runoob.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100
})
```
以上实例中 col 是我们的集合名，如果该集合不在该数据库中， MongoDB 会自动创建该集合并插入文档。
查看已插入文档：
```sql
> db.col.find()
{ "_id" : ObjectId("56064886ade2f21f36b03134"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
>
```
我们也可以将数据定义为一个变量，如下所示：
```sql
> document=({title: 'MongoDB 教程',
    description: 'MongoDB 是一个 Nosql 数据库',
    by: '菜鸟教程',
    url: 'http://www.runoob.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100
});
```
执行后显示结果如下：
```sql
{
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "菜鸟教程",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
```
执行插入操作：
```sql
> db.col.insert(document)
WriteResult({ "nInserted" : 1 })
>
```
插入文档你也可以使用 db.col.save(document) 命令。如果不指定 _id 字段 save() 方法类似于 insert() 方法。如果指定 _id 字段，则会更新该 _id 的数据。
## MongoDB 更新文档
MongoDB 使用 update() 和 save() 方法来更新集合中的文档。
#### update()方法
update() 方法用于更新已存在的文档。语法格式如下：
```sql
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```
参数说明：
- query : update的查询条件，类似sql update查询内where后面的。
- update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
- upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
- multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
- writeConcern :可选，抛出异常的级别。
**实例**
我们在集合 col 中插入如下数据：
```sql
>db.col.insert({
    title: 'MongoDB 教程',
    description: 'MongoDB 是一个 Nosql 数据库',
    by: '菜鸟教程',
    url: 'http://www.runoob.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100
})
```
接着我们通过 update() 方法来更新标题(title):
```sql
>db.col.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })   # 输出信息
> db.col.find().pretty()
{
        "_id" : ObjectId("56064f89ade2f21f36b03136"),
        "title" : "MongoDB",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "菜鸟教程",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
>
```
可以看到标题(title)由原来的 "MongoDB 教程" 更新为了 "MongoDB"。
以上语句只会修改第一条发现的文档，如果你要修改多条相同的文档，则需要设置 multi 参数为 true。
```sql
>db.col.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}},{multi:true})
```

#### svae()方法
save() 方法通过传入的文档来替换已有文档。语法格式如下：
```sql
db.collection.save(
   <document>,
   {
     writeConcern: <document>
   }
)
```

参数说明：
- document : 文档数据。
- writeConcern :可选，抛出异常的级别。

**实例**
以下实例中我们替换了 _id 为 56064f89ade2f21f36b03136 的文档数据：
```sql
>db.col.save({
	"_id" : ObjectId("56064f89ade2f21f36b03136"),
    "title" : "MongoDB",
    "description" : "MongoDB 是一个 Nosql 数据库",
    "by" : "Runoob",
    "url" : "http://www.runoob.com",
    "tags" : [
            "mongodb",
            "NoSQL"
    ],
    "likes" : 110
})
```
替换成功后，我们可以通过 find() 命令来查看替换后的数据
```sql
>db.col.find().pretty()
{
        "_id" : ObjectId("56064f89ade2f21f36b03136"),
        "title" : "MongoDB",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "Runoob",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "NoSQL"
        ],
        "likes" : 110
}
>
```

#### 更多实例
```sql
# 只更新第一条记录：
db.col.update( { "count" : { $gt : 1 } } , { $set : { "test2" : "OK"} } );
# 全部更新：
db.col.update( { "count" : { $gt : 3 } } , { $set : { "test2" : "OK"} },false,true );
# 只添加第一条：
db.col.update( { "count" : { $gt : 4 } } , { $set : { "test5" : "OK"} },true,false );
# 全部添加加进去:
db.col.update( { "count" : { $gt : 5 } } , { $set : { "test5" : "OK"} },true,true );
# 全部更新：
db.col.update( { "count" : { $gt : 15 } } , { $inc : { "count" : 1} },false,true );
# 只更新第一条记录：
db.col.update( { "count" : { $gt : 10 } } , { $inc : { "count" : 1} },false,false );
```

## MongoDB 删除文档
MongoDB remove()函数是用来移除集合中的数据。
MongoDB数据更新可以使用update()函数。在执行remove()函数前先执行find()命令来判断执行的条件是否正确，这是一个比较好的习惯。
**语法**
remove() 方法的基本语法格式如下所示：
```sql
db.collection.remove(
   <query>,
   <justOne>
)
```
如果你的 MongoDB 是 2.6 版本以后的，语法格式如下：
```sql
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```
参数说明：
- query :（可选）删除的文档的条件。
- justOne : （可选）如果设为 true 或 1，则只删除一个文档。
- writeConcern :（可选）抛出异常的级别。

**实例**
以下文档我们执行两次插入操作：
```sql
>db.col.insert({title: 'MongoDB 教程',
    description: 'MongoDB 是一个 Nosql 数据库',
    by: '菜鸟教程',
    url: 'http://www.runoob.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100
})
```
使用 find() 函数查询数据：
```sql
> db.col.find()
{ "_id" : ObjectId("56066169ade2f21f36b03137"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
{ "_id" : ObjectId("5606616dade2f21f36b03138"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
```
接下来我们移除 title 为 'MongoDB 教程' 的文档：
```sql
>db.col.remove({'title':'MongoDB 教程'})
WriteResult({ "nRemoved" : 2 })           # 删除了两条数据
>db.col.find()
……                                        # 没有数据
```
如果你只想删除第一条找到的记录可以设置 justOne 为 1，如下所示：
```sql
>db.COLLECTION_NAME.remove(DELETION_CRITERIA,1)
```
如果你想删除所有数据，可以使用以下方式（类似常规 SQL 的 truncate 命令）：
```sql
>db.col.remove({})
>db.col.find()
>
```

## MongoDB 查询文档
MongoDB 查询文档使用 find() 方法。
find() 方法以非结构化的方式来显示所有文档。
**语法**
MongoDB 查询数据的语法格式如下：
```sql
db.collection.find(query, projection)
```
- query ：可选，使用查询操作符指定查询条件
- projection ：可选，使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）。

如果你需要以易读的方式来读取数据，可以使用 pretty() 方法，语法格式如下：
```sql
>db.col.find().pretty()
```
pretty() 方法以格式化的方式来显示所有文档。
**实例**
以下实例我们查询了集合 col 中的数据：
```sql
> db.col.find().pretty()
{
        "_id" : ObjectId("56063f17ade2f21f36b03133"),
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "菜鸟教程",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
```
除了 find() 方法之外，还有一个 findOne() 方法，它只返回一个文档。
#### MongoDB 与 RDBMS Where 语句比较
|操作	|格式	|范例	|RDBMS中的类似语句
|--|--|--|--
|等于	|{<key>:<value>}	|db.col.find({"by":"菜鸟教程"}).pretty()	|where by = '菜鸟教程'
|小于	|{<key>:{$lt:<value>}}	|db.col.find({"likes":{$lt:50}}).pretty()	|where likes < 50
|小于或等于	|{<key>:{$lte:<value>}}	|db.col.find({"likes":{$lte:50}}).pretty()	|where likes <= 50
|大于	|{<key>:{$gt:<value>}}	|db.col.find({"likes":{$gt:50}}).pretty()	|where likes > 50
|大于或等于	|{<key>:{$gte:<value>}}	|db.col.find({"likes":{$gte:50}}).pretty()	|where likes >= 50
|不等于	|{<key>:{$ne:<value>}}	|db.col.find({"likes":{$ne:50}}).pretty()	|where likes != 50
#### MongoDB AND 条件
MongoDB 的 find() 方法可以传入多个键(key)，每个键(key)以逗号隔开，及常规 SQL 的 AND 条件。
语法格式如下：
```sql
>db.col.find({key1:value1, key2:value2}).pretty()
```

**实例**
以下实例通过 by 和 title 键来查询 菜鸟教程 中 MongoDB 教程 的数据
```sql
> db.col.find({"by":"菜鸟教程", "title":"MongoDB 教程"}).pretty()
{
        "_id" : ObjectId("56063f17ade2f21f36b03133"),
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "菜鸟教程",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
```
以上实例中类似于 WHERE 语句：WHERE by='菜鸟教程' AND title='MongoDB 教程'
#### MongoDB OR 条件
MongoDB OR 条件语句使用了关键字 $or,语法格式如下：
```sql
>db.col.find(
   {
      $or: [
	     {key1: value1}, {key2:value2}
      ]
   }
).pretty()
```
**实例**
以下实例中，我们演示了查询键 by 值为 菜鸟教程 或键 title 值为 MongoDB 教程 的文档。
```sql
>db.col.find({$or:[{"by":"菜鸟教程"},{"title": "MongoDB 教程"}]}).pretty()
{
        "_id" : ObjectId("56063f17ade2f21f36b03133"),
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "菜鸟教程",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
>
```
#### AND 和 OR 联合使用
以下实例演示了 AND 和 OR 联合使用，类似常规 SQL 语句为： 'where likes>50 AND (by = '菜鸟教程' OR title = 'MongoDB 教程')'
```sql
>db.col.find({"likes": {$gt:50}, $or: [{"by": "菜鸟教程"},{"title": "MongoDB 教程"}]}).pretty()
{
        "_id" : ObjectId("56063f17ade2f21f36b03133"),
        "title" : "MongoDB 教程",
        "description" : "MongoDB 是一个 Nosql 数据库",
        "by" : "菜鸟教程",
        "url" : "http://www.runoob.com",
        "tags" : [
                "mongodb",
                "database",
                "NoSQL"
        ],
        "likes" : 100
}
```
## MongoDB 条件操作符
**描述**
条件操作符用于比较两个表达式并从mongoDB集合中获取数据。
MongoDB中条件操作符有：
- (>) 大于 - $gt (greater than)
- (<) 小于 - $lt (less than)
- (>=) 大于等于 - $gte (gt equal)
- (<=) 小于等于 - $lte (lt equal)
- (!=) 不等于 - &ne (not eaual)

## MongoDB $type操作符
$type操作符是基于BSON类型来检索集合中匹配的数据类型，并返回结果。
MongoDB 中可以使用的类型如下表所示：

|类型	|数字
|--|--
|Double	|1
|String	|2
|Object	|3
|Array	|4
|Binary data	|5
|Undefined	|6(已废弃。)
|Object id	|7
|Boolean	|8
|Date	|9
|Null	|10
|Regular Expression	|11
|JavaScript	|13
|Symbol	|14
|JavaScript (with scope)	|15
|32-bit integer	|16
|Timestamp	|17
|64-bit integer	|18
|Min key	|25 (Query with -1.)
|Max key	|127

## MongoDB Limit与Skip()方法
如果你需要在MongoDB中读取指定数量的数据记录，可以使用MongoDB的Limit方法，limit()方法接受一个数字参数，该参数指定从MongoDB中读取的记录条数。
#### MongoDB Limit() 方法
**语法**
limit()方法基本语法如下所示：
```sql
>db.COLLECTION_NAME.find().limit(NUMBER)
```

**实例**
集合 col 中的数据如下：
```json
{ "_id" : ObjectId("56066542ade2f21f36b0313a"), "title" : "PHP 教程", "description" : "PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言。", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "php" ], "likes" : 200 }
{ "_id" : ObjectId("56066549ade2f21f36b0313b"), "title" : "Java 教程", "description" : "Java 是由Sun Microsystems公司于1995年5月推出的高级程序设计语言。", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "java" ], "likes" : 150 }
{ "_id" : ObjectId("5606654fade2f21f36b0313c"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "mongodb" ], "likes" : 100 }
```
以上实例为显示查询文档中的两条记录：
```sql
# db.col.find({},{"title":1,_id:0}).limit(2)方法
# 第一个 {} 放 where 条件，为空表示返回集合中所有文档。
# 第二个 {} 指定那些列显示和不显示 （0表示不显示 1表示显示)。
> db.col.find({},{"title":1,_id:0}).limit(2)
{ "title" : "PHP 教程" }
{ "title" : "Java 教程" }
>
```
注：如果你们没有指定limit()方法中的参数则显示集合中的所有数据。
#### MongoDB Skip() 方法
我们除了可以使用limit()方法来读取指定数量的数据外，还可以使用skip()方法来跳过指定数量的数据，skip方法同样接受一个数字参数作为跳过的记录条数。
**语法**
skip() 方法脚本语法格式如下：
```sql
>db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)
```
**实例**
以上实例只会显示第二条文档数据
```sql
>db.col.find({},{"title":1,_id:0}).limit(1).skip(1)
{ "title" : "Java 教程" }
>
```
注:skip()方法默认参数为 0 。
## MongoDB 排序
#### MongoDB sort()方法
在MongoDB中使用使用sort()方法对数据进行排序，sort()方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而-1是用于降序排列。
**语法**
sort()方法基本语法如下所示：
```sql
>db.COLLECTION_NAME.find().sort({KEY:1})
```
实例
col 集合中的数据如下：
```json
{ "_id" : ObjectId("56066542ade2f21f36b0313a"), "title" : "PHP 教程", "description" : "PHP 是一种创建动态交互性站点的强有力的服务器端脚本语言。", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "php" ], "likes" : 200 }
{ "_id" : ObjectId("56066549ade2f21f36b0313b"), "title" : "Java 教程", "description" : "Java 是由Sun Microsystems公司于1995年5月推出的高级程序设计语言。", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "java" ], "likes" : 150 }
{ "_id" : ObjectId("5606654fade2f21f36b0313c"), "title" : "MongoDB 教程", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "mongodb" ], "likes" : 100 }
```
以下实例演示了 col 集合中的数据按字段 likes 的降序排列：
```sql
>db.col.find({},{"title":1,_id:0}).sort({"likes":-1})
{ "title" : "PHP 教程" }
{ "title" : "Java 教程" }
{ "title" : "MongoDB 教程" }
>
```
注： 如果没有指定sort()方法的排序方式，默认按照文档的升序排列。
## MongoDB 索引
索引通常能够极大的提高查询的效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。
这种扫描全集合的查询效率是非常低的，特别在处理大量的数据时，查询可以要花费几十秒甚至几分钟，这对网站的性能是非常致命的。
索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库表中一列或多列的值进行排序的一种结构
####　ensureIndex() 方法
MongoDB使用 ensureIndex() 方法来创建索引。
**语法**
ensureIndex()方法基本语法格式如下所示：
```sql
>db.COLLECTION_NAME.ensureIndex({KEY:1})
```
语法中 Key 值为你要创建的索引字段，1为指定按升序创建索引，如果你想按降序来创建索引指定为-1即可。
**实例**
```sql
>db.col.ensureIndex({"title":1})
```
ensureIndex() 方法中你也可以设置使用多个字段创建索引（关系型数据库中称作复合索引）。
```sql
>db.col.ensureIndex({"title":1,"description":-1})
```
ensureIndex() 接收可选参数，可选参数列表如下：

|Parameter	|Type	|Description
|--
|background	|Boolean	|建索引过程会阻塞其它数据库操作，background可指定以后台方式创建索引，即增加 "background" 可选参数。 "background" 默认值为false。
|unique	|Boolean	|建立的索引是否唯一。指定为true创建唯一索引。默认值为false.
|name	|string	|索引的名称。如果未指定，MongoDB的通过连接索引的字段名和排序顺序生成一个索引名称。
|dropDups	|Boolean	|在建立唯一索引时是否删除重复记录,指定 true 创建唯一索引。默认值为 false.
|sparse	|Boolean	|对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为true的话，在索引字段中不会查询出不包含对应字段的文档.。默认值为 false.
|expireAfterSeconds	|integer	|指定一个以秒为单位的数值，完成 TTL设定，设定集合的生存时间。
|v	|index version	|索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本。
|weights	|document	|索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重。
|default_language	|string	|对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语
|language_override	|string	|对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的language，默认值为 language.
**实例**
在后台创建索引：
```sql
db.values.ensureIndex({open: 1, close: 1}, {background: true})
```
通过在创建索引时加background:true 的选项，让创建工作在后台执行























