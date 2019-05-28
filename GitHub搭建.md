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

> * 到C:\Users\benson\.ssh打开id_rsa.pub，复制全部内容。
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

