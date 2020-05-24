[//title]: (Node.js箭头函数详解)
[//englishtitle]: (nodejs-arrow-function)
[//category]: (node,javascript)
[//tags]: (node,javascript,arrow-function,箭头函数)
[//createtime]: (20200512)
[//updatetime]: (20200513)

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

它的作用主要用来简化匿名函数，但是有点差别，在箭头函数中不能使用`this`关键字。

本文 github 代码库: [https://github.com/huahuayu/nodejs-playground/tree/master/01-arrow-function](https://github.com/huahuayu/nodejs-playground/tree/master/01-arrow-function)

## 示例

以下示例中的`squareFun1`和`squareFun2`、`squareFun3`都是等价的。

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

以下例子`getName1`和`getName2`等价，但显然`getName2`是更优雅的写法。

箭头函数是没有绑定`this`的，所以在 `getName3` 函数中是读不到`this.name`的。

```js
const person = {
  name: "shiming",
  getName1: function () {
    return this.name;
  },
  getName2() {
    return this.name;
  },
  getName3: () => {
    return this.name;
  },
};

console.log(person.getName1()); // output: shiming
console.log(person.getName2()); // output: shiming
console.log(person.getName3()); // output: undefined
```

## 代码简化实例

以下代码`getFemaleStudents1()`、`getFemaleStudents2()`、`getFemaleStudents3()`效果一样，但是依次更简洁

```js
const students = {
  students: [
    { name: "alice", gender: "female" },
    { name: "bob", gender: "male" },
    { name: "frank", gender: "male" },
    { name: "lily", gender: "female" },
  ],
  getFemaleStudents1() {
    const femaleStudents = this.students.filter(function (student) {
      return student.gender === "female";
    });
    return femaleStudents;
  },
  // 优化一：减少一个const声明;用一个箭头函数替代了filter中的匿名函数
  getFemaleStudents2() {
    return this.students.filter((student) => {
      return student.gender === "female";
    });
  },
  // 优化二：如果匿名函数只有一句, 可以省略return
  getFemaleStudents3() {
    return this.students.filter((student) => student.gender === "female");
  },
};

/**
 * 三个输出一样
[
  { name: 'alice', gender: 'female' },
  { name: 'lily', gender: 'female' }
]
 */
console.log(students.getFemaleStudents1());
console.log(students.getFemaleStudents2());
console.log(students.getFemaleStudents3());
```
