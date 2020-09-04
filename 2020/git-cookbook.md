[//title]: (git-cookbook)
[//englishtitle]: (git-cookbook)
[//category]: (git,tutorial,cookbook)
[//tags]: (git)
[//createtime]: (20200301)
[//updatetime]: (20200803)

## git 命令

| 命令                                              | 描述                                                     |
| ------------------------------------------------- | -------------------------------------------------------- |
| **Local Repository**                              |                                                          |
| sudo apt-get install git                          | Install git in Linux Ubuntu                              |
| git config –global user.name alice                | Set username as ‘alice’                                  |
| git config –global user.email abc@gmail.com       | Set email as ‘abc@gmail.com                              |
| git config –global core.editor “vim”              | Set ‘vim’ as default text-editor                         |
| git config –global credential.helper cache        | Cache username and password                              |
| git init                                          | Initialize git repository                                |
| git status                                        | File status i.e. modified and untracked etc.             |
| git add .                                         | Add all untracked files                                  |
| git add -u                                        | Add all tracked files and ignore untracked               |
| git add file1 file2                               | Add (stage) file1 and file2                              |
| git rm –cached file1                              | Remove the staged file file1                             |
| git commit -m “commit message”                    | Commit stage file with ‘commit message’                  |
| git log                                           | Show detail list of commits                              |
| git log –oneline                                  | Show hash and commit name only                           |
| git log –graph                                    | Show commits in the form of graph                        |
| git log –oneline –graph                           | Show online-commit in the form of graph                  |
| git diff                                          | Differences between unstaged files and previous commit   |
| git diff –cached                                  | Differences between staged files and                     |
| git diff –stat                                    | Show only changed filenames (not the details)            |
| git reset                                         | Remove all files from stage list (i.e. back to modified) |
| git reset file1                                   | Remove file1 from stage list (i.e. back to modified)     |
| git reset –hard 13802e3                           | Reset to previous commit with hash 13802e3               |
| git reset HEAD –hard                              | remove all changes after last commit                     |
| git reset –merge (git merge --abort)              | abort current merge, without losing commits              |
| git reset HEAD~1 --soft                           | abort current commit, without losing the changes         |
| git checkout file1                                | Remove changes from non-staged file1 to previous commit  |
| git rm file1                                      | Delete file1 from git (but available in previous commit  |
| git mv file1 file2                                | rename file1 to file2                                    |
| git branch                                        | Show all the branches                                    |
| git branch branch1                                | Create branch1                                           |
| git branch -d branch1                             | Delete branch1                                           |
| git checkout branch1                              | Go to branch1                                            |
| git checkout master                               | Go to master branch                                      |
| git merge branch1                                 | Merge the brach1 to current branch e.g. master           |
| git checkout 13802e3                              | Create new branch from previous commit 13802e3           |
| git checkout -b branch1                           | First checkout and then create branch                    |
| **Remote repository**                             |                                                          |
| git remote add repoName https://url_of_repo       | Add remote repo with name ‘repoName’                     |
| git remote -v                                     | Show list of added repoNames                             |
| git remote set-url origin git@{new_repo_name}.git | change remote repo url to track                          |
| git remote remove repoName                        | Remove repoName from list                                |
| git push repoName branch1                         | Push ‘branch1’ to ‘repoName’                             |
| git push repoName –all                            | Push all branches to repoName                            |
| git clone https://nameOfRemoteRepository          | Clone or download remote repository                      |
| git clone –depth 1 https://nameOfRemoteRepository | Clone only last branch                                   |
| git pull repoName branchName                      | Download and merge ‘branchName’ of repoName              |
| git fetch repoName branchName                     | Download, but not merge repoName                         |

## 连接远程 repo

### Existing folder

```bash
cd existing_folder
git init
git remote add origin git@git/remote/repo.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

### Existing Git repository

```bash
cd existing_repo
git remote rename origin old-origin
git remote add origin git@git/remote/repo.git
git push -u origin --all
git push -u origin --tags
```

## 改变跟踪的远程 repo

如果远程 repo 重命名了，或者想跟踪另一个远程 repo，本地 repo 将无法拉取远程的更新，所以也需要重命名，重命名方法如下

```bash
git remote set-url origin git@{new_repo_name}.git
```

可以使用`git remote -v`命令来查看修改前后的变化

## git merge vs git rebase

- 相同点：都是用来合并分支
- 不同点：
  - git merge - 合并时会创建一个新的 commit，专门处理合并；会留下真实的 checkout 历史；git log 历史看起来比较凌乱
  - git rebase - 会改写 commit 历史，将修改附加到待合并分支的最新的 commit 上面；使用 rebase，git log 看起来比较好看；使用时**必须谨慎**，不能在公共的分支（不止你一人在改的）上做 rebase，因为 rebase 会改写 commit 历史，别人可能还在基于某个历史 commit 在修改

## 实用技巧

### git status 中文显示问题

git status/git log 中文显示为 8 进制编码，如果要正常显示，可使用命令

```bash
git config --global core.quotepath false
git config --global gui.encoding utf-8
git config --global i18n.commit.encoding utf-8
git config --global i18n.logoutputencoding utf-8
# bash 环境下
export LESSCHARSET=utf-8
# cmd环境下：
set LESSCHARSET=utf-8
```

## git 重名文件

用 git 管理的文件不能直接右键重命名，否则会被当成删除旧文件再新增一个文件，git 下重命名文件应该用下面的方式

```bash
$ git mv file1 file2
```

## 将已跟踪文件的变更加入暂存

已跟踪的文件变更加入暂存，忽略未跟踪的文件

```bash
$ git add -u
```

### 回退操作

**场景一：**  
文件已经 commit，甚至 push，现在希望以后不再跟踪，从远程 repo 中删除，但是本地文件不删除。  
首先将文件添加到`.gitignore`然后执行以下命令。

```bash
$ git rm --cached <file-name>
# 支持通配符
$ git rm --cached *.log
```

如果是文件夹

```bash
$ git rm -r --cached directory/
```

`git rm --cached <file-name>`会使文件不再被跟踪，但不会删除本地文件；`git rm -f <file-name>`还会将本地文件都删除掉。

**场景二：**  
文件已经添加到 stage，还未 commit，但暂时不想提交，想从 staged 状态变为 unstaged 状态，文件变更不丢失。

```bash
# unstage一个文件
$ git reset HEAD <file-name>
# unstage所有文件
$ git reset HEAD .
```

**场景三：**  
本地已 commit，但是未 push 到远程，现在想撤销 commit，但变更不丢失，回到 stage 状态

```bash
git reset HEAD~1 --soft
```

**场景四：**  
放弃当前所有变更，回退到上一个 commit 版本

```bash
$ git reset --hard
```

放弃所有变更，回退到指定版本

```bash
$ git reset --hard <commit-id>
```

**场景五：**  
放弃已经 track 的，但是没有在 stage 的文件变更，已经在 stage 的文件保持不变(比`git reset --hard`要安全)

```bash
# 单个文件
$ git checkout -- <file-name>
# 所有文件
$ git checkout -- .
```

**场景六：**  
取消当前 merge

git 版本 >= 1.6.1

```bash
git reset –merge
```

或者 git 版本 >= 1.7.4

```bash
git merge --abort
```

两者等价

### commit msg

[git commit 规范](https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
