[//title]: (zsh启动速度优化-5倍提升)
[//englishtitle]: (speed-up-zsh-load-nvm-tuning)
[//category]: (zsh,linux,mac,problem-solved)
[//tags]: (zsh,nvm,启动速度,mac)
[//createtime]: (20201020)
[//updatetime]: (20201020)

## 概述

Mac 下 `iterm` + `zsh` + `ohmyzsh`配置的组合本来很好用，但是随着使用插件的增多，再加上`.zshrc` / `.zprofile`文件中的一些命令加载耗时，现在新开一个 zsh tab 页异常的慢（3s 以上）。我平时使用命令行的频率很高，所以我决心做一次 zsh 启动性能优化。

## 环境

**软件**

zsh 5.7.1 (x86_64-apple-darwin19.0)

ohmyzsh commit id 7525b1d533b927bc40e3cfb29e399e5f2e0828d4

iterm2 3.3.11

**启用的插件**

```text
plugins=(
  git
  z
  jsontools
  vi-mode
  docker
  docker-compose
  kubectl
  command-not-found
  sudo
  zsh-brew-services
  zsh_reload
  zsh-completions
  zsh-syntax-highlighting
#  zsh-autosuggestions
  extract
)
```

**nvm 配置**

```bash
	export NVM_DIR="$HOME/.nvm"
	[ -s "/usr/local/opt/nvm/nvm.sh" ] && . "/usr/local/opt/nvm/nvm.sh"  # This loads nvm
	[ -s "/usr/local/opt/nvm/etc/bash_completion" ] && . "/usr/local/opt/nvm/etc/bash_completion"  # This loads nvm bash_completion
```

## 查看启动时间

执行`\time zsh -i -c exit`命令可以查看 zsh 启动时间。

```bash
$ \time zsh -i -c exit
        3.09 real         1.36 user         1.17 sys
```

作为对比，使用`--no-rcs` 选项可以测试不加载 `.zshrc` 文件的启动时间，只要 0.01 秒。

```bash
\time zsh --no-rcs -i -c exit
        0.01 real         0.00 user         0.00 sys
```

所以优化的重点是 `.zshrc` 文件。

## 时间消耗分析

量化时间消耗才能有的放矢。

在 `.zshrc` 首行插入 `zmodload zsh/zprof`，依次执行以下命令：

```bash
$ source ~/.zshrc
$ zsh
$ zprof

```

可以得到类似以下结果，耗时都按顺序排好了：

```text
num  calls                time                       self            name
-----------------------------------------------------------------------------------
 1)    1         299.61   299.61   18.92%    299.53   299.53   18.91%  nvm_die_on_prefix
 2)    2         733.99   366.99   46.35%    260.46   130.23   16.45%  nvm
 3)    1         184.36   184.36   11.64%    184.36   184.36   11.64%  compdump
 4)    1         173.62   173.62   10.96%    161.51   161.51   10.20%  nvm_ensure_version_installed
 5)    1         879.20   879.20   55.52%    145.21   145.21    9.17%  nvm_auto
 6)    2         139.50    69.75    8.81%    137.46    68.73    8.68%  bracketed-paste-magic
 7)    1         383.49   383.49   24.22%     94.65    94.65    5.98%  compinit
 8)    1          93.53    93.53    5.91%     93.27    93.27    5.89%  __kubectl_bash_source
 9)  783          80.83     0.10    5.10%     80.83     0.10    5.10%  compdef
10)    3          53.95    17.98    3.41%     53.95    17.98    3.41%  compaudit
11)    1          17.78    17.78    1.12%     17.78    17.78    1.12%  nvm_supports_source_options
12)    1          12.11    12.11    0.76%     12.11    12.11    0.76%  nvm_is_version_installed
13)    1          11.21    11.21    0.71%     10.71    10.71    0.68%  _zsh_highlight_load_highlighters
14)    1           5.93     5.93    0.37%      5.93     5.93    0.37%  _zsh_highlight_bind_widgets
15)    2           5.93     2.96    0.37%      5.93     2.96    0.37%  grep-flag-available
16)    2           5.69     2.85    0.36%      5.69     2.85    0.36%  env_default
17)    1           2.61     2.61    0.16%      2.61     2.61    0.16%  iterm2_decorate_prompt
18)    4           3.58     0.90    0.23%      1.88     0.47    0.12%  _zsh_highlight
19)    6           1.52     0.25    0.10%      1.52     0.25    0.10%  url-quote-magic
20)    1           1.31     1.31    0.08%      1.31     1.31    0.08%  _z_precmd
21)    1           1.01     1.01    0.06%      1.01     1.01    0.06%  colors
22)   25           0.70     0.03    0.04%      0.70     0.03    0.04%  is_plugin
23)    2           0.95     0.48    0.06%      0.61     0.30    0.04%  _zsh_highlight_main_highlighter_highlight_list
24)    2           0.60     0.30    0.04%      0.60     0.30    0.04%  add-zsh-hook
25)    3           0.57     0.19    0.04%      0.57     0.19    0.04%  is-at-least
26)   11           2.07     0.19    0.13%      0.50     0.05    0.03%  (anon)
27)    2           0.37     0.18    0.02%      0.37     0.18    0.02%  nvm_has
28)    2           1.55     0.78    0.10%      0.33     0.16    0.02%  _zsh_highlight_highlighter_main_paint
29)    4         143.48    35.87    9.06%      0.31     0.08    0.02%  _zsh_highlight_call_widget
30)    2           0.19     0.09    0.01%      0.19     0.09    0.01%  _zsh_highlight_main_calculate_fallback
31)    1           0.26     0.26    0.02%      0.17     0.17    0.01%  complete
32)    4           0.16     0.04    0.01%      0.16     0.04    0.01%  _zsh_highlight_main__type
33)    2           0.13     0.06    0.01%      0.13     0.06    0.01%  title
34)    2           0.12     0.06    0.01%      0.10     0.05    0.01%  iterm2_print_state_data
35)    1           0.14     0.14    0.01%      0.09     0.09    0.01%  omz_termsupport_preexec
36)    2           0.09     0.04    0.01%      0.09     0.04    0.01%  _zsh_highlight_add_highlight
37)    1           0.08     0.08    0.01%      0.08     0.08    0.01%  _zsh_highlight_main__precmd_hook
38)    2           0.08     0.04    0.01%      0.08     0.04    0.01%  _zsh_highlight_main_highlighter__try_expand_parameter
39)    1         897.05   897.05   56.64%      0.07     0.07    0.00%  nvm_process_parameters
40)    1           0.07     0.07    0.00%      0.07     0.07    0.00%  zle-line-init
41)    1           2.74     2.74    0.17%      0.07     0.07    0.00%  iterm2_precmd
42)    1         141.08   141.08    8.91%      0.07     0.07    0.00%  _zsh_highlight_widget_orig-s0.0000030000-r5757-bracketed-paste
43)    4           0.11     0.03    0.01%      0.07     0.02    0.00%  _zsh_highlight_highlighter_main_predicate
44)    1           0.05     0.05    0.00%      0.05     0.05    0.00%  bashcompinit
45)    1           0.81     0.81    0.05%      0.05     0.05    0.00%  _zsh_highlight_widget_orig-s0.0000030000-r5757-zle-line-init
46)    3           0.05     0.02    0.00%      0.05     0.02    0.00%  _zsh_highlight_buffer_modified
47)    2           0.05     0.02    0.00%      0.05     0.02    0.00%  _zsh_highlight_main_add_region_highlight
48)    2           0.04     0.02    0.00%      0.04     0.02    0.00%  _zsh_highlight_main_highlighter_expand_path
49)    1           0.12     0.12    0.01%      0.04     0.04    0.00%  omz_termsupport_precmd
50)    1           0.03     0.03    0.00%      0.03     0.03    0.00%  pasteinit
51)    1           1.30     1.30    0.08%      0.03     0.03    0.00%  _zsh_highlight_widget_orig-s0.0000030000-r5757-zle-line-finish
52)    1           0.48     0.48    0.03%      0.03     0.03    0.00%  _zsh_highlight_widget_orig-s0.0000030000-r5757-accept-line
53)    1           0.07     0.07    0.00%      0.03     0.03    0.00%  iterm2_after_cmd_executes
54)    1           0.02     0.02    0.00%      0.02     0.02    0.00%  zle-line-finish
55)    2           0.02     0.01    0.00%      0.02     0.01    0.00%  iterm2_print_user_vars
56)    1           0.03     0.03    0.00%      0.02     0.02    0.00%  iterm2_preexec
57)    1           0.02     0.02    0.00%      0.02     0.02    0.00%  nvm_is_zsh
58)    2           0.01     0.01    0.00%      0.01     0.01    0.00%  _zsh_highlight_main__is_redirection
59)    1           0.01     0.01    0.00%      0.01     0.01    0.00%  _zsh_highlight_preexec_hook
60)    1           0.01     0.01    0.00%      0.01     0.01    0.00%  iterm2_before_cmd_executes
61)    1           0.01     0.01    0.00%      0.01     0.01    0.00%  pastefinish
```

## 问题解决

通过分析可以看出 nvm 命令的加载非常耗时，80% 的精力解决这 20% 的问题就够了。

一通搜索后得知加速 nvm 的加载主要有两种办法：

- 懒加载，使用 node 命令的时候才加载 nvm

```bash
lazy_load_nvm() {
  unset -f node
  export NVM_DIR=~/.nvm
  [[ -s "$NVM_DIR/nvm.sh" ]] && source "$NVM_DIR/nvm.sh"
}

node() {
  lazy_load_nvm
  node $@
}
```

- 使用默认 node 版本 + --no-use 选项

```bash
    # Add default node to path
    export PATH=$HOME/.nvm/versions/node/v10.22.0/bin:$PATH

    # load nvm
	export NVM_DIR="$HOME/.nvm"
    [[ -s "$NVM_DIR/nvm.sh" ]] && source "$NVM_DIR/nvm.sh" --no-use
```

我使用的第二种方案，**瞬间 zsh 启动速度提高了 1.2 秒左右**，而且 node、nvm 命令都还能正常使用！打开新 terminal tab 页感受已经好很多。

## 其他优化

我继续使用`zprof`分析启动时间，再去掉了几个插件，最终剩下的插件如下：

```text
plugins=(
  git
  z
  jsontools
  vi-mode
  docker
  docker-compose
  sudo
  zsh-brew-services
  zsh_reload
  zsh-syntax-highlighting
)
```

现在 terminal 上打开新 tab 的时间为 0.6s，相比之前的 3s 已经很满意了。

```bash
$ \time zsh -i -c exit
        0.61 real         0.32 user         0.26 sys
```

## 优化记录

因为我在 github 用 .dotfiles 管理配置文件，所以本次优化都留有记录

https://github.com/huahuayu/.dotfile/commit/31d9cc2452f8a29e1566b0edb8596772a5756790

## 参考资料

[How to speed up shell load while using NVM](https://www.ioannispoulakas.com/2020/02/22/how-to-speed-up-shell-load-while-using-nvm/)
