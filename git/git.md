# git小技巧
<!-- TOC -->

- [git小技巧](#git小技巧)
    - [github ssh](#github-ssh)
    - [git配置](#git配置)
    - [git初始化工程](#git初始化工程)
    - [git基本操作](#git基本操作)
    - [git reset](#git-reset)
    - [git revert](#git-revert)
    - [git rebase](#git-rebase)
    - [git cherry-pick](#git-cherry-pick)

<!-- /TOC -->

## github ssh

```text
cd ~/.ssh
ssh-keygen
cat ~/.ssh/id_rsa.pub
ssh -T git@github.com

copy the public key to github

settings -> SSH and GPG keys -> new SSH key
```

## git配置
```text
git config --global user.name "xxx"
git config --global user.email "xxx@xx.com"
git config alias.st status
           alias.co checkout
           alias.cm commit
           alias.df diff
           alias.br branch
           alias.commit ci
           alias.ci commit
git config -l
```

## git初始化工程
```text
touch README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:[github用户名]/[repo名称].git
git push -u origin master
```

## git基本操作

```text
git status 查看文件状态
git checkout 切换分支,舍弃工作目录中的更改
git checkout -b feature/feature-a 基于当前分支创建一个a分支
git commit -m 'description' 从工作区提交到暂存区
git merge master 从master分支合并到当前分支
git push 将更新推送到git上
git pull 取回远程主机某个分支的更新，再与本地的指定分支合并 git pull = git fetch + git merge
git fetch 相当于是从远程获取最新版本到本地, 不会自动合并, 强调全部更新
git log 查看提交日志
```

## git reset
将当前的分支重设到指定的<commit>或者HEAD(默认是HEAD，即最新的一次提交)
```text
git reset [--hard|soft|mixed|merge|keep] [<commit>或HEAD] 
--hard 重设(reset) 索引和工作目录, 永久删除
git reset --hard HEAD~1 回退到当前最近一次的提交 HEAD后面的数字表示会退到的最后第几次

--soft 只回退操作,当前工作空间的修改文件依旧还在,适用于当提交完发现需要重新编辑后在提交
git reset --soft HEAD^ 表示指向HEAD之前最近的一次提交
```

## git revert
revert是撤回指定版本的内容并提交一个新的commit
```text
git revert HEAD^ 标识撤销前一次提交
git revert commit-id 撤回指定提交
```

## git rebase
git rebase 代替 git merge
- 优点: 所有的提交都在一条直线上,可以更清晰的查看
- 缺点: 本地分支的基线被调整
```text
git checkout feature-a
git rebase master
如果发生冲突,则解决冲突
git rebase --continue 相当于执行git-commit
git rebase --abort 终止rebase行动,分支会退到rebase前状态
```

## git cherry-pick
选择某一个分支中的一个或几个commit(s)来进行操作,用于将在其他分支上的 commit 修改，移植到当前的分支
```text
git log 查看某个分支的提交
git cherry-pick -x <commit-id> -x 保留作者信息进行提交
git cherry_pick <start-commit-id>…<end-commit-id> (左开，右闭]
git cherry-pick <start-commit-id>^...<end-commit-id> [左闭，右闭] 
git cherry-pick --continue | --quit | --abort 提交与终止
```