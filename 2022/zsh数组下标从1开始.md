[//title]: (zsh数组下标从1开始)
[//englishtitle]: (zsh-array-start-from-1)
[//category]: (linux,bash,zsh)
[//tags]: (linux,bash,zsh)
[//createtime]: (20220316)
[//updatetime]: (20220316)

今天测试偶然发现 zsh 下的数组下标是从 1 开始，而且很多 shell 脚本语言都是从 1 开始 (Bourne, csh, tcsh, fish, rc, es, yash)，ksh 是一个例外，而 bash 采用了 ksh 的方式。 #无用但有趣

reference: [Is there a reason why the first element of a Zsh array is indexed by 1 instead of 0?](https://unix.stackexchange.com/questions/252368/is-there-a-reason-why-the-first-element-of-a-zsh-array-is-indexed-by-1-instead-o)
