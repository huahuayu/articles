[//title]: (purge-local-queued-tx-in-geth)
[//englishtitle]: (purge-local-queued-tx-in-geth)
[//category]: (geth,ethereum)
[//tags]: (geth,ethereum)
[//createtime]: (20210524)
[//updatetime]: (20210524)

## 本地交易阻塞

通过本地节点发的交易因为 nonce 值没控制好，在本地造成了阻塞，44 笔交易在本地没发出去。

```text
geth attach ./geth.ipc
> txpool.status
{
  pending: 44,
  queued: 1
}
```

导致这个账号通过本地节点再也不能发交易。

## 解决办法

step1: 将 geth 节点停掉

step2: 找到 geth data 目录，删除`transactions.rlp`文件, 这个文件就是 rlp encoded transaction 数据

step3: 重启 geth 节点

## 参考资料

https://github.com/ethereum/go-ethereum/issues/16284#issuecomment-371450865

https://ethereum.stackexchange.com/questions/1774/geth-how-to-clear-queued-transactions
