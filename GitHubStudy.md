## 1、检查用户名和邮箱是否配置

> * git *config* --*global*  --*list* 

## 2、配置用户名和邮箱

> * git config --global user.email "这里换上你的邮箱"
> * git config --global  user.name "这里换上你的用户名"

## 3、生成秘钥

> * ssh-keygen -t rsa -C "这里换上你的邮箱"

> - 执行命令后需要进行3次或4次确认：确认秘钥的保存路径（如果不需要改路径则直接回车）；
>   如果上一步置顶的保存路径下已经有秘钥文件，则需要确认是否覆盖（如果之前的秘钥不再需要则直接回车覆盖，如需要则手动拷贝到其他目录后再覆盖）；
>   创建密码（如果不需要密码则直接回车）；

## 4、配置秘钥

> * 到C:\Users\benson\\.ssh\打开id_rsa.pub，复制全部内容。
> * 到GitHub的setting中的SSH and GPG keys，new SSH key，把上面的内容复制进去，定义名称。

## 5、在GitHub上创建一个仓库

> * 建议参考<https://www.liaoxuefeng.com/wiki/896043488029600/898732792973664>
> * 下面的6、7、8三条建议直接省略，采用git clone 地址；该方式适用于先在GitHub上建仓储。
> * 6、7、8的方式适用于先在本地建仓储，后再GitHub上建仓储。

## 6、本地初始化

> * 进入要同步的目录执行：git init

## 7、添加远程服务器地址

> 把刚才创建的仓库的地址添加到本地:
> git remote add origin https://github.com/bensondawn/bigdata.git

## 8、更新服务器数据到本地

> * git pull

## 9、提交本地资源

> * git add .
> * git commit -m "内容描述"
> * git push origin master（第一次push要用git push -u origin master）

## 10、push到GitHub服务器

> * git push origin master

## 11、免去每次都要输入用户名和密码的麻烦

> - git config --global credential.helper store
> - git pull    输入用户名、密码，之后就不会再输入了。

## 12、常用命令

> 如果想把GitHub上的仓储下载下来，并随时更新可以这样：

> * git clone https://github.com/bensondawn/bigdata.git
> * git clone -b dev https://github.com/bensondawn/bigdata.gitl 克隆分支dev下的代码。
> * git pull（以后直接用改命令从GitHub上更新就行了！）
> * git pull origin dev 把gitLab上dev分支上的内容都拉取到本地
> * git push origin dev 提交代码到dev分支
> * git fetch origin dev 设定当前分支的 FETCH_HEAD 为远程服务器的dev 分支；此时不会在本地创建dev分支，这个操作是git pull origin dev的第一步。
> * rebase操作可以把本地未push的分叉提交历史整理成直线；
> * rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

## 13、删除github上的文件或文件夹

> -  删除仓库中的目录：
>    -  git rm -r --cached 目录名
>    -  git commit -m '描述'
>    -  git push -u origin master
> -  删除仓库中的文件，保留本地的文件：
>    -  git rm --cached file
>    -  git commit -m "xxx"
>    -  git push origin master
> -  删除本地及仓库中的文件：
>    -  git rm file
>    -  git commit -m "xxx"
>    -  git push origin master

## 14、git reset使用

> - git add 如果添加了错误的文件的话，git status 先看一下add 中的文件，撤销的方法：
>     - git reset HEAD 如果后面什么都不跟的话，就是上一次add 里面的全部移到工作区。即：将暂存区和HEAD保持一致，撤销上一次的add操作。
>    - git reset HEAD XXX.java或者git reset HEAD .  就是对某个java文件或者所有add的文件移到工作区。
>    - git reset HEAD public/ 即把add的public目录移到工作区。
>  - git reset三种方式（后面的1是指回退几个版本）
>     - git reset(--mixed) HEAD~1
>  回退一个版本，会将暂存区的内容和本地已提交的内容全部恢复到未暂存的状态。暂存区就是git add临时提交到的地方；未暂存的状态就是将这些文件标记为待提交。
>     - git reset --soft HEAD~1
>  回退一个版本,不清空暂存区,将已提交的内容恢复到暂存区,不影响原来本地的文件(未提交的也不受影响)，这些文件还在暂存区中。
>        
>     - git reset --hard
>    
>       - git reset --hard HEAD 表示回退到当前版本(即最近提交的版本)，HEAD指向当前版本。工作区和暂存区的修改会丢失，跟HEAD保持一致。如果你修改了一些代码，想去除，就可以用git reset --hard HEAD 一次性去除。
>    
>       - git reset --hard HEAD~1 表示回退到上一个版本,清空暂存区,工作区的代码也将替换成上一个版本的代码。
>

## 15、git stash使用

- git stash //将修改存储到stash暂存区，工作区会删除这些修改，重置为最近一次提交的状态(commit)。
- git stash save <message>  //给stash存储取个名字
- git stash  show  stash@{X} -p //查看stash@{X}暂存的修改
- git stash pop //取出修改，并删除对应的stash存储
- git stash list //查看stash暂存区的所有暂存修改记录
- git stash apply stash@{X} //取出相应的stash暂存，不会删除stash暂存区的内容
- git stash drop stash@{X} //将记录列表中取出的对应stash暂存记录删除
- git stash clear  //清空stash暂存区

## 16、git diff命令

- git diff --cached	比较暂存区和HDAD的不同。
- git diff   比较暂存区和工作区的不同。

## 17、git log命令

- git log	显示所有的提交。
- git log --oneline 简化git log的默认的输出。

## 18、git add命令

- git add -A  提交所有变化
- git add -u  提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)
- git add .  提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件
- git add 文件夹/ 
- git add *.文件类型

## 19、git checkout命令

- 创建新分支和切换分支：
  - 创建新分支：git branch branchName
  - 查看所有分支：git branch -a
  - 切换到新分支：git checkout branchName
  - 上面两个命令合成为一个命令：git checkout -b branchName
- 将暂存区内容恢复到工作区，即覆盖工作区的内容：
  - git checkout -- readme.txt  把暂存区的文件readme.txt覆盖工作区的文件
  - 如果要还原多个文件，那么使用空格分隔：git checkout -- readme.txt antzone.txt
  - 如果要还原所有文件：git checkout -- .
  - 特别说明：文件名称前面有两个横杠，并且中间采用空格分隔（否则报错）。此种方式可以防止Git出现误判，加入暂存区有一个文件名为ant（没有后缀名），恰好当前项目也有有个名为ant的分支，这个时候Git会优先将ant当做分支处理，于是就有可能导致错误。
- 用指定commit提交的内容覆盖工作区:
  - git checkout 6c89271 -- readme.txt   6c89271 指的是git log --oneline命令显示的版本号
  - git checkout HEAD~2 -- readme.txt
  - git checkout Develop -- readme.txt  如果分支后面跟着文件路径，那么就是将对应分支中的文件还原到当前分支的工作区。

## 20、git branch命令

- git branch	不带参数：列出本地已经存在的分支，并且在当前分支的前面用"*"标记。
- git branch -r   查看远程版本库分支列表。
- git branch -a   查看所有分支列表，包括本地和远程。
- git  branch  -v	查看各个分支最后一次提交。
- git branch dev   创建名为dev的分支，创建分支时需要是最新的环境，创建分支但依然停留在当前分支。
- git branch -d dev   删除dev分支，如果在分支中有一些未merge的提交，那么会删除分支失败，此时可以使用 git branch -D dev：强制删除dev分支。
- git branch -vv   可以查看本地分支对应的远程分支。
- git branch -m oldName newName   给分支重命名。

## 21、git merge

> 分支同步主线代码步骤：
>
> 1、git checkout master 却换到主线
>
> 2、git pull 把主线代码拉取到本地
>
> 3、git checkout dev-v1.1 切换导分支
>
> 4、git merge master 合并主线代码，此时有冲突解决冲突，之后本地分支代码和主线代码就一样了。
>
> 5、git push origin dev-v1.1 推送到远端分支。

## 22、git remote 添加多个远程库地址

> **方法一：**首先，我们从零开始， 假设你现在想要增加3个远程库地址，分别为 :
>
> <url1> **https://git.oschina.net/shede333/swioslibary.git**
> <url2> **https://git.oschina.net/shede333/swscrollbar.git**
> <url3> **https://github.com/shede333/CoreAnimationTestSW.git**
>
> 首先，先增加第一个地址 `git remote add origin <url1>`
> 然后增加第二个地址 `git remote set-url --add origin <url2>`
> 增加第三个地址 `git remote set-url --add origin <url3>`
> ....依次类推
>
> 这样就完成了添加多个地址到origin库中了， 以后只要使用`git push origin master` 就可以一次性push到3各库里面了(使用`git push`也可)
>
> `git remote -v`:显示当前所有远程库的详细信息。

> **方法二：**修改./git/config，[remote "origin"]里面添加url地址即可，不必去执行git命令。

> **注意：**
>
> 使用`git push origin master`时，你可以push到origin的多个url地址，
> 但是使用 `git pull`时，只能拉取origin里的一个url地址(即fetch-url，如上图)，这个fetch-url默认为 **你添加的到origin的第一个地址**，
> 如果你想更改，只需要更改config文件里，那三个url的顺序即可，fetch-url会直接对应排行第一的那个utl连接。

