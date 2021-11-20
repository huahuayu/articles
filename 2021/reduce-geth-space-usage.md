[//title]: (reduce-geth-space-usage)
[//englishtitle]: (reduce-geth-space-usage)
[//category]: (blockchain,geth,ethereum,bsc)
[//tags]: (blockchain,geth,ethereum,bsc)
[//createtime]: (20211118)
[//updatetime]: (20211118)

According to this ethereum [issue](https://github.com/ethereum/go-ethereum/issues/15797#issuecomment-357030043) @karalabe provide this solution to reduct disk space for fullnode (by "fast syncing" with itself):

```bash
geth --datadir=/my/temp/datadir copydb --cache=512 /my/main/datadir/geth/chaindata/
rm -rf /my/main/datadir/geth/chaindata/
mv /my/temp/datadir/geth/chaindata /my/main/datadir/geth/
```

**Note:**

It only applies to ethereum, but not woking in bsc.

For bsc you need [offline block prune](https://github.com/binance-chain/bsc/issues/502#issue-1041155498)

```
Some of the enhancements below can address the existing challenges with running a BSC full node:

Prune the state. To prune the stale data and make the storage lighter:

Stop the bsc node first.
Run nohup geth snapshot prune-state --datadir {the data dir of your bsc node} &. It will take 3-5 hours to finish.
Start the node once it is done.
Enable diff sync and disable unnecessary transaction exchange.

Stop the bsc node first.
Add DisablePeerTxBroadcast = true under the [Eth] module of the config.tom file.
Add --diffsync to the start command.
Start the node.
If you build a new bsc node, please fetch snapshot from: https://github.com/binance-chain/bsc-snapshots
```

**In short:**

prune state

```bash
geth --datadir /your/data/dir snapshot prune-state
```

once pruning is complete, start Geth again: `sudo systemctl start geth`

## reference

https://gist.github.com/yorickdowne/3323759b4cbf2022e191ab058a4276b2
