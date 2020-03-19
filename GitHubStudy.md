## 1、检查用户名和邮箱是否配置

> * git *config* --*global*  --*list* 

## 2、配置用户名和邮箱

> * git config --global user.email "这里换上你的邮箱"
> * git config --global  user.name "这里换上你的用户名"

## 3、生成秘钥

> * ssh-keygen -t rsa -C "这里换上你的邮箱"

执行命令后需要进行3次或4次确认：

确认秘钥的保存路径（如果不需要改路径则直接回车）；
如果上一步置顶的保存路径下已经有秘钥文件，则需要确认是否覆盖（如果之前的秘钥不再需要则直接回车覆盖，如需要则手动拷贝到其他目录后再覆盖）；
创建密码（如果不需要密码则直接回车）；

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

把刚才创建的仓库的地址添加到本地。

> * git remote add origin https://github.com/bensondawn/bigdata.git

## 8、更新服务器数据到本地

> * git pull

## 9、提交本地资源

> * git add .
> * git commit -m "内容描述"
> * git push origin master（第一次push要用git push -u origin master）

## 10、push到GitHub服务器

> * git push origin master

## 11、免去每次都要输入用户名和密码的麻烦

进入项目目录输入：

> * git config --global credential.helper store

### 12、其它

如果想把GitHub上的仓储下载下来，并随时更新可以这样：

> * git clone https://github.com/bensondawn/bigdata.git
> * git pull（以后直接用改命令从GitHub上更新就行了！）
> * 推送分支git push origin master(分支名称)，git push origin dev(分支名称)
> * rebase操作可以把本地未push的分叉提交历史整理成直线；
> * rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

## 13、常用命令

> * git add 文件夹/ 
> * git add *.文件类型
> * git clone -b dev https://github.com/bensondawn/bigdata.gitl 克隆分支dev下的代码。
> * git branch dev 新建分支dev
> * git branch -a 查看所有分支
> * git push origin dev 提交代码到dev分支
> * git fetch origin dev 设定当前分支的 FETCH_HEAD 为远程服务器的dev 分支；此时不会在本地创建dev分支，这个操作是git pull origin dev的第一步。
> * git checkout -b dev 在本地创建分支dev并切换到该分支
> * git pull origin dev 把gitLab上dev分支上的内容都拉取到本地了
> * git checkout -- filename 撤销文件的修改至上一次提交的状态。

## 14、删除github上的文件或文件夹

> -  git rm -r --cached 目录名或者文件的绝对路径
> -  git commit -m '描述'
> -  git push -u origin master

## 15、在git上撤销上一次commit的文件或目录

> - 1、git stash	把工作区和暂存区的状态存储起来，并将当前工作区和暂存区重置为最近一次提交后的状态(commit)。
> - 2、git reset HEAD~  保证你原本的修改还在，也能撤销本次提交失误。
> - 3、git stash pop  将储藏内容弹出并应用，储藏内容会被从储藏列表中删除。

## 16、在git上撤销上一次add的文件或目录

> - git rm -r name --cached	name可以是.也可以是你git add 某个目录或文件。