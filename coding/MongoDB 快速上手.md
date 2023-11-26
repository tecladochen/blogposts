> _MongoDB 是一款十分流行的 NoSQL 开源文档型数据库。_

MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。它的主要特点有：

1. 结构简单，使用类似 JSON 格式的 BSON 来存储数据。
2. 支持强大的查询语言，符合面向对象设计原则。
3. 提供大多数关系型数据库才有的表查询、二级索引、事务等功能。
## 理论知识介绍
在学习如何使用 MongoDB 前，需要先了解一些理论概念。
MongoDB 在概念模型上参考了 SQL 数据库，可以对照着理解，但并非完全相同。

| **MongoDB 概念** | **SQL 概念** |
| --- | --- |
| database | database |
| collection | table |
| document | row |
| field | column |

在 MongoDB 中，数据库（database） 与 SQL 中的概念一致，一个 database 包含多个集合（collection），集合类似于 SQL 中的表概念，在集合中存放文档（document），文档类似于 SQL 中的行记录，一个文档中保护多个字段（field），字段类似于 SQL 中的列。
在 MongoDB 中，文档是使用类 JSON 的 BSON（Binary JSON）格式存储的，它与 JSON 的结构类似，十分容易解析和阅读，此外还增加了一些数据类型来增强存储功能。一个典型的文档如下：
```json
{
  "_id": ObjectId("654ded2d7de559981db87ddc"),
  "name" : { "first" : "John", "last" : "Backus" },
  "contribs" : [ "Fortran", "ALGOL", "Backus-Naur Form", "FP" ],
  "awards" : [
    {
      "award" : "W.W. McDowell Award",
      "year" : 1967,
      "by" : "IEEE Computer Society"
    }, {
      "award" : "Draper Prize",
      "year" : 1993,
      "by" : "National Academy of Engineering"
    }
  ]
}
```
## MongoDB 语法
了解完基本概念，就可以学习一下基本操作，MongoDB 的操作语法很简单。
使用 use 命令切换到指定数据库，如果数据库不存在则创建数据库；db 命令显示当前所在数据库；show dbs 命令可以查看所有数据库。
```sql
use test
> switched to db test
db
> test
show dbs
> admin   0.000GB
> config  0.000GB
> local   0.000GB
```
在 MongoDB 中删除数据库必须先切换到要删除的数据库，调用自己的删除数据库函数.
```sql
use test
> switched to db test
db.dropDatabase()
```
在数据库中创建集合和删除集合也很容易；使用 show collections 可以查看数据库中所以的集合。
```sql
db.createCollection("users")
show collections
> users
db.users.drop()
```
在集合中增加文档使用 insertOne 和 insertMany 俩个方法。
```sql
db.users.insertOne({name: "monica", age: 18}})
db.users.insertMany([
  {name: "monica", age: 18},
  {name: "Abel", age: 20},
  {name: "Robin", age: 23}
])
```
在集合中查询数据使用 find 方法，find 方法可以实现各种条件查询。
```sql
db.users.find({})  // 查询所以数据
db.users.find({name: "Robin"})  // 条件查询
db.users.find({name: "Robin", age: 23})  // 多条件查询
db.users.find({age: {$lt: 20, $gt: 30}})  // 查询(20,30)年龄区间内的用户
db.users.find({age: {$in: [18, 20]}})  // 查询年龄是 18 或 20 的用户
db.users.find({$or: [{name: "Robin"}, {age: 23}]})
// 查询名字是 Robin 或者年龄是 23 的用户
```
需要更新文档内容时使用 updateOne 和 updateMany 俩个方法。
```sql
db.users.updateOne({name: "Abel"}, {age: 21})
// 更新名字叫 Abel 的用户年龄为 21
db.users.updateMany({age: {$gt: 20}}, {adult: true})
// 给所有年龄大于 20 的用户加上 adult 字段
```
需要删除文档时使用 deleteOne 和 deleteMany 俩个方法。
```sql
db.users.deleteOne({name: "Monica"})  // 删除指定的一条文档
db.users.deleteMany({age: {$gt: 20}})  // 条件删除多条文档
db.users.deleteMany({})  // 删除所有文档
```
这些对于数据的基本操作很容易掌握，MongoDB 的操作语法不仅能够在命令行中使用，而且在各种语言环境中也一样适用，下面以 Node.js 环境为例：
先在项目中安装 mongodb 官方驱动包。
```bash
$ npm install mongodb
```
新建一个 app.js 文件，引入 MongoClient 构造方法，创建客户端实例对象 client，需要先连接上数据库然后操作语法是基本一样的。
```javascript
import { MongoClient } from "mongodb"
const url = 'mongodb://localhost:27017'
const client = new MongoClient(url)

async function main() {
    await client.connect()
    console.log('Connected successfully to server')
    const db = client.db('test')
    const users = db.collection('users')
    const result = await users.insertMany([{
        name: "monica",
        age: 18
    },
    {
        name: "Abel",
        age: 23
    }
    ])
    return result
}

main()
  .then(console.log)
  .catch(console.error)
  .finally(() => client.close())
```
> 注意：当使用 find 查询数据时返回的值需要使用 toArray 方法转换成数组方便后续操作。

```javascript
const findResult = await users.find({}).toArray()
console.log('Found documents =>', findResult)
```
## Mongoose 使用
MongoDB 官方提供了在 Node 环境下接口驱动，而 Mongoose 就是基于官方驱动进行进一步封装的对象文档库，它把结构化的模式应用到 MongoDB 的集合和文档中，提供了更多的功能。
![清新配色流程图标准样式模板 (4).png](https://cdn.nlark.com/yuque/0/2023/png/12428058/1699939076153-62736b84-b5ef-4819-a801-428b57369989.png#averageHue=%23392d2d&clientId=u5b8fea24-996b-4&from=paste&height=119&id=u1b623588&originHeight=119&originWidth=567&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9108&status=done&style=none&taskId=u5cca17d3-ed48-41cd-add2-04e45cf9c5b&title=&width=567)
关于 Mongoose 中的一些基本概念：
Schema ：用于定义数据库中的文档结构和类型约束。
Model ：相当于 MongoDB 中的集合，创建 Model 需要指定 Schema。
Document ：表示集合中的具体某个文档，由 Model 实例化生成。
Mongoose 在使用上与原生语法还是有一定的区别，先来了解一下如何用 Mongoose 连接上数据库。
首先在项目中安装 Mongoose 包。
```bash
$ npm install mongoose --save
```
然后新建一个 app.js 文件，引入 mongoose 模块，使用异步方法连接上数据库，需要先创建一个 Schema 声明文档结构和类型约束，通过 Schema 创建出一个 Model，使用 Model 实例化出具体的文档，最后调用 save 方法会将文档保存到数据库中。
Mongoose 会自动为每个 Model 创建出一个复数的集合，然后把文档保存到集合中。
```javascript
import mongoose from 'mongoose'

async function main() {
    await mongoose.connect('mongodb://localhost:27017/test')
    const kittySchema = new mongoose.Schema({
        name: String
    })
    
    const Kitten = mongoose.model('Kitten', kittySchema)
    const silence = new Kitten({ name: 'silence' })
    return await slience.save()
}

main()
    .then(console.log)
    .catch(console.error)
```
关于使用 Mongoose 的增删改查基本操作与原生语法有一定差别，但是不难理解。
在新增文档时，有 save ，create 和 insertMany 等方法可以使用。
```javascript
// 1. save
const silence = new Kitten({name: 'silence'})
await silence.save()

// 2. create
await Kitten.create([{name: 'silence'}, {name: 'fudge'}])

// 3. insertMany
await Kitten.insertMany([{name: 'silence'}, {name: 'fudge'}])
```
在查询文档时，有 find 系列方法可以使用，除了查询全部文档外，其他查询需要调用 exec 方法来执行查询。
```javascript
// find all documents
await Kitten.find({})

// 1. find
await Kitten.find({name: 'silence'}).exec()

// 2. findOne
await Kitten.findOne({name: 'silence'}).exec()

// 3. findById
await Kitten.findById(id).exec()
```
当需要更新文档时，Mongoose 提供和 MongoDB 类似的 updateOne 和 updateMany 之外，还有 findOneAndUpdate 和 findByIdAndUpdate 等其他方法。
```javascript
// 1. updateOne
await Kitten.updateOne({name: 'silence'}, {age: 5})

// 2. updateMany
await Kitten.updateMany({name: /silence$/}, {Named: true})

// 3. findOneAndUpdate
const query = {name: 'silence'}
await Kitten.findOneAndUpdate(query, {age: 5})

// 4. findByIdAndUpdate
await Kitten.findByIdAndUpdate(id, {age: 5})
```
当需要删除文档时，也有 delete 系列方法可以使用，使用 Mongoose 操作文档时都以 Model 为中心，Model 中封装了大量的方法供方便使用。
```javascript
// 1. deleteOne
await Kitten.deleteOne({name: 'silence'})

// 2. deleteMany
await Kitten.deleteMany({name: /silence$/})

// 3. findOneAndDelete
await Kitten.findOneAndDelete({name: 'silence'})

// 4. findByIdAndDelete
await Kitten.findByIdAndDelete(id)
```
要熟练掌握 Mongoose 的基本使用和高级功能，需要参考官方文档继续学习。

