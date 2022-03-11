[//title]: (bash-cookbook)
[//englishtitle]: (bash-cookbook)
[//category]: (linux,bash,tutorial,cookbook)
[//tags]: (linux,bash,cookbook)
[//createtime]: (20220311)
[//updatetime]: (20220311)

## 帮助文档

```bash
man bash
```

## 变量赋值

### 简单赋值

使用前后都不带空格的`=`号做变量赋值。

```bash
a=10
```

### 算术运算赋值

使用`let`做包含算术运算的赋值

```bash
let a=1+1
```

如果不使用`let`

```bash
a=1+1
```

`echo $a`的结果为`1+1`，当字符串处理

## 赋默认值

### 常量默认值

`a=${a:-b}` 当`$a`未定义或者`$a`的值为空时，`$a`的值为 `b`

示例 1: 无初始值，赋默认值

```bash
$ echo $a

$ a=${a:-default-value}
$ echo $a
default-value
```

示例 2: 有初始值

```bash
$ a=some-vaule
$ a=${a:-default-value}
$ echo $a
some-vaule
```

### 变量默认值

以上赋值可以衍生为 `a=${a:-$b}`，即`b`可以为变量

示例

```bash
DATA_ROOT=${DATA_ROOT:-$(pwd)}
```

### :- 和 := 的区别

直接上例子，简单来说，在执行时 `:=` 会直接赋值给左边的变量，而`:-`不会。

```bash
var=
echo "$var"               # prints nothing
echo "${var:-foo}"        # prints "foo"
echo "$var"               # $var is still empty, prints nothing
echo "${var:=foo}"        # prints "foo", assigns "foo" to $var
echo "$var"               # prints "foo"
```

reference: [What's the difference between :- and := in Bash parameter substitution?](https://stackoverflow.com/questions/48218775/whats-the-difference-between-and-in-bash-parameter-substitution)
