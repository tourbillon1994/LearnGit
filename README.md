# LearnGit
记录常用的git 操作
=======================================================================
1、提交commit后
git fetch
git rebase origin/master    
git push origin HEAD:refs/for/master

2、提交代码有误，本地修改，再次提交

git add .
git commit --amend
git pull -r
git push origin HEAD:refs/for/master （gerrit的要求）

3、放弃本次gerrit流程，重新生成提交记录
git reset HEAD^
git commit -a -m "EC:XXXXXXX"
git pull -r
git push origin HEAD:refs/for/vnfp

远端覆盖本地
git fetch --all
git reset --hard origin/master

查看staged的差异
git diff --staged

【git 快捷键设置】    在.git/config文件中设置
[color]
        ui = true
[alias]
        ci = commit
        co = checkout
        st = status
        br = branch
        df = diff
        lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s
%Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit

[branch "master"]
        remote = origin
        merge = refs/heads/master
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true


HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard
[commit_id]。

穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。

要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

git diff    #是工作区(work dict)和暂存区(stage)的比较
git diff --cached    #是暂存区(stage)和分支(master)的比较
git diff HEAD -- test.txt 是branch的test.txt与工作区的test.txt作比较；

工作区(work dict) --ADD---暂存区(stage）---COMMIT---master(HEAD)

git diff 版本号码1 版本号码2  （filename） 比较两个版本（某文件）的差异


场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令  git checkout  (--)
[filename] 

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命
令  git reset HEAD <file>，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推
送到远程库


git rm [file] + git commit ...  ==  rm file + git rm  [file]  + git commit  ...

REMOTE REPOSITORY
ssh-keygen -t rsa -C "youremail@example.com"
git remote add origin git@github.com:michaelliao/learngit.git 
git push -u origin master

git push origin master


查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

合并分支时保留分支 git merge --no-ff -m "merge with no-ff" dev

删除分支：git branch -d <name>

git log --graph命令可以看到分支合并图

git stash : 针对未commit状态   必须是modify 或add 的已有文件 或add 后的新文件
工作现场git stash一下，然后去修复bug，修复后
git stash pop 恢复最近一次 git stash  并删除stash 文件
git stash list 显示 list    stash@{i从0递增}     git stahs apply stash@{i从0递增} 恢复对应 

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>

远程仓库的默认名称是origin， 要查看远程库的信息，用git remote (-v)

git branch  -a | -r | null  查看所有或者远端分支，默认是本地分支

$ git remote -v
origin  git@github.com:michaelliao/learngit.git (fetch)
origin  git@github.com:michaelliao/learngit.git (push)
上面显示了可以抓取和推送的origin的地址。如果没有推送权限，就看不到push的地址

推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该
分支推送到远程库对应的远程分支上
$ git push origin master
如果要推送到其他分支，比如dev，就改成：
$ git push origin dev

首先，可以试图用git push origin <branch-name>推送自己的修改；

如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

如果合并有冲突，则解决冲突，并在本地提交；

没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！

如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git
branch --set-upstream-to <branch-name> origin/<branch-name>。

改变Git commit
git add .如只改commit不需要
git commit --amend 

合并commit
git rebase -i HEAD~2”(后面的2代表着要合并的分支数量)
git rebase -i commit_id 

git rebase  把本地未push的分叉提交历史整理成直线；


Linux 下高亮显示 当前branch 
home/user/.bashrc 中加入  source 刷新
function git_branch {
branch="`git branch 2>/dev/null | grep "^\*" | sed -e "s/^\*\ //"`"
if [ "${branch}" != "" ];then
if [ "${branch}" = "(no branch)" ];then
branch="(`git rev-parse --short HEAD`...)"
fi
echo " ($branch)"
fi
}
export PS1='\u@\h \[\033[01;36m\]\W\[\033[01;32m\]$(git_branch)\[\033[00m\] \$ '


unstage 和 staged 区是公用的，在没提交前，在各branch之间都是公用的，所以要注意commit


git pull = git fetch + git merge
用git pull命令把"origin"分支上的修改pull下来与本地提交合并（merge）成版本M，但这样会形成图中的
菱形，让人很困惑。

git pull --rebase = git fetch + git rebase
创建一个新的提交R，R的文件内容和上面M的一样，但我们将E提交废除，当它不存在（图中用虚线表示）。
由于这种删除，小李不应该push其他的repository.rebase的好处是避免了菱形的产生，保持提交曲线为直
线，让大家易于理解。
在rebase的过程中，有时也会有conflict，这时Git会停止rebase并让用户去解决冲突，解决完冲突后，用
git add命令去更新这些内容，然后不用执行git-commit,直接执行git rebase --continue,这样git会继续
apply余下的补丁。

在任何时候，都可以用git rebase --abort参数来终止rebase的行动，并且mywork分支
会回到rebase开始前的状态。

