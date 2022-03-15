[//title]: (zsh-echo-n-选项无换行符多出百分号)
[//englishtitle]: (zsh-echo-n-option-percent-sign)
[//category]: (linux,bash,echo,zsh)
[//tags]: (linux,bash,echo,zsh,percent-sign,n-option,line-break)
[//createtime]: (20220315)
[//updatetime]: (20220315)

## 问题描述

`echo` 输出内容是默认带换行符的

加上`-n`选项可以去除默认的换行符，效果如下

![](https://cdn.liushiming.cn/img/20220315205405.png)

这个在`bash`下显示正常，但是在`zsh`下会多输出一个`%`

![](https://cdn.liushiming.cn/img/20220315205517.png)

## 解决方案

在 `.zshrc` 中添加以下设置，然后`source ~/.zshrc`

```bash
unsetopt prompt_cr prompt_sp
```

## 参考资料

[Why ZSH ends a line with a highlighted percent symbol?](https://unix.stackexchange.com/questions/167582/why-zsh-ends-a-line-with-a-highlighted-percent-symbol)
