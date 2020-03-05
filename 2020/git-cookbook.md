[//title]:(git-cookbook)
[//englishTitle]:(git-cookbook)
[//category]:(git,tutorial)
[//tags]:(git)
[//createTime]:(20200301)
[//lastUpdateTime]:(20200305)
## git命令

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
| git remote remove repoName                        | Remove repoName from list                                |
| git push repoName branch1                         | Push ‘branch1’ to ‘repoName’                             |
| git push repoName –all                            | Push all branches to repoName                            |
| git clone https://nameOfRemoteRepository          | Clone or download remote repository                      |
| git clone –depth 1 https://nameOfRemoteRepository | Clone only last branch                                   |
| git pull repoName branchName                      | Download and merge ‘branchName’ of repoName              |
| git fetch repoName branchName                     | Download, but not merge repoName                         |


## 连接远程repo
### Existing folder
```
cd existing_folder
git init
git remote add origin git@git/remote/repo.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

### Existing Git repository
```
cd existing_repo
git remote rename origin old-origin
git remote add origin git@git/remote/repo.git
git push -u origin --all
git push -u origin --tags
```

## 改变跟踪的远程repo
如果远程repo重命名了，或者想跟踪另一个远程repo，本地repo将无法拉取远程的更新，所以也需要重命名，重命名方法如下  
``` bash
git remote set-url origin git@{new_repo_name}.git
``` 

可以使用`git remote -v`命令来查看修改前后的变化  

## 实用技巧
### git status中文显示问题
git status中文显示为8进制编码，如果要正常显示，可使用命令
```
git config --global core.quotepath false
```  


### 回退操作
**场景一：**    
文件已经commit，甚至push，现在希望以后不再跟踪，且要从repo中删除。   
首先将文件添加到`.gitignore`然后执行以下命令。  
```
$ git rm --cached <file-name>
# 支持通配符
$ git rm --cached *.log
```

如果是文件夹  
```
$ git rm -r --cached directory/
```

`git rm --cached <file-name>`会使文件不再被跟踪，但不会删除本地文件；`git rm -f <file-name>`还会将本地文件都删除掉  

**场景二：**  
文件已经添加到stage，还未commit，但暂时不想提交，想从staged状态变为unstaged状态，文件变更不丢失。  
```
# unstage一个文件
$ git reset HEAD <file-name>
# unstage所有文件
$ git reset HEAD .
```

**场景三：**  
放弃当前所有变更，回退到上一个commit版本  
```
$ git reset --hard
```

放弃所有变更，回退到指定版本
```
$ git reset --hard <commit-id>
```

**场景四：**  
放弃已经track的，但是没有在stage的文件变更，已经在stage的文件保持不变(比`git reset --hard`要安全)   
```
# 单个文件
$ git checkout -- <file-name>
# 所有文件
$ git checkout -- .
```

**场景五：**  
取消当前merge  

git版本 >= 1.6.1  
``` 
git reset –merge 
```

或者git版本 >= 1.7.4  
```
git merge --abort
```

两者等价

### commit msg
[git commit规范](https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html) 
