[//title]: (bash-cookbook)
[//englishtitle]: (bash-cookbook)
[//category]: (linux,bash,tutorial,cookbook)
[//tags]: (linux,bash,cookbook)
[//createtime]: (20220311)
[//updatetime]: (20220316)

## 什么是 bash

`bash` 是`Bourne-Again SHell`首字母的缩写，一个兼容 `sh` 的 shell 命令解释器。

这个程序一般是存放在 `/bin` 目录下，如果你的 Linux 系统比较特别，bash 也有可能被存放在 `/sbin` 、`/usr/local/bin` 、`/usr/bin` 、`/usr/sbin` 或 `/usr/local/sbin` 这样的目录下。

如果还找不到，你可以用 `which bash`, `locate bash` ,`find / -name bash 2>/dev/null` 或 `whereis bash` 这几个命令找出 bash 所在的位置；如果仍然找不到，那你可能需要自己动手安装一个 bash 软件包了。

## 帮助文档

```bash
man bash
```

## hello world

vim script.sh

```bash
#!/bin/bash
# This is a comment
var="Hello World"
echo $var
```

`#!` 后面跟 `bash`解释器的绝对路径，说明这个文件由 `bash` 解释器来解释执行，类似写法有 `#!/bin/perl` , `#!/usr/bin/env python`

单个`#` 作为注释行的开头

## 运行 bash 脚本

```bash
# 1. 使用sh来执行
sh script.sh

# 2. 使用bash来执行
bash script.sh

# 3. 可以把script.sh当可以执行文件运行，但是用户必须对文件有执行权限，如果没有要加上
chmod u+rx script.sh
./script.sh

# 4.使用.来执行
. ./script.sh

# 5.使用source来执行
source script.sh
```

`1`,`2` 效果一致，使用命令解释器执行，只不过一个使用 `sh`, 一个使用 `bash`, 而 `bash` 兼容 `sh`

`3` 是把 `script.sh` 当可执行文件执行，需要对文件有可执行权限

`4`,`5` 完全等价，因为`.` 是 `source`的同义词

**注意:**

`1`,`2`,`3` 和 `4`,`5`有一个显著的不同，前者是在一个新的 shell 环境中执行，而后者是在当前 shell 环境中执行

## 保留字

```text
! case  do done elif else esac fi for function if in select then until while { } time [[ ]]
```

## 变量赋值

### 简单赋值

使用前后都不带空格的`=`号做变量赋值。

```bash
a=10
b="$a"     # 双引号引住时，b 的值为 10
echo $b
b1='$a'     # 单引号引住时，不会解析变量a的内容，b1 的值为 "$a" 字符串
echo $b1
b2=${a}abc  # 为了拼接时不混淆，用大括号括起变量，b2的值为 "10abc"
echo "len of b2: "${#b2} # 输出b字符串的长度
```

### 数组赋值

```bash
c=(1 "abc" 100)   # 列表变量的赋值
echo ${c[@]}   # 输出所有元素
echo ${c[*]}   # 同上
echo ${c[0]}   # 输出第一个元素
echo ${#c[@]}  # 输出数组长度
echo ${#c[*]}  # 输出数组长度
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

### 循环赋值

```bash
for file in $(ls /etc);do echo $file;done
```

### 只读变量

使用`readonly`修饰可以把变量定义为只读，再次赋值会报错

```bash
DNS="8.8.8.8"
readonly DNS
```

### 位置参数

从命令行传递到脚本的参数：`$0`，`$1`，`$2`，`$3`...

`$0` 就是脚本文件自身的名字，`$1` 是第一个参数，`$2` 是第二个参数，`$3` 是第三个参数，然后是第四个。`$9` 之后的位置参数就必须用大括号括起来了，比如，`${10}`，`${11}`，`${12}`。

- `$#` : 传递到脚本的参数个数
- `$*` : 以一个单字符串显示所有向脚本传递的参数。与位置变量不同,此选项参数可超过 9 个
- `$$` : 脚本运行的当前进程 ID 号
- `$!` : 后台运行的最后一个进程的进程 ID 号
- `$@` : 与 $\* 相同,但是使用时加引号,并在引号中返回每个参数
- $? : 显示最后命令的退出状态。 0 表示没有错误,其他任何值表明有错误。

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

## 美元符号

`$` 代表变量，可用于变量替换

```bash
#!/bin/bash
a=1
echo $a
echo ${a}2345
```

## 字符串运算

| 运算符 | 说明                                         | 举例                     |
| ------ | -------------------------------------------- | ------------------------ |
| =      | 检测两个字符串是否相等，相等返回 true。      | [ $a = $b ] 返回 false。 |
| !=     | 检测两个字符串是否不相等，不相等返回 true。  | [ $a != $b ] 返回 true。 |
| -z     | 检测字符串长度是否为 0，为 0 返回 true。     | [ -z $a ] 返回 false。   |
| -n     | 检测字符串长度是否不为 0，不为 0 返回 true。 | [ -n "$a" ] 返回 true。  |
| $      | 检测字符串是否为空，不为空返回 true。        | [ $a ] 返回 true。       |

```bash
#!/bin/bash

a="abc"
b="efg"

if [ $a = $b ]
then
   echo "$a = $b : a 等于 b"
else
   echo "$a = $b: a 不等于 b"
fi
if [ $a != $b ]
then
   echo "$a != $b : a 不等于 b"
else
   echo "$a != $b: a 等于 b"
fi
if [ -z $a ]
then
   echo "-z $a : 字符串长度为 0"
else
   echo "-z $a : 字符串长度不为 0"
fi
if [ -n "$a" ]
then
   echo "-n $a : 字符串长度不为 0"
else
   echo "-n $a : 字符串长度为 0"
fi
if [ $a ]
then
   echo "$a : 字符串不为空"
else
   echo "$a : 字符串为空"
fi
```

## 文件测试运算符

| 操作符  | 说明                                                                        | 举例                      |
| ------- | --------------------------------------------------------------------------- | ------------------------- |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。                             | [ -b $file ] 返回 false。 |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。                           | [ -c $file ] 返回 false。 |
| -d file | 检测文件是否是目录，如果是，则返回 true。                                   | [ -d $file ] 返回 false。 |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ] 返回 true。  |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。                           | [ -g $file ] 返回 false。 |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。                 | [ -k $file ] 返回 false。 |
| -p file | 检测文件是否是有名管道，如果是，则返回 true。                               | [ -p $file ] 返回 false。 |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。                           | [ -u $file ] 返回 false。 |
| -r file | 检测文件是否可读，如果是，则返回 true。                                     | [ -r $file ] 返回 true。  |
| -w file | 检测文件是否可写，如果是，则返回 true。                                     | [ -w $file ] 返回 true。  |
| -x file | 检测文件是否可执行，如果是，则返回 true。                                   | [ -x $file ] 返回 true。  |
| -s file | 检测文件是否为空（文件大小是否大于 0），不为空返回 true。                   | [ -s $file ] 返回 true。  |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。                         | [ -e $file ] 返回 true。  |

示例

```bash
file="/foo/bar"
if [ -r $file ]
then
   echo "文件可读"
else
   echo "文件不可读"
fi
if [ -w $file ]
then
   echo "文件可写"
else
   echo "文件不可写"
fi
if [ -x $file ]
then
   echo "文件可执行"
else
   echo "文件不可执行"
fi
if [ -f $file ]
then
   echo "文件为普通文件"
else
   echo "文件为特殊文件"
fi
if [ -d $file ]
then
   echo "文件是个目录"
else
   echo "文件不是个目录"
fi
if [ -s $file ]
then
   echo "文件不为空"
else
   echo "文件为空"
fi
if [ -e $file ]
then
   echo "文件存在"
else
   echo "文件不存在"
fi
```

## 括号

reference [shell 中各种括号的作用()、(())、[]、[[]]、{}](https://www.runoob.com/w3cnote/linux-shell-brackets-features.html)

### 小括号

在小括号中的命令列表，将会另开一个 shell 来运行，不影响当前 shell 的上下文。

```bash
cat test.sh
#!/bin/bash
a=123
(a=321;b=456)
echo $a
echo $b

bash test.sh
123

```

小括号还可以创建数组

```bash
#!/bin/bash
arr=(1 3 5 7 9 "abc")
echo ${arr[@]}
echo ${arr[0]}
```

### 双小括号

在双小括号中支持整数运算

```bash
echo $(( 10 - 1 * 3 / 2 ))
```

或者写为

```bash
echo $(expr 10 - 1 \* 3 / 2) # 乘法要转义，否则报语法错误
```

如果要小数运算，要借助外部的命令，比如说`bc`

```bash
#!/bin/bash

radius=2.4

pi=3.14159

girth=$(echo "scale=4; 3.14 * 2 * $radius" | bc)

area=$(echo "scale=4; 3.14 * $radius * $radius" | bc)

echo "girth=$girth"

echo "area=$area"
```

### 单中括号

`[` 是一个名为`[`的程序，等价于 `test` 程序，如果条件的值为 true，则返回 0;如果条件的值为 false，则返回 1，以下表达式是等价的

```bash
test expression
[ expression ]
```

**在 bash 的语法中使用中括号作为 shell 的判断式时，中括号的两端必须要有空格符来分隔，并且在中括号[]内的每个组件都需要有空格键来分隔。**

示例

测试变量是否等于/或不等于某个字符串

```bash
[ "$variable" ==|!= "string" ] # 注意符号左右都用空格分隔
```

比较变量和数字大小（不可以用大于、小于、等于号，要用 equal/not equal/greater than/less than/greater than or equal/less than or equal）

```bash
[ "$variable" -eq|ne|gt|lt|ge|le integer ]
```

测试文件是否存在

```bash
#!/bin/bash
filename=foo.sh
if [ -e "$filename" ]; then
    echo "File $filename exists, backup"; cp $filename $filename.bak
else
    echo "File $filename not found, create"; touch $filename
fi;
```

### 双中括号

1. [[是 bash 程序语言的关键字。并不是一个命令，[[]] 结构比[ ]结构更加通用。在[[和]]之间所有的字符都不会发生文件名扩展或者单词分割，但是会发生参数扩展和命令替换。
1. 支持字符串的模式匹配，使用=~操作符时甚至支持 shell 的正则表达式。字符串比较时可以把右边的作为一个模式，而不仅仅是一个字符串，比如[[hello == hell?]]，结果为真。[[]] 中匹配字符串或通配符，不需要引号。
1. 使用[[…]]条件判断结构，而不是[ … ]，能够防止脚本中的许多逻辑错误。比如，&&、||、<和> 操作符能够正常存在于[[]]条件判断结构中，但是如果出现在[ ]结构中的话，会报错。
1. bash 把双中括号中的表达式看作一个单独的元素，并返回一个退出状态码。

### 大括号

#### 列举值

```bash
touch {a,b,c,d,e}.txt
ls
```

```bash
touch {1..5}.txt
touch {a..d}.txt
ls
```

在大括号中，不允许有空白

#### 代码块

```bash
#!/bin/bash
a=123
{a=321}
echo $a
b="too longgg"
echo ${b%gg}    # 截断最后两个"gg"
greeting="Hello World"
echo Goodbye${greeting#Hello}  # 截断最前面的"Hello"
```

在代码块中定义的变量在外部可见

### 右尖括号

`>` 覆盖写 `>>` 附加写（附在原有内容之后）

```bash
 #!/bin/bash
 echo "Hello World" > my.txt
```

### 双左尖括号

用 `cat` 创建一个新文件或覆盖写同名文件

```bash
cat > foo.txt <<EOF
hello
world
EOF
```

## 引号

双引号

"STRING" 将会阻止（解释）STRING 中大部分特殊的字符。

单引号

'STRING' 将会阻止 STRING 中所有特殊字符的解释，这是一种比使用"更强烈的形式。

```bash
a=10
b="$a"     # 双引号引住时，b 的值为 10
echo $b
b1='$a'    # 单引号引住时，不会解析变量a的内容，b1 的值为 "$a" 字符串
```

## 斜杠与反斜杠

### 斜杠

`/`表示文件名路径分隔符，如`/home/foo/bar`，注意在 linux 中表示路径的时候，许多个 `/` 跟一个 `/` 是一样的。 `//home//foo//bar` 等价于 `/home/foo/bar`

### 反斜杠

`\` 是该行最后一个字符时表示续行

其他位置表示转义，常见转义:

```text
\n 表示新的一行
\r 表示回车
\t 表示水平制表符
\v 表示垂直制表符
\b 表示后退符
\a 表示"alert"(蜂鸣或者闪烁)
\0xx 转换为八进制的 ASCII 码, 等价于 0xx
\" 表示引号字面的意思
```

## 反引号

反引号中的命令优先执行

```bash
cp `mkdir back` test.sh back
ls
```

先创建了 back 目录，然后复制 test.sh 到 back 目录。

## 问号

三目运算符

```bash
 #!/bin/bash
 a=10
 (( t=a<50?8:9 ))
 echo $t
```

## 分支判断

### if else

```bash
$ VARIABLE=abcdef
$ if [[ $VARIABLE == abcdef ]] ; then echo yes ; else echo no ; fi
yes
```

如果用改成一个中括号，`[ $VARIABLE = abcdef ]` 要减少一个 `=` 号

### case

```bash
case EXPRESSION in

  PATTERN_1)
    STATEMENTS
    ;;
  PATTERN_2)
    STATEMENTS
    ;;
  PATTERN_N)
    STATEMENTS
    ;;
  *)
    STATEMENTS
    ;;
esac
```

示例，用 case 判断用户输入是 yes 还是 no

```bash
$ cat yesno.sh
#!/bin/bash
echo -n "Do you agree with this? [yes or no]: "
read yno
case $yno in
        [yY] | [yY][Ee][Ss] )
                echo "Agreed"
                ;;
        [nN] | [n|N][O|o] )
                echo "Not agreed, you can't proceed the installation";
                exit 1
                ;;
        *) echo "Invalid input"
                ;;
esac

$ ./yesno.sh
Do you agree with this? [yes or no]: YES
Agreed
```

但是在判断数字范围时，case 不是很好用，主要是 bash 的 case 是做正则匹配的，不能数字直接比大小，这时候用 if else 更适用

```bash
$ cat resolution.sh
#!/bin/bash
read -p "please enter a resolution: " X
if ((461<=X && X<=660))
then
    echo "480p"
elif ((661<=X && X<=890))
then
    echo "720p"
elif ((891<=X && X<=1200))
then
    echo "1080p"
else
    echo "DVD"
fi

$ bash resolution.sh
please enter a resolution: 1000
1080p
```

## 循环

### while

```bash
$cat valid-number-checker.sh
#! /bin/bash
while true
    do
        read -p "input value(q to quit): " var;
    if [[ $var == q || $var == Q ]];then
        exit;
    fi
    echo "$var" | grep [^0-9] >/dev/null && echo "$var is not number" || echo "$var is number"
done

$ bash valid-number-checker.sh
input value(q to quit): a
a is not number
input value(q to quit): 10
10 is number
input value(q to quit): q
```

## 获取用户输入

```bash
read VARIABLE
```

```bash
read -p "please input a value: " VARIABLE
```

## 重定向

在 shell 脚本中，默认情况下，总是有三个文件处于打开状态，`stdin`(键盘输入)、`stdout`（输出到屏幕）、`stderr`（也是输出到屏幕），它们分别对应的文件描述符是 `0`，`1`，`2` 。

`>` 默认为标准输出重定向，与 `1>` 相同。

`2>&1` 意思是把 标准错误输出 重定向到 标准输出.

`&>file` 意思是把标准输出 和 标准错误输出 都重定向到文件 file 中

**/dev/null 是一个空文件，这个文件比较特殊，所有传给它的东西它都丢弃掉**

`foo.sh > filename`: 重定向 foo.sh 的输出覆盖写到文件 filename 中。如果 filename 不存在会被创建；存在的话，内容会被覆盖。

`foo.sh >> filename`: 把 foo.sh 的输出追加到文件 filename 中。如果 filename 不存在的话，将会被创建。

`foo.sh &> filename`: 重定向 foo.sh 的 stdout（标准输出）和 stderr（标准错误）到 filename 中。

`foo.sh >&2`: 重定向 foo.sh 的 stdout 到 stderr 中。

`rm -f $(find /foo -name bar) &> /dev/null`: 静默删除找到的文件

参考 [Shell 重定向 ＆>file、2>&1、1>&2 、/dev/null 的区别](https://blog.csdn.net/u011630575/article/details/52151995)

## 管道

`|` 是管道符，使前面命令的输出作为后面命令的输入。

`ls -al | tr 'a-z' 'A-Z'`: 将`ls -al`的输出全部变为大写

`ls -al | tr 'A-Z' 'a-z'`: 全部变为小写

## 破折号

作为参数前缀，比如`-eq`

```bash
#!/bin/bash

a=5
b=5
if [ "$a" -eq "$b" ]
then
    echo "a is equal to b."
fi
```

## 函数

`bash`函数定义

```bash
foo(){
    echo "bar"
}
```

可以带参数

```bash
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```

也可以有返回值，用 return 显示返回，但是返回值限定为 0-255 之间的数字，如果不加，将以最后一条命令运行结果作为返回值。获取返回值的方式为 `$?`，一般`0`为正常，非`0`为错误。

## 实例

### 获取当前目录/文件名

```bash
$ mkdir -p /tmp/foo/bar
$ cd /tmp/foo/bar
$ basename $(pwd)
bar
bash-3.2$ dirname $(pwd)
/tmp/foo
```

### 清空日志文件

```bash
#!/bin/bash
LOG_DIR=/var/log
cd $LOG_DIR
cat /dev/null > dpkg.log
echo "Logs cleaned up."
exit
```

### echo 写文件

```bash
$ echo "multi-\
line
string
to
file" > foo.txt
```

```bash
$ cat foo.txt
multi-line
string
to
file
```

注意: `\` 为连字符

### 生成随机数

```bash
$(((RANDOM % $((upperBound - lowerBound))) + lowerBound))
```

如生成 [2000, 65000) 范围内的随机数

```bash
for i in $(seq 100); do echo $(((RANDOM % $((65000 - 2000))) + 2000));done
```

参考 https://stackoverflow.com/a/71501367/6797425

### cat 写文件

```bash
$ cat > foo.txt <<EOF
multi-\
line
string
to
file
EOF
```

```bash
$ cat foo.txt
multi-line
string
to
file
```

### 判断一个变量是否是数字

```bash
#! /bin/bash
while true
    do
        read -p "input value(q to quit): " var;
    if [[ $var == q || $var == Q ]];then
        exit;
    fi
    echo "$var" | grep [^0-9] >/dev/null && echo "$var is not number" || echo "$var is number"
done
```

参考 https://www.cnblogs.com/liangyou666/p/10877630.html

## 符号说明

| 字符 | 说明                                                                                                      | 备注 |
| ---- | --------------------------------------------------------------------------------------------------------- | ---- |
| ；   | 命令分隔符，在前一个命令结束时，忽略其返回值，继续执行下一个命令                                          |      |
| &&   | 前一个命令执行成功后，才继续执行下一个命令。例: p1 && p2 ，若 p1 执行成功后，才执行 p2，反之，不执行 p2   |      |
| \|\| | 前一个命令执行失败后，才继续执行下一个命令。例: p1 \|\| p2 ，若 p1 执行成功后，不执行 p2，反之，才执行 p2 |      |
| &    | 后台运行命令，最大的好处是无需等待命令执行结束，就可以在同一命令行下继续输入命令                          |      |
| =    | 变量名=值，为变量赋值。注意"="左右紧跟变量名和值，中间不要有空格                                          |      |
| $    | 变量值替换，"$变量名"替换为shell变量的"值"，为避免在文本连接时混淆，请使用${变量名}                       |      |
| >    | 输出重定向，prog > file 将标准输出重定向到文件（覆盖原文件内容）                                          |      |
| >>   | 输出重定向，prog >> file 将标准输出追加到文件                                                             |      |
| <    | 输入重定向，prog < file 从文件 file 中获取标准输入                                                        |      |
| \|   | 管道命令，例: p1 \| p2 将 p1 的标准输出作为 p2 的标准输入                                                 |      |
| （） | 在子 shell 中执行命令，或用于运算，或用于命令替换（$（command））                                         |      |
| {}   | 在当前 shell 中执行命令，或用在变量替换的界定范围(例如上面的${变量名}用法)                                |      |
| !    | 执行历史记录（history 列表）中的命令，如!1                                                                |      |
| !!   | 上一个历史记录的别名, 以管理员权限执行上一个命令 sudo !!                                                  |      |
| ~    | 用户主目录                                                                                                |      |

参考 https://linuxhint.com/bash-scripting-symbols/
