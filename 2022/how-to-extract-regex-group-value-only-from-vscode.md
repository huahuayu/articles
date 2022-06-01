[//title]: (how-to-extract-regex-group-value-from-vscode)
[//englishtitle]: (how-to-extract-regex-group-value-from-vscode)
[//category]: (regex,vscode,snippet)
[//tags]: (regex,vscode,snippet)
[//createtime]: (20220601)
[//updatetime]: (20220601)

## Requirement

From the source text:

```text
aeblfdkhhhdcdjpifhhbdiojplfjncoa : 1Password – Password Manager : version 2_3_4
ahfgeienlihckogmohjhadlkjgocpleb : Web Store : version 0_2
amaaokahonnfjjemodnpmeenfpnnbkco : Grepper : version 0_0_7_5
bkhaagjahfmjljalopjnoealnfndnagc : Octotree - GitHub code tree : version 7_5_0
```

I want to keep the name and delete other chars, so the final result will be like:

```text
1Password – Password Manager
Web Store
Grepper
Octotree - GitHub code tree
```

## Solution

In vscode search by regex.

Find `.*: (.*) :.*` and replace to `$1` will just keep group 1.

![](https://cdn.liushiming.cn/img/20220601163042.png)
