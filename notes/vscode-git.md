# Visual Studio Code 上初始化本地仓库的使用方法

**1、使用 VSCode 直接打开文件，切换到活动栏中的“源码管理器：GIT”标签并点击“初始化 GIT 存储库”；**

**2、来到当前 Repository 文件夹根目录中，执行下面的命令：**

```shell
# 与远程仓库连接
git remote add origin https://xxxxxx.com/xxxxxx/xxxxxx.git

# 从远程从库的分支同步下来代码，这里同步的是云端的master库
git pull origin master

```

**3、 点击“+”号暂存已经修改的代码；**

**4、输入更改代码的信息说明，并点击勾号提交；**

**5、点开隐藏菜单，第一次点击“发布”，以后点击“推送”代码到云端（`master`分支）。**

**6、使用 `git config --global credential.helper store` 长期存储凭证，再次提交输入用户名及密码后，可以避免每次提交、推送都要输入密码（手动输入，切勿复制）。**

# Visual Studio Code 上本地仓库关联远程仓库的方法

**1、本地仓库与远程关联，获取远程仓库的地址，打开 VSCode 命令行工具，输入命令：`git remote add origin https://github.com/username/project-name.git` 此时本地仓库与远程仓库已经关联上了，可以提交代码到远程**

**2、利用 VSCode 图形界面，输入信息，将新增的文件添加（暂存）到本地 git 仓库中，提交到本地分支上，但这是不能直接 push 代码到远程，否则会报错。因为在远程仓库还存在一个 README.md 文件，而本地却没有**

**3、使用 `git pull --rebase origin master` ，先将远程的项目与本地合并，然后再使用命令： `git push -u origin master` 提交（ _仅第一次提交时需要加 -u，以后就不需要了_ ）。此时，代码提交成功，在远程仓库中已经有了我们提交的代码文件**

> 注意：与远程仓库链接时一定要先 `pull` ，再 `push` ，否则如果本地仓库和远程仓库版本不一样，文件信息不一样（比如远程仓库中有的文件，本地没有，那么直接 `push` ，本地覆盖远程的，肯定不允许），可能导致提交失败，先 `pull` ，如果有冲突，提前在本地解决，这样可以减少风险。

# Git 相关命令

```shell
#建立本地分支与远程分支的映射关系（本地分支与远程分支名称不一定必须同名）
git branch -u origin/branchName

#或者
git branch --set-upstream-to origin/branchName

```

```shell
#撤销本地分支与远程分支的映射关系
git branch --unset-upstream
```

```shell
#解决.gitignore文件修改后不生效的问题
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```
