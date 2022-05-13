## 三个概念

1. 数据库（database)
   - 数据库是一个仓库，在仓库中可以存放集合
2. 集合（collection）
   - 集合类似于数组，在集合中可以存放文档
3. 文档（document）
   - 文档是数据中的最小单位，我们存储和操作的内容都是文档

使用insert完成插入操作

操作格式：

- `db.<集合>.insertOne(<JSON对象>)`
- `db.<集合>.inserMany([<JSON 1>,<JSON 2>,<JSON 3>])`

示例：

```js
db.user.insertOne({name: "张三"})
db.user.insertMany([
    {name: "张三"},
    {name: "李四"},
    {name: "王五"}
])
```

## MongoDB的基本操作

在MongoDB中，数据库和集合都不需要我们手动创建，当我们创建文档时，如果文档所在的集合或数据库不存在，她会自动创建数据库和集合！

### 基本指令

`show dbs`：显示当前所有数据库

`show database`：显示当前所有数据库

`use 数据库名`：进入到指定的数据库中，如果该数据库不存在，则会创建数据库

`db`：表示我们当前所处的数据库

`show collections`：显示我们数据库中所有的集合

### CRUD（增删改查）操作

向数据库中插入文档

`db.<集合>.insert(doc)`：向集合中插入文档

向test数据库中的stus集合中插入一个新的学生对象

```
db.stus.insert ( {name：“孙悟空”，age：18，gender：”男“} )
```

`db.<集合名>.find()`：查询集合中的所有文档

### 插入文档

`db.<集合名>.insert()`向集合中插入一个或多个文档

当我们向集合中插入文档时，如果没有给文档指定id属性，则数据库会自动给文档添加id，该属性用来作为文档的唯一标识。

id可以自己指定，如果我们指定了，数据库就不会再添加了,如果自己指定id必须也确保唯一性。

`db.<集合名>.insertOne()`：插入一个文档对象

`db.<集合名>.insertMany()`：插入多个文档对象

```
db.stus.insert({name:"张三",age:28,gender:"男"})
db.stus.find()

db.stus.insert([
    {name:"沙和尚",age:36,gender:"男"},
    {name:"白骨精",age:16,gender:"女"},
    {name:"蜘蛛精",age:14,gender:"女"}
])
```

`$push`：用于向数组中添加一个新的元素

```js
db.user.update({username:"tangseng"},{ $push:{"hobby.movies":"Interstellar"} })
```

`$addToSet`：向数组中添加一个新元素（类似于向set集合中添加，如果数组中已经存在了该元素，则添加失败，因为不可重复）

```js
db.user.update({username:"tangseng"},{$addToSet:{"hobby.movies":"Interstellar"}})
```

### 查询文档

`db.<集合名>.find（）`：用来查询集合中所有符合条件的文档

`find（）`可以接收一个对象作为条件参数，返回值是一个数组

- { }：表示查询该集合内所有文档

- { 属性：值 }：查询属性是指定值的文档

  ```
  db.stus.find( { status: "D" } )
  ```

`db.<集合名>.findOne()`用来查询集合中符合条件的第一个文档，返回的是一个文档。

```
db.stus.findOne({ name: "张三" })
```

`db.stus.find({}).count()`：查询所有结果的数量

```
db.stus.find({name:"张三"})[0]
db.stus.find({}).length()
```

#### 使用查询运算符指定条件

查询集合中所有`status`为`A`或`D`的文档：`$in`

```
db.inventory.find( { status: { $in: [ "A", "D" ] } } )
```

> 注意：
>
> 尽管可以使用 [`$or`](https://www.mongodb.com/docs/manual/reference/operator/query/or/#mongodb-query-op.-or) 运算符来表示此查询，但在对同一字段（属性）执行相等性检查时，请使用 $in 运算符而不是 [`$or`](https://www.mongodb.com/docs/manual/reference/operator/query/or/#mongodb-query-op.-or) 运算符。

该操作对应于以下 SQL 语句：

```
SELECT * FROM inventory WHERE status in ("A", "D")
```

#### 指定条件`AND`

复合查询可以**为集合文档中的多个字段指定条件**。隐式地逻辑连接复合查询的子句，以便**查询选择集合中与所有条件匹配的文档**。

下面的示例检索stus集合中`status`等于`A` **且** `qty<30`<小于 `($lt)` >的所有文档

```
db.stus.find( { status: "A", qty: { $lt: 30 } } )
```

该操作对应于以下 SQL 语句：

```
SELECT * FROM inventory WHERE status = "A" AND qty < 30
```

#### 指定条件`OR`

使用 [`$or`](https://www.mongodb.com/docs/manual/reference/operator/query/or/#mongodb-query-op.-or) 运算符，可以指定一个复合查询，查询使用该逻辑连接的每个子句，以便查询选择stus集合中至少与一个条件匹配的文档

```shell
db.stus.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )
```

该操作对应于以下 SQL 语句：

```
SELECT * FROM stus WHERE status = "A" OR qty < 30
```

#### 指定`AND`以及 `OR`条件

在下面的示例中，复合查询文档选择集合中`status = "A"` **并且** `qty < 30` （小于符`$lt`） *或*`item`以`p`字符开头的所有文档：

```shell
db.inventory.find( {
     status: "A",
     $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]
} )
```

该操作对应于以下 SQL 语句：

```
SELECT * FROM inventory WHERE status = "A" AND ( qty < 30 OR item LIKE "p%")
```

#### 匹配嵌入/嵌套文档

`MongoDB`的文档的属性值也可以是一个文档，当一个文档的属性值是文档时，我们称这个文档为**内嵌文档**

`MongoDB`支持直接通过内嵌文档的属性进行查询，如果要查询内嵌文档可以则可以通过`.`的形式来匹配，且属性名必须使用引号,双引号单引号都可以

```js
// 要查询出来的文档示例
{
     item: "paper",
     qty: 100,
     size: { h: 14, w: 21, uom: "cm"  },
     status: "D" 
}
```

例如，以下查询选择集合`inventory`中的，文档中字段等于`size{ h: 14, w: 21, uom: "cm" }`的所有文档：

```shell
db.inventory.find( { size: { h: 14, w: 21, uom: "cm" } } )
```

整个嵌入文档上的相等匹配要求：**与指定文档（包括字段顺序）*完全*匹配**。例如，以下查询与集合中的任何文档都不匹配：`<value>``inventory`

```
db.inventory.find({ size: { w: 21, h: 14, uom: "cm" } }  )
```

#### 嵌套字段查询

若要对嵌入/嵌套文档中的字段指定查询条件，请使用点表示法（`"field.nestedField"`）

> 注意：使用点表示法进行查询时，字段和嵌套字段必须在引号内。

##### 在嵌套字段上指定相等匹配

选择出嵌套在字段中的size中`uom: "in"`的所有文档

```js
{
     item: "paper",
     qty: 100,
     size: { h: 8.5, w: 11, uom: "in" },
     status: "D" 
}
```

```shell
db.inventory.find( { "size.uom": "in" } )
```

##### 使用查询运算符指定匹配

以下查询对字段中嵌入的字段使用小于运算符（[`$lt`](https://www.mongodb.com/docs/manual/reference/operator/query/lt/#mongodb-query-op.-lt)）

```shell
db.inventory.find( { "size.h": { $lt: 15 } } )
```

##### 指定条件`AND`

以下查询选择嵌套字段`h<15`**且**嵌套字段`uom: "in"` **且**字段`status: "D"`的所有文档

```shell
db.inventory.find( { "size.h": { $lt: 15 }, "size.uom": "in", status: "D" } )
```

#### 匹配数组

下面的示例按**指定的顺序**查询`tags`的字段值为**正好包含**两个元素`red`和`blank` 的数组 的所有文档

```shell
db.inventory.find( { tags: ["red", "blank"] } )
```

相反，如果您希望查找同时包含元素`red`和`blank`  的数组，而**不考虑数组中的顺序或其他元素**，请使用 [`$all`](https://www.mongodb.com/docs/manual/reference/operator/query/all/#mongodb-query-op.-all) 运算符

```shell
db.inventory.find( { tags: { $all: ["red", "blank"] } } )
```

。。。

### 修改文档

`db.<集合名>.update(查询条件，新对象)`

- `update（）`默认情况下会使用新对象来替换旧对象
- `update（）`默认只会修改一个对象

如果需要修改指定的属性，而不是替换，需要使用 “修改操作符” 来完成修改

- `$set`：可以用来修改文档中的指定属性（将在字段不存在时创建该字段）
- `$unset`：可以用来删除文档的指定属性

`db.<集合名>.updateOne('查询对象',)`：修改一个符合条件的文档

```shell
db.inventory.updateOne(
   { item: "paper" },
   {
     $set: { "size.uom": "cm", status: "P" },
     $currentDate: { lastModified: true }
   }
)
```

- 使用[`$set`](https://www.mongodb.com/docs/manual/reference/operator/update/set/#mongodb-update-up.-set)运算符将字段`size.uom`的值更新为`"cm"` ，并将`status`字段的值更新为`"P"`
- 使用[`$currentDate`](https://www.mongodb.com/docs/manual/reference/operator/update/currentDate/#mongodb-update-up.-currentDate)运算符将字段的值更新为当前日期。如果字段不存在，[`$currentDate`](https://www.mongodb.com/docs/manual/reference/operator/update/currentDate/#mongodb-update-up.-currentDate)将创建该字段

`db.<集合名>.updateMany()`：同时修改多个符合条件的文档

```shell
db.inventory.updateMany(
   { "qty": { $lt: 50 } },  查询qty<50
   {
     $set: { "size.uom": "in", status: "P" },
     $currentDate: { lastModified: true }
   }
)
```

`db.<集合名>.replaceOne()`：替换一个符合条件的文档

```js
// 待替换的数据
{ item: "paper", qty: 100, size: { h: 8.5, w: 11, uom: "in" }, status: "D" }
```

```shell
db.inventory.replaceOne(
   { item: "paper" }, // 查询item为paper
   { item: "paper", instock: [ { warehouse: "A", qty: 60 }, { warehouse: "B", qty: 40 } ] } // 替换
)
```

#### 字段顺序

对于写入操作，`MongoDB`保留文档字段的顺序，但以下情况*除外*：

- 该字段始终是文档中的第一个字段。`_id`
- 包括[`重命名`](https://www.mongodb.com/docs/manual/reference/operator/update/rename/#mongodb-update-up.-rename)字段名称的更新可能会导致文档中的字段重新排序。

### 删除文档

`db.<集合名>.remove（）`可以根据条件来删除文档，传递条件的方式和`find（）`一样

- 能删除符合条件的所有文档，默认删除多个
- 如果第二个参数传递一个true，则只会删除一个
- 如果只传递一个`{ }`作为参数，则会删除集合中的所有文档

#### 删除所有文档

```shell
db.inventory.deleteMany({})
```

#### 删除与条件匹配的所有文档

```shell
db.inventory.deleteMany({ status : "A" })
```

#### 仅删除一个与条件匹配的文档

```shell
db.inventory.deleteOne( { status: "D" } )
```

`db.collection.drop（）`：删除集合（如果最后一个集合没了，数据库也没了。。。）

```
db.stus.remove({ age: 28 },true)
db.stus.remove({}) //性能差
db.stus.drop()
db.dropDatabase()
```

一般数据库中的数据都不会删除，所以删除的方法很少调用，一般会在数据中添加一个字段，来表示数据是否被删除

### 练习

```
//添加两万条数据的性能高，尽量少调用系统的方法
var arr=[];
for(var i=1;i<=20000;i++){
    arr.push({num:i});
}
db.user.insert(arr);
```

```shell
//查询numbers中num大于5000的文档
db.unmbers.find({num:{$gt:500}})

//查询numbers中num小于30的文档
db.unmbers.find({num:{$lt:500}})

//查询numbers中num大于40小于50的文档
db.numbers.find({num:{$gt:40,$lt:50}})

//查询numbers前10条的数据
db.numbers.find({num:{$lte:10}})

//limit()设置显示数据的上限
db.numbers.find().limit(10)

//查询numbers中第11条到20条的数据
	//skip()用于跳过指定数量的数据   skip( (页码-1)*每页显示的条数 ).limit(每页显示的条数)
	//MongoDB会自动调整limit()和skip()的位置
db.numbers.find().skip(10).limit(10)
db.numbers.find().limit(10).skip(10)
```

```
//查询工资小于1000或者大于2000的员工
db.emp.find( $or:[ {sal:{$lt:1000}},{sal:{$gt:2500}} ])

//为所有工资小于1000的增加400
db.emp.find({sal:{$lte:1000}},	{$inc:{$sal:400}})
```

### 文档间的关系

#### 一对一（one to one）

夫妻

在`MongoDB`中，可以通过内嵌文档的形式来体现出一对一的关系

```js
db.WifeAndHusband.insert([
    {
        wife:"黄蓉",
        husband:{
            name:"郭靖"
        }
    },
    {
       wife:"潘金莲",
       husband:{
         name:"武大郎"
       }
    }
])
```

#### 一对多（one to many）/ 多对一（many to one）

一对多：父母和孩子、用户和订单、文章和评论，也可以通过内嵌文档的方式来映射一对多的关系（将1的那个属性设置为多的里面的字段）

```js
db.order.insert({
  list:["watermelor"],          
  user_id:ObjectId("5f87b1deda684b252c2fc7a5")
})
var user_id = db.users.findOne({username:"swk"})._id
//查询孙悟空的订单
db.order.find({user_id:user_id})
```

#### 多对多（many to many）：分类和商品，通过内嵌文档的方式

```js
db.teacher.insert([
    // 这里的_id可以不用填，让其自动生成
	{name:"洪七公",_id: xxxa8},
    {name:"黄药师",_id: xxxa9},
    {name:"龟仙人",_id: xxxaa}
])

db.stus.insert([
  {
    name:"郭靖",
    tech_ids:[
       ObjectId("5f87b4b6da684b252c2fc7a8"),
       ObjectId("5f87b4b6da684b252c2fc7a9")
    ]
  },   
  {
    name:"孙悟空",
    tech_ids:[
       ObjectId("5f87b4b6da684b252c2fc7a8"),
       ObjectId("5f87b4b6da684b252c2fc7a9"),
       ObjectId("5f87b4b6da684b252c2fc7aa")
    ]
   }
])
```

### sort和投影

`find（）`查询文档时，默认情况是按照_id的值进行升序排列

`sort（）`可以用来指定文档的排序的规则，需要传递一个对象来指定排序规则，**1表示升序，-1表示降序**

```js
db.users.find({}).sort({ sale: 1 })
db.users.find({}).sort({ sale:1, qq:1 }) // 先指定sale的升序 再qq的降序
```

`limit（）`设置显示数据的上限（每页显示的数量）

`skip（）`用于跳过指定数量的数据（从哪条数据开始显示）

limit、skip、sort可以任意顺序的调用，例子在上面的练习

查询时，我们可以在第二个参数的位置来设置查询结果的**投影**

```js
// 只显示每一条数据的sale属性  1是显示，0是隐藏
db.users.find({},{sale: 1})
// _id默认情况都会显示，如果不想显示 添加 _id: 0
db.users.find({},{sale: 1, _id: 0})
```



## Mongoose模块

Mongoose是一个让我们可以**通过Node来操作MongoDB的模块**。

Mongoose是一个**对象文档模型（ODM）库**，它对Node原生的MongoDB模块进行了进一步的优化封装，并提供了更多的功能。

在大多数情况下，它被用来**把结构化的模式应用到一个MongoDB集合**，并提供了**验证**和**类型转换**等好处。

Mongoose的好处：

- 可以为文档创建一个模式结构（Schema）
- 可以对模型中的 对象/文档 进行验证
- 数据可以通过类型转换 转换为对象模型
- 可以使用中间件来应用业务逻辑挂钩
- 比Node原生的MongoDB驱动更容易

Mongoose中为我们提供了几个新的对象

- Schema（模式对象）：定义约束了数据库中的文档结构
- Model：作为集合中的所有文档的表示，**相当于**MongoDB数据库中的**集合collection**
- Document：表示集合中的具体文档，**相当于集合中的一个具体的文档**

### 基本使用

##### 安装

```bash
npm install mongoose --save
```

##### 引入

```js
const mongoose = require('mongoose')
```

##### 连接数据库

```js
// 格式：mongoose.connect('mongodb://数据库的ip地址:端口号/数据库名)
// 如果端口号是默认端口号（27017），则可以省略不写
mongoose.connect('mongodb://localhost/test');
```

##### 断开数据库连接（一般不用）

MongoDB数据库，一般情况下只需要连接一次，除非项目项目停止服务器关闭，否则连接一般不会断开。

```js
mongoose.disconnect()
```

##### 监听数据库的连接状态

在mongoose对象中，有一个属性叫做`connection`，表示的就是数据库连接，通过监视该属性的状态，可以来监听数据库的连接和断开

```js
let db = mongoose.connection;
db.once('open', function () {
    console.log('数据库连接成功！')
})
db.once('close', function () {
    console.log('数据库断开连接！')
})
```

##### 1.创建Schema（模式）对象

```js
// 将 mongoose.Schema 赋值给一个变量
let Schema = mongoose.Schema;
// 创建 Schema（模式）对象
let stuSchema = new Schema({
    name: String,
    age: Number,
    gender: {
        type: String,
        default: 'female'
    },
    address: String
});
```

##### 2.通过Schema来创建Model（集合）

Model代表的是数据库中的集合，通过Model才能对数据库进行操作

- `mongoose.model(’modelName‘,schema)`
- `modelName` 就是要映射的集合名
- `schema`：要用哪个模式对象来约束创建的集合

```js
let stuModel = mongoose.model('student',stuSchema)
```

> 有了Model之后，就可以对数据库进行增删改查操作了

##### 3.例如插入一个文档

```js
stuModel.create(
    {
        name: '李四',
        age: 16,
        gender: 'male',
        address: '广州'
    }
    , function (err, results) {
        if (err) {
            return console.log(err)
        }
        console.log(results);
    })
```

### Model方法

> 有了 Model 之后，就可以对数据库进行增删改查操作了

#### 添加

`Model.create( doc(s), [callback] )`：用来创建一个或多个文档并添加到数据库中。

参数：

- `doc(s)`：可以是一个文档对象，也可以是一个文档对象的数组。
- `callback`：当操作完成以后调用的回调函数，第一个参数一定是`err`，第二个参数是插入成功的文档。

`Model.insertMany( arr, [callback] )`：用于插入大量文档。

参数：

- `arr`：一个或多个文档对象 组成的 数组
- `callback`：操作完成后的回调函数，第一个参数：`err`，第二个参数，插入成功的数组

#### 查询

1. ##### 查询所有符合条件的文档

   查询返回的是一个**数组**

   ```js
   Model.find( conditions, [projection], [options], [callback] )
   ```

2. ##### 根据文档的`_id`属性查询文档

   第一个参数直接传一个`_id`字符串，只返回一个文档**对象**

   ```js
   Model.findById( conditions, [projection], [options], [callback] )
   ```

3. ##### 查询符合条件的第一个文档

   总会返回一个具体的文档**对象**，不是数组

   Document对象就是该集合Model的一个实例

   ```js
   Model.findOne( conditions, [projection], [options], [callback] )
   ```

参数：

- `conditions`：查询的条件
- `projection`：投影，是不是想显示所有的字段，两种方式
  1. `{name: 1, _id: 0}`，1显示，0隐藏
  2. `"name  -_id"`，直接写属性名显示，加`-`隐藏
- `options`：查询选项（skip、limit）
- `callback`：回调函数，查询结果会通过回调函数返回

示例

```js
stuModel.find({ gender: "male" },"name age -_id", function (err, results) {
    if (err) {
        return console.log(err)
    }
    console.log(results);
})
```

#### 修改

- ##### 修改一个或多个文档

  ```
  Model.update( conditions, doc, [options], [callback] )
  ```

- ##### 修改一个文档

  ```
  Model.updateOne( conditions, doc, [options], [callback] )
  ```

- ##### 修改多个文档

  ```
  Model.updateMany( conditions, doc, [options], [callback] )
  ```

参数

- `conditions`：查询条件
- `doc`：修改后的对象
- `options`：配置参数
- `callback`：回调函数

示例

```js
stuModel.updateOne({ name: "鱼仔" }, { $set: { name: "赵四" } }, function (err,results) {
    if (err) {
        console.log("发生了错误！",err)
    }
    console.log(results)
  /*  {
     acknowledged: true, // 告知已收到
     modifiedCount: 1, // 修改数
     upsertedId: null, // 插入Id
     upsertedCount: 0, // 插入数
     matchedCount: 1 // 匹配数
  } */
})
```

#### 删除

- 仅删除一个与条件匹配的文档

  ```
  Model.deleteOne(conditions,[callback])
  ```

- 删除所有匹配到的文档

  ```
  Model.deleteMany(conditions, [callback])
  ```

参数

- `conditions`：匹配条件
- `callback`：回调函数

#### 统计文档的数量

`Model.count( 匹配条件 ，回调函数)`

```js
stuModel.count({}, function (err, count) {
    if (err) {
        return console.log(err)
    }
    console.log(count)
})
```

### Documen方法

Document和集合中的文档一一对应，Document是Model的实例，通过Model查询到结果都是Document

#### 添加

`document.save([options],[fn])` 添加进数据库

```js
// 利用创建好的 Schema对象 来创建 Model(集合)
let stuModel = mongoose.model('student', stuSchema);
// 创建一个Document
let stu = new stuModel({
    name: "唐僧",
    age: 48,
    gender: 'male',
    address: '大唐'
});
// document的方法
// Model#save([options],[fn]) 添加进数据库
stu.save(function (err) {
    if (!err) console.log('保存成功！');
})
```

#### 修改

```js
stuModel.findOne({ name: "张三" }, function (err, doc) {
    if (!err) {
// 1.doc.update(update,[options],[fn]) 修改对象
       doc.update({ $set: { age: 35 } }, function (err) {
           if (!err) console.log('修改成功！')
       }) 
// 2.直接通过doc.age更改
        doc.age = 35;
        doc.save();
     }    
})
```

#### 删除

```js
{
    ....// 省略其他代码
// doc.remove([fn]) 删除
 doc.remove(function (err) {
      if (!err) console.log('删除成功！')
 }) 
}   
```

#### 其他

```js
stuModel.findOne({ name: "张三" }, function (err, doc) {
  if (!err) {
     // get(name) 获取文档中的指定属性值
      console.log( doc.get("age") );
      console.log( doc.age )
      
      // set(name,value) 设置文档的指定属性值
      doc.set("name", "张老六")
      doc.name = "张三"
      
      // id 获取文档的_id属性
      console.log(doc._id)
      console.log(doc.id)
      
      // toObject() 将document对象转换为一个普通对象，转换后所有的document对象属性该对象都不能再使用
    let doc1 = doc.toObject()
  }    
})
```

### 模块化

#### 新建`tools/conn_mongo.js`用于存放连接数据库的模块

```js
// 引入 mongoose 模块
const mongoose = require('mongoose');

// 连接 MongoDB 数据库
// 格式：mongoose.connect('mongodb://数据库的ip地址:端口号/数据库名)
// 如果端口号是默认端口号（27017），则可以省略不写
mongoose.connect('mongodb://localhost/test');

// 监听 MongoDB 数据库的连接状态
let db = mongoose.connection;
db.once('open', function () {
    console.log('数据库连接成功！')
})
db.once('close', function () {
    console.log('数据库断开连接！')
})
```

在项目入口文件引入连接数据库的文件，让其执行一次

```js
require('./tools/conn_mongo.js');
```

#### 新建`models`文件夹，用于存放Model（模型）

需要以什么模型就在`modles`文件夹创建一个相应的js文件

例如：需要一个`students`模型，就新建`students.js`文件

```js
// 引入 mongoose 模块
const mongoose = require('mongoose');
// 将 mongoose.Schema 赋值给一个变量
let Schema = mongoose.Schema;
// 创建 Schema（模式）对象
let stuSchema = new Schema({
    name: String,
    age: Number,
    gender: {
        type: String,
        default: 'female'
    },
    address: String
});
// 利用创建好的 Schema对象 来创建 Model(集合)
// - `mongoose.model('modelName',schema)`
// - `modelName` 就是要映射的集合名
// - `schema`：要用哪个模式对象来约束创建的集合
let stuModel = mongoose.model('student', stuSchema);
// 向外暴露 Model(模型)
module.exports = stuModel
```

哪里需要通过该Model（模型）操作数据库，引入之后即可使用该模型进行增删改查等操作。

```js
const students = require('../tools/students')
```

