[//title]: (Node.js回调函数（callback）详解)
[//englishtitle]: (nodejs-callback-function)
[//category]: (node,javascript)
[//tags]: (node,javascript,callback,回调函数,异步编程)
[//createtime]: (20200524)
[//updatetime]: (20200524)

在一个同步程序中，你可以写一些类似的函数：

```js
function processData() {
  var data = fetchData();
  data += 1;
  return data;
}
```

但是如果 `fetchData` 需要很长的时间来加载数据（可能是从硬盘或互联网上的流媒体），那么这就会导致整个程序`阻塞`--直到数据被获取到。`Node.js` 不会等待文件 I/O 之类的事情完成，Node.js 倾向于使用回调函数（callback）。回调是在指定任务完成时调用的函数；这可以避免阻塞，并允许其他代码在此期间运行。

Node.js 的处理方式类似这样：

```js
function processData(callback) {
  fetchData(function (err, data) {
    if (err) {
      console.log("An error has occurred. Abort everything!");
      return callback(err);
    }
    data += 1;
    callback(data);
  });
}
```

乍一看这看起来好像很复杂啊! 但回调是 Node.js 的基础，回调为你提供了一个接口，你可以用回调来控制程序：`做完了那一件事，就做这一件`。这可以让系统能处理的 IO 操作同时发生。例如，在一个有成百上千个阻塞查询请求的 web 服务器中，异步执行阻塞查询让程序能够继续工作，而不是静静地着等阻塞操作完成。这是一个重大的改进。

异步函数的典型惯例：

```js
function asyncOperation ( a, b, c, callback ) {
  // ... lots of hard work ...
  if ( /* an error occurs */ ) {
    return callback(new Error("An error has occurred"));
  }
  // ... more work ...
  callback(null, d, e, f);
}

asyncOperation ( params.., function ( err, returnValues.. ) {
  //This code gets run after the async operation gets run
});
```

你应该遵循[回调惯例](https://nodejs.org/en/knowledge/errors/what-are-the-error-conventions/)，因为大多数 Node.js 用户会希望你的项目遵循它。一般的做法是，`callback函数`是最后一个参数。在函数完成所有操作后，`callback函数`就会被调用。习惯上，回调的第一个参数是`error`。如果函数遇到错误，那么他们通常会以第一个参数为`Error`对象来调用 `callback函数`。如果处理不遇到错误，那么`error`参数为`null`，其余的是返回值。

本文译自：[What are callbacks?](https://nodejs.org/en/knowledge/getting-started/control-flow/what-are-callbacks/)
