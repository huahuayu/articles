[//title]: (root-npm-global-install报错node-Permission-denied解决方案)
[//englishtitle]: (npm-g-install-by-root-node-permission-denie)
[//category]: (node)
[//tags]: (nodejs,npm,permission)
[//createtime]: (20201106)
[//updatetime]: (20201106)

## 问题描述

使用 ubuntu 18.04，root 账户执行 `npm install -g yo` 命令时报错, 普通账户执行不报错。

报错信息 `sh: 1: node: Permission denied`

我尝试过的 nodejs 版本（npm 版本与之对应）：

```text
v10.23
v12.19.0
v14.15.0
```

尝试过三个 npm 版本之后我确定不是版本的问题。

命令输出详情

```text
$ npm install -g yo
npm WARN deprecated request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
npm WARN deprecated har-validator@5.1.5: this library is no longer supported
npm WARN deprecated cross-spawn-async@2.2.5: cross-spawn no longer requires a build toolchain, use it instead
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
/root/.nvm/versions/node/v10.23.0/bin/yo -> /root/.nvm/versions/node/v10.23.0/lib/node_modules/yo/lib/cli.js
/root/.nvm/versions/node/v10.23.0/bin/yo-complete -> /root/.nvm/versions/node/v10.23.0/lib/node_modules/yo/lib/completion/index.js

> core-js@3.6.5 postinstall /root/.nvm/versions/node/v10.23.0/lib/node_modules/yo/node_modules/core-js
> node -e "try{require('./postinstall')}catch(e){}"

sh: 1: node: Permission denied
npm ERR! code ELIFECYCLE
npm ERR! syscall spawn
npm ERR! file sh
npm ERR! errno ENOENT
npm ERR! core-js@3.6.5 postinstall: `node -e "try{require('./postinstall')}catch(e){}"`
npm ERR! spawn ENOENT
npm ERR!
npm ERR! Failed at the core-js@3.6.5 postinstall script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /root/.npm/_logs/2020-11-06T02_00_44_654Z-debug.log
```

## 解决方案

执行以下命令即可。

原理是什么我没细查，反正可以 work（谁说得清原理请告诉我）。

```bash
$ npm config set user 0
$ npm config set unsafe-perm true
```

**提示：**

如果你在 root 账户下运行 npm，这就是正确的答案。但请记住，这是不安全的(除非你知道自己在做什么)。

## 参考资料

[sh: 1: node: Permission denied](https://stackoverflow.com/questions/51811564/sh-1-node-permission-denied)
