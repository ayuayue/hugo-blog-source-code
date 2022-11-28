---
cssclass:
title: GIT 操作
tags: [IT/Git]
image-auto-upload: true
date: 2022-10-03 20:00:47
lastmod: 2022-10-25 08:27:48
---
# GIT 操作
### 删除未被追踪的文件 `Untracked files`

```SQL
git clean -nf # 删除文件,列出要删除的文件,去掉 n 为删除
git clean -nfd # 删除文件及目录, -n 列出,删除去掉n
```

### 回退到上一次提交

```SQL
git reset --hard HEAD^
```

### 暂存修改

```SQL
git stash
git stash list # 列出暂存记录
git stash apply stash@{1} # 应用暂存
git stash clear # 清空暂存：

```

### 将加入版本控制某个文件添加进 .ignore 中

```Bash
git rm --cached file or folder # 去除之前提交的,使 .ignore 文件生效
git commit -am 'add ignore file'
git push

```

## vscode 拉取失败

报错：vscode would clobber existing tag
解决：
```bash
git fetch --tags -f
```

## git 服务器切换分支后拉取冲突

用远程的分支覆盖本地
```bash
git fetch --all
git reset --hard origin/master
```

## 手机端报错

error: object file ... is empty
```bash
find .git/objects -type f -empty | xargs rm
git fetch -p
```

## 虚拟机与 windows 本地状态不同

使用 git status 查看两个系统返回的信息不同，是由于文件结束符，换行符不一致导致的。在 linux 虚拟机中执行
```bash
git config --global core.autocrlf true 
```
即可保持一致

## 将 github 仓库改为 gitee
首先到 gitee 导入 github 的仓库
```bash
git remote -v
git remote rm origin 
git remote add origin git@gitee.com:caoayu/my-docker.git
git branch --set-upstream-to=origin/main main # 分支名
```
然后把公钥添加进gitee。
把https链接改为git，设置公钥后不再需要设置密码，进入项目的 .git/config。把url改成git的链接。


## git pull fatal: Not possible to fast-forward, aborting

合并代码的方式有很多中：git merge,git rebase,git pull等
git 在处理文件merge时，会分成三种处理方式
- –ff–only fast forward模式，快速合并 有冲突就会失败
- –no–ff 非快速合并 会生成一次commit
- –squash 将合并后的不同分支所有的提交记录作为一次提交
fase-forward只要存在冲突就会失败， 我们得配置git pull
```
git config pull.rebase false —- 关闭 rebase
git config pull.rebase true —–开启rebase
git config pull.ff only/false —–开启/关闭 fast-forward
```
其实我们要做的是关闭rebase和fast-forward在pull中的表现，可运行如下代码解决当前分支问题：
```
git config pull.rebase false
git config pull.ff false
```
可运行如下代码解决全局问题：
```
git config --global pull.rebase false
git config --global --add pull.ff false
```


