[//title]: (windows-git-clone报错error-invalid-path解决办法)
[//englishtitle]: (fixing-invalid-git-paths-on-windows)
[//category]: (windows,git)
[//tags]: (windows,git,invalid-path)
[//createtime]: (2020-10-28)
[//updatetime]: (2020-10-28)

## 问题

自己的 github 项目在 mac 上没事，在 windows git 客户端 clone 下来报错`error: invalid path '2020/vim 强制保存命令:w !sudo tee %详解.md'`

```bash
$ git clone https://github.com/huahuayu/articles.git Cloning into 'articles'...
remote: Enumerating objects: 123, done.
remote: Counting objects: 100% (123/123), done.
remote: Compressing objects: 100% (87/87), done.
remote: Total 599 (delta 40), reused 114 (delta 36), pack-reused 476
Receiving objects: 100% (599/599), 343.09 KiB | 467.00 KiB/s, done.
Resolving deltas: 100% (251/251), done.
error: invalid path '2020/vim 强制保存命令:w !sudo tee %详解.md'
fatal: unable to checkout working tree
warning: Clone succeeded, but checkout failed.
You can inspect what was checked out with 'git status'
and retry with 'git restore --source=HEAD :/'
```

## 解决办法

报错原因是在 windows 下这是非法 file path, 参见 [windows 官方说明](https://docs.microsoft.com/en-us/windows/desktop/fileio/naming-a-file)。

解决办法就是在远程 repo 将文件名改为合法的即可。

按 windows 的文档，以下都是不能用的：

```text
Use any character in the current code page for a name, including Unicode characters and characters in the extended character set (128–255), except for the following:

The following reserved characters:

< (less than)
(greater than)

: (colon)
" (double quote)
/ (forward slash)
\ (backslash)
| (vertical bar or pipe)
? (question mark)
(asterisk)
```

我改完了再次 clone，又报错了：

```bash
$ git clone https://github.com/huahuayu/articles.git
Cloning into 'articles'...
remote: Enumerating objects: 130, done.
remote: Counting objects: 100% (130/130), done.
remote: Compressing objects: 100% (94/94), done.
remote: Total 606 (delta 42), reused 114 (delta 36), pack-reused 476
Receiving objects: 100% (606/606), 345.27 KiB | 464.00 KiB/s, done.
Resolving deltas: 100% (253/253), done.
error: invalid path '2020/实测: 使用 cdn 对搜索引擎不友好.md'
fatal: unable to checkout working tree
warning: Clone succeeded, but checkout failed.
You can inspect what was checked out with 'git status'
and retry with 'git restore --source=HEAD :/'
```

那就再改，报一个改一个，改到所有文件名合法为止。。。

## 参考资料

[Fixing Invalid Git Paths on Windows](https://brendanforster.com/notes/fixing-invalid-git-paths-on-windows/)
