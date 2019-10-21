# MongoDB CMD 纯命令操作
(测试数据库：testdb   集合：person , animal   属性：name , age)
### mongo入门命令
 1.   #### 查看当前的数据库  
      show dbs    
 2.   #### 选库(如果没有就创建库，db.createCollection('animal')才是真的创建)  
      use testdb
 3.   #### 查看当前库下的集合  
 	  show collections     
 4.   #### 显示集合数据  
 	  db.person.find()    
 5.   #### 创建集合  
 	  db.createCollection('animal')  
 6.   #### 集合允许隐式创建，即插入文档数据  
 	  db.animal.insert({'name':'dog',age:2})  
 7.   #### 删除集合  
 	  db.animal.drop()  
 8.   #### 删除数据库  
 	  db.dropDatabase()

### 基本操作增删改查
 1.   #### 增加单篇文档  
      db.person.insertOne({'name':'zhangsan','age':20})   
 2.   #### 增加单个文档,并指定_id  
      db.person.insertOne({'_id':8,'name':'zhangsan','age':30})  
 3.   #### 3. 增加多个文档  
      db.person.insertMany([{'name':'lisi','age':25},{'name':'wangwu','age':26}])   
 4.   #### 删除文档  
      - 1.删除单篇文档 
        - db.person.deleteOne({'name':'lisi'}) 
         
      - 2.删除多篇文档 
        - db.person.deleteMany({'name':'lisi'})   
 5.   #### 修改文档  
      - 1.修改单篇文档 
        - db.person.updateOne({'name':'lisi'},{$set:{'age':30}})   
          如果有age那列没有,将文档增加一列，如果有就修改    
        - db.person.updateOne({'name':'lisi'},{$set:{'age':30}},{upsert:true})   
          如果有name='lisi’的文档,将被修改,如果没有,将添加此新文档    
        - db.person.updateOne({'name':'lisi'},{$rename:{'age':'address'}})   
          $rename 重命名某个列    
        - db.person.updateOne({'name':'lisi'},{$unset:{'age':''}})   
          $unset 删除某个列     
         
      - 2.修改多篇文档 
        - db.person.updateMany({'name':'lisi'},{$set:{'age':40}})  
        
### 查询文档
 1.   #### 查询所有  
      db.person.find()    
 2.   #### 查询结果显示name ，address 列   
      db.person.find({},{'name':1,'address':1})   
 3.   #### 查询name = lisi 的文档  
      db.person.find({'name':'lisi'})  
      
### 查询表达式
 1.   #### $eq 等于    
      db.person.find({'name':{$eq:'lisi'}}) 
 2.   #### $ne 不等    
      db.person.find({'name':{$ne:'lisi'}})    
 3.   #### $gt 大于   
      db.person.find({'age':{$gt:22}})   
 4.   #### $gte 大于等于    
      db.person.find({'age':{$gte:20}})  
 5.   #### $lt 小于  
      db.person.find({'age':{$lt:22}})  
 6.   #### $lte 小于等于   
      db.person.find({'age':{$lte:20}})  
 7.   #### $in 在其中   
      db.person.find({'age':{$in:[20,25]}})  
      db.person.find({'name':{$in:['zhangsan'，'lisi']}})  
 8.   #### $nin 不在其中   
      db.person.find({'age':{$nin:[20,25]}})  
      db.person.find({'name':{$nin:['lisi']}})  
 9.   #### $all 列是一个数组,且至少包含 v1,v2值   
      db.person.insert({'name':'lisi','city':['gd','gx']})  
      db.person.find({'city':{$all:['gd','gx']}})  
 10.  #### $exists 含有字段   
      db.person.find({'city':{ $exists:1}})  
 11.  #### $nor 所有条件都不满足的文档为真返回   
      db.person.find({$nor:[{age:20},{age:25}]})  
 12.  #### 用正则表达式查询 以”li”开头的name   
      db.person.find({'name':/li*/})  
 13.  #### 用正则表达式查询 以”li”开头的name   
      db.person.find({"$where" : "this.age>20"}).pretty()  
      
### 游标操作 cursor
 1.   #### 查询游标所有 while 循环 
      var cursor = db.person.find()
      while(cursor.hasNext()){printjson(cursor.next());}   
 2.   #### 查询游标所有 for 循环 
      for(var cursor=db.person.find(), doc=true;cursor.hasNext();) {printjson(cursor.next());}   
 3.   #### 允许我们自定义回调函数来逐个处理每个单元.cursor.forEach(回调函数) 
      var getname = function(obj) {print(obj.name)}   
      var cursor = db.person.find()  
      cursor.forEach(getname)  
 4.   #### 跳过前2行,查询下2行
      var cursor = db.person.find().skip(2).limit(2);   
      for(doc=true;cursor.hasNext();) { printjson(cursor.next());}   
      
 5.   #### 通过cursor一次性得到所有数据, 并返回数组
      var cursor = db.person.find()   
      printjson(cursor.toArray())   
      printjson(cursor.toArray()[2])   
      
### 索引操作
 1.   #### 查看集合服务器等讯息 
      db.person.find().explain()  
 2.   #### 查看索引 
      db.person.getIndexes()   
 3.   #### 创建一个索引 
      db.person.ensureIndex({age:-1})     
 4.   #### 创建唯－索引 unique
      db.person.ensureIndex({'name':1},{unique:true}); 
 5.   #### 删除一个索引 
      db.person.dropIndex({age:-1})   
 6.   #### 删除所有索引 
      db.person.dropIndexes() 
 7.   #### 创建子文档索引 
      db.person.insert({'name':'lisi','address':{'city':'hn'}})
      db.person.ensureIndex({'address.city':1})   
 8.   #### 删除子文档索引 
      db.person.dropIndex({'address.city':1})   
 9.   #### 创建一个稀疏索引 sparse  
      db.person.ensureIndex({'name':1},{sparse:true})
 10.  #### 创建一个哈希索引 hashed 
      db.person.ensureIndex({'name':'hashed'})  
      
### group操作(不可以作用分布型集群)
 1.   #### 查询每个栏目下的number数量 
      db.person.insertMany([{'name':'lisi','age':25,'number':10},{'name':'lisi','age':26,'number':20}])
      db.person.group(  
      {  
      key:{'number':1},  
      cond:{},  
      reduce:function(curr,result) {  
          result.cnt += 1;  
      },  
      initial:{cnt:0}  
      }  
      )  
 2.   #### 查询每个栏目下的number数量 
      db.person.insertMany([{'name':'lisi','age':25,'number':10},{'name':'lisi','age':26,'number':20}])
      db.person.group(  
      {  
      key:{'name':1},  
      cond:{},  
      reduce:function(curr,result) {  
          result.cnt += 1;  
      },  
      initial:{cnt:0}  
      }  
      )  
 3.   #### 查询每个栏目下age 大于 25 的number数量 
      db.person.insertMany([{'name':'lisi','age':25,'number':10},{'name':'lisi','age':26,'number':20}])
      db.person.group(  
      {  
      key:{'name':1},  
      cond:{'age':{$gt:25}},  
      reduce:function(curr,result) {  
          result.cnt += 1;  
      },  
      initial:{cnt:0}  
      }  
      )  
 4.   #### 每个栏目下的number sum()操作 
      db.person.group(  
      {  
      key:{'name':1},  
      cond:{},  
      reduce:function(curr,result) {  
          result.num += curr.number;  
      },  
      initial:{num:0}  
      }  
      )   
 5.   #### 每个栏目最大的number, max()操作 
      db.person.group(  
      {  
      key:{'name':1},  
      cond:{},  
      reduce:function(curr , result) {  
          if(curr.number > result.max) {  
              result.max = curr.number;  
          }  
      },  
      initial:{max:0}  
      }  
      )  
 6.   #### 查询每个栏目下number平均操作 
      db.person.group(  
      {  
      key:{'name':1},  
      cond:{},  
      reduce:function(curr , result) {  
          result.cnt += 1;  
          result.sum += curr.number;  
      },  
      initial:{sum:0,cnt:0},  
      finalize:function(result) {  
          result.avg = result.sum/result.cnt;  
      }  
      }  
      )  
            
### aggregate操作
 1.   #### 每个栏目下的数量  
      db.person.aggregate(  
      [{$group:{_id:"$name",total:{$sum:1}}}]  
      )   
 2.   #### 查询有多少条记录  
      db.person.aggregate(  
      [{$group:{_id:null,total:{$sum:1}}}]  
      )   
 3.   #### 每个栏目下number大于10个数  
      db.person.aggregate(  
      [  
      {$match:{number:{$gt:10}}},  
      {$group:{_id:"$name",total:{$sum:1}}}  
      ]  
      )  
 4.   #### 每个栏目下number大于10个数,且个数大于3  
      db.person.aggregate(   
      [   
      {$match:{number:{$gt:10}}},   
      {$group:{_id:"$name",total:{$sum:1}}},   
      {$match:{total:{$gte:3}}}   
      ]   
      )   
 5.   #### 每个栏目下的number,并按总量排序  
      db.person.aggregate(  
      [  
      {$group:{_id:"$name",total:{$sum:"$number"}}},  
      {$sort:{total:1}}  
      ]  
      )  
 6.   #### 每个栏目的number平均,并按平均数由高到低排序  
      db.person.aggregate(  
      [  
      {$group:{_id:"$name",avg:{$avg:"$number"}}},  
      {$sort:{avg:-1}}  
      ]  
      )   
      
### mapReduce操作 (分布式)
 1.   #### 计算每个栏目的number总量  
      var map = function() {   
          emit(this.name,this.number);   
      }  
      var reduce = function(name,numbers) {  
          return Array.sum(numbers);  
      }    
      db.person.mapReduce(map,reduce,{out:'res'});  
      db.res.find()  
      
 2.   #### 计算每个栏目下number的平均数   
      var map = function() {  
          emit(this.name,this.number);   
      }   
      var reduce = function(name,values) {   
          return Array.avg(values);   
      }  
      db.person.mapReduce(map,reduce,{out:'res'});   
      db.res.find()   