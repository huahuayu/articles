[//title]: (solidity-cookbook)
[//englishtitle]: (solidity-cookbook)
[//category]: (solidity,cookbook)
[//tags]: (solidity,cookbook)
[//createtime]: (20210518)
[//updatetime]: (20210518)

## 可见性

| 类型        | 默认可见性     | 可选可见性                             |
| ----------- | -------------- | -------------------------------------- |
| function    | public         | public / internal / private / external |
| constructor | public         | public / internal / private / external |
| variable    | internal       | public / internal / private            |
| event       | Not applicable | always public                          |

`external`:

如果一个 function 只会从外部调用时，使用 external 修饰，使用 external 定义的 function 通常比使用 public 定义的 function 更省 gas，特别是在入参为数组的情况下。

`public`:

public 修饰的 function 可以从合约外部调用也可以从合约内部调用。

public 修饰的 state variables 会默认生成一个 getter function，以便查询该变量的值。

`internal`:

internal 修饰的 function 和 state variables 只能被该合约或派生合约访问。

`private`:

private 修饰的 function 和 state variables 只能被本合约访问。

参考: https://docs.soliditylang.org/en/latest/contracts.html#visibility-and-getters
