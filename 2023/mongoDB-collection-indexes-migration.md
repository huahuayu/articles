[//title]: (mongoDB-indexes-migration)
[//englishtitle]: (mongoDB-indexes-migration)
[//category]: (mongodb,snippet)
[//tags]: (mongodb,snippet)
[//createtime]: (20230222)
[//updatetime]: (20230222)

## 问题描述

如何把 mongoDB 中某个 collection 的 indexes 应用到另一个相似的 collection 上面？

首先可以通过这个原生的方法查看 index 列表

```bash
db.your_collection.getIndexes();
```

结果

![](https://cdn.liushiming.cn/img/20230222155309.png)

但问题是这并不是可执行的语句，如何能像 mysql 一样导出创建 index 的语句，以便可以应用在新表上?

## 解决办法

根据我简单的 research 发现没有原生的方法可以实现，但是`mongosh`可以通过执行`js` script 来实现。

**Step0:** 新建`/tmp/script.js` 文件

```js
var collectionList = db.getCollectionNames();
// var collectionList = ["foo","bar"]
for (var index in collectionList) {
  var collection = collectionList[index];
  var cur = db.getCollection(collection).getIndexes();
  if (cur.length == 1) {
    continue;
  }
  for (var index1 in cur) {
    var next = cur[index1];
    if (next["name"] == "_id_") {
      continue;
    }
    var unique = next["unique"] ? true : false;
    print(
      'db.getCollection("' +
        collection +
        '").createIndex(' +
        JSON.stringify(next["key"]) +
        ",{unique:" +
        unique +
        "},{background:1});"
    );
  }
}
```

**Step1:** 通过`mongosh`执行 script

```bash
mongosh 'mongodb://$user:$passwd@$host:$port/$db?ssl=false&authSource=admin' /tmp/script.js > /tmp/result.txt
```

结果直接是可执行的 mongo 语句

```bash
db.getCollection("foo").createIndex({"created_time":1,"contract":1,"symbol":1},{unique:false},{background:1});
db.getCollection("foo").createIndex({"contract":1,"symbol":1},{unique:false},{background:1});
db.getCollection("foo").createIndex({"created_time":1},{unique:false},{background:1});
```

**Step2:** 根据自己的需求更新语句，然后应用在新 collection 上面
