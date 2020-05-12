[//title]: (Node.js箭头函数)
[//englishtitle]: (nodejs-arrow-function)
[//category]: (node,javascript)
[//tags]: (node,javascript,arrow-function,箭头函数)
[//createtime]: (20200512)
[//updatetime]: (20200512)

## 概述

箭头函数的语法规则为

```js
(parameters) => {
  statements;
};
```

等价于

```js
function (parameters) {
  statements;
}
```

它的作用主要用来简化匿名函数，但是有点差别，在箭头函数中不能使用`this`关键字

本文 github 代码库: [https://github.com/huahuayu/nodejs-playground/tree/master/01-arrow-function](https://github.com/huahuayu/nodejs-playground/tree/master/01-arrow-function)

## 示例

以下示例中的`squareFun1`和`squareFun2`、`squareFun3`都是等价的

```js
// 匿名函数赋值
const squareFun1 = function (x) {
  return x * x;
};

// 箭头函数省略function关键字
const squareFun2 = (x) => {
  return x * x;
};

// 如果函数体中只有一句statement，可以省略大括号
const squareFun3 = (x) => x * x;

console.log(squareFun1(2)); // output: 4
console.log(squareFun2(2)); // output: 4
console.log(squareFun3(2)); // output: 4
```

## this 使用

箭头函数是没有绑定`this`的，所以在 `getName2` 函数中是读不到`this.name`的

```js
const person = {
  name: "shiming",
  getName1: function () {
    return this.name;
  },
  getName2: () => {
    return this.name;
  },
};

console.log(person.getName1()); // output: shiming
console.log(person.getName2()); // output: undefine
```
