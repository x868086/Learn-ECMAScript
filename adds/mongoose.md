
# Mongoose
## 1. mongodb connect mongodb数据库连接
```js
    var express = require('express')
    var router = express.Router()
    var mongoose=require('mongoose')
    var Goods=require('../models/goods.js') //加载goods实体model模块

    /*model模块定义的Goods，goods.js文件
        let mongoose=require('mongoose');
        let Schema=mongoose.Schema;
        let productSchema=new Schema({
            "productId" : String,
            "productName" : String,
            "salePrice" : Number,
            "productImage" : String,
            "productUrl" : String
        })

        let Good=mongoose.model("Good",productSchema)

        module.exports=Good
    */

        mongoose.connect('mongodb://root:123456@127.0.0.1:27017/shop');

        mongoose.connection.on("connected",()=>{
            console.log("mongodb connected")
        })

        mongoose.connection.on("error",()=>{
            console.log("mongodb connected error")
        })

        mongoose.connection.once("disconnected",()=>{
            console.log("mongodb connected offline")
        })
```


## 2. mongoose model query，查询实体
```js
//modelQuery是queryBuilder，调用exec方法执行查询
    let modelQuery=Goods.find({}).skip(skips).limit(pageSize);
        modelQuery.sort({'salePrice':sortParam})
        //查询结果实例query使用exec方法调用回调函数
        modelQuery.exec((err,doc)=>{
            err? res.json({
                'status':0,
                'msg':err.message
            }): res.json({
                'status':1,
                'count':doc.length,
                'msg':'response done',
                'result':doc
            })
        })
```


## 3. modelQuery实体，根据查询字符串在后端实现数据分页返回
```js
    let sortParam=req.param('sortParam');
    let pageNum=Number(req.param('pageNum'));
    let pageSize=Number(req.param('pageSize'));
    console.log(req)
    //1.1根据页数和每页的数量计算要跳过的条数
    let skips=(pageNum-1) * pageSize

    //1.2创建查询结果实例并在查询结果中分页
    let modelQuery=Goods.find({}).skip(skips).limit(pageSize);
 ```
 
 ## 4. mongoose model定义，包含填充选项
 ```js
let mongoose = require('mongoose')
let Schema = mongoose.Schema
let planSchema = new Schema({
  'planid': String,
  'planname': String,
  'plantype': String,
  'plandate': String,
  'belong': String,
  'plandescribe': {
    'gift': Array,
    'productname': String,
    'remark': String
  },
  'customerslot': [{type: Schema.Types.ObjectId, ref: 'customer'}] //customerslot是填充字段的插槽，数据来源是customer model
})

let planModel = mongoose.model('plan', planSchema, 'planlist')

module.exports = planModel
 ```
 ```js
let mongoose = require('mongoose')
let Schema = mongoose.Schema
let customerSchema = new Schema({
  'userid': String,
  'customername': String,
  'tel': String,
  'productresult': String,
  'broadtype': String,
  'ywinfo': String,
  'enddate': String,
  'age': String,
  'planid': String,
  'projectname': String,
  'belong': String,
  'collector': String,
  'planslot': {type: Schema.Types.ObjectId, ref: 'plan'} //planslot是填充字段的插槽，数据来源是plan model
})
let customerModel = mongoose.model('customer', customerSchema, 'customerinfo')

module.exports = customerModel
 ```
----