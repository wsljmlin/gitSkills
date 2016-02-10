git config --global user.name "xxx"
git config --global user.email "xxx@xxx.xxx"

git init ;在当前目录创建版本库
git add xxx ; 添加xxx文件
git commit -m “comment” xxxx; 提交到仓库

git status ;查看当前的状态及结果
git log ; 查看历史记录
git reflog ;查看每一次的log
git  --graph --pretty=oneline --abbrev-commit;按照一定的格式查看log

git reset --hard HEAD^; 恢复到当前版本的上一版本
; HEAD指当前版本 ^指上一版本

git add把文件添加到暂存区
git commit 提交更改，把暂存区的所有修改提交到当前分支
git checkout -- xxx; 让这个文件回到最近一次git commit或git add时的状态
git reset HEAD file可以把暂存区的修改撤销掉，已经add的文件，在commit之前可以恢复

删除rm文件后，使用git status查看状态，如果想恢复被删除的文件可使用git checkout -- xxx； 如果要真正的从版本库里删除，请使用git rm xxx 并使用git commit提交文件
ssh-keygen -t rsa -C "xx@xx.xx"; 生成ssh的公钥和私钥

1.本地与远程库的关联及上传
1.1 .创建一个新的版本库，然后与github上新建的repo关联起来
create a new repository on the command line

echo "# learngit" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:wsljmlin/learngit.git
git push -u origin master ； 第一次上传，代码从本地分支上传到远程
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来
之后上传使用git push origin master即可

1.2 把一个本地的版本库与远程的关联起来
push an existing repository from the command line

git remote add origin git@github.com:wsljmlin/learngit.git
git push -u origin master


从远程库克隆一个本地库
git clone git@githubxxxx/xx.git

分支管理
git checktout -b dev; 创建dev分支并切换过去
相当于git branch dev && git checkout dev

git branch； 查看所有的分支以及当前所片的分支
git merge dev; 把dev分支的工作成果合并到当前分支上
其中fast-forward指合并是“快速模式”
git branch -d dev; 删除dev分支

解决冲突
多是两个分支的最新版本不一致在提交时引起的，在提交后的文件带<<<和>>>的，直接修改再提交就可以解决冲突

合并分支时，如果可能，Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息.
如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息
git merge --no-ff -m "merge with no-ff" dev; 不使用快速模式merge，可以看到分支信息

分支管理基本原则：
1.1 master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活
1.2干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本
1.3 团队人都有自己的分支，然后再合并到dev分支


bug分支
1.1 使用git stash储存当前工作区
1.2 新建bug分支进行bug修改并合并到master分支
1.3 回到工作dev分支，使用git stash pop
git stash list； 查看缓存区中的内容
1.4 你可以多次stash，恢复的时候，先用git stash list查看，然后恢复指定的stash，用命令：git stash apply stash@{0}

git branch -D xx; 强行删除xx分支

多人协作
git remote ; 查看远程库的信息
git remote -v; 查看远程库更详细的信息，显示可推送和抓取的origin的地址
git push origin dev; 把本地的dev分支推送到远端（会在远端创建dev分支）
分支管理：
1. master分支是主分支，因此要时刻与远程同步；
2. dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
3. bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
4.feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

你的小伙伴要在dev分支上开发，就必须创建远程origin的dev分支到本地，于是他用这个命令创建本地dev分支：
$ git checkout -b dev origin/dev

若你与小伙伴的上传远端有冲突，可以先使用git pull把最新的版本更新下来，再合并解决冲突
如果git pull也失败了，原因应该是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：
$ git branch --set-upstream dev origin/dev
然后再git pull解决完再使用上传
多人协作的工作模式通常是这样：
1.1 首先，可以试图用git push origin branch-name推送自己的修改；
1.2 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
1.3 如果合并有冲突，则解决冲突，并在本地提交；
1.4 没有冲突或者解决掉冲突后，再用git push origin branch-name推送就能成功！
1.5 如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream branch-name origin/branch-name。

标签管理
git tag; 查看当前分支的所有tag
git tag v1.0; 设置当前版本的tag
git tag v1.0 Id; 设置id为Id的版本的tag
git show v1.0; 查看tag的版本信息
git tag -a v0.1 -m "version 0.1 released" ID; 创建带有说明的标签

git tag -d v0.1 ; 删除tag
git push origin v1.0; 将tag上传上远端
git push origin --tags；一次性将所有tag上传到远端
删除远程标签先删除本地再从远程删除：
git tag -d v0.9 && git push origin :refs/tags/v0.9; 注意空格

git配置
忽略文件的原则是：
1.1 忽略操作系统自动生成的文件，比如缩略图等；
1.2 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
1.3 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件
自己定义.gitignore文件

配置别名
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"


