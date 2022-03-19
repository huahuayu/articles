[//title]: (非交互式运行geth-attach命令)
[//englishtitle]: (non-interactive-exec-geth-attach-command)
[//category]: (ethereum,blockchain,geth)
[//tags]: (ethereum,blockchain,geth,attach,non-interactive)
[//createtime]: (20220317)
[//updatetime]: (20220319)

## 问题

以太坊的 `geth attach` 命令一般是给管理员提供一个本地的交互式 console，但是有时候希望不通过人机交互式的方式，而是直接用命令行调用，该怎么办？

## 解决方案

示例

```bash
geth --exec 'admin.nodeInfo' attach http://127.0.0.1:8545
```
