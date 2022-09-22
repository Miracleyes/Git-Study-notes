# Git的学习笔记

-----
## 1.设置自己的全局账户信息
`git config --global`是全局的用户信息，文件位于`user/~/.gitconfig`
其中邮箱和姓名是必备的，用于区分不同的用户，设置方法如下

```cmd
$ git config --global user.email *****@qq.com
$ git config --global user.name *****
```
查看自己的全局信息`git config --global --list`
查看某条全局信息`git config --global user.name`


-----
## 2.创建存储库
**存储库** 是一个文件夹，文件夹和子文件夹里的所有代码文件都会被`.git`托管和跟踪
选定一个目录右键运行`git bash`输入

```cmd
git init
```
这个目录会初始化成一个 **存储库** ，其中增加了`.git`文件夹，该文件夹不可手动修改
>**存储库** 由三个部分组成：
**工作区**`->add->`**暂存区**`->commit->`**版本区**
其中**暂存区** 和 **版本区** 位于`.git`文件夹中， **工作区** 是用户自己的内容

-----
## 3.存储库的常用操作

将 **工作区** 的文件添加到 **暂存区**
```cmd
git add <file> 
git add <file1> <file2> # 添加多个文件
git add . # 添加存储库中的全部文件
git add ./<dict> # 添加文件夹下的全部文件
```
>无论是文件的增加、修改、删除都可以使用`add`命令

将 **暂存区** 的文件提交到 **版本区**
```cmd
git commit
git commit <file>
git commit -m <massage>
git commit <file> -m <massage>
```
>使用`git commit`提交全部修改，会弹出一个VIM编辑器，用于记录这次改动的备注信息（commit massage for the changes），其中有一些已经写好的内容，不过带有`#`符号的内容会自动删去，第一行的空行，用于填写自己的备注
>按`i`进入 **插入模式** 这时可以编辑文本内容，按`esc`退出 **插入模式** ，在下方输入`:wq`按回车可以 **保存并退出**
>使用带有`-m`的命令可以在不打开编辑器的情况下快速保存信息，`massage`对应的内容应加双引号

如果你在 **工作区** 修改或新增了文件，可以查看当前的文件状态
```cmd
git status
git status -s # 简化版
```
>下面列出了一些不同的前缀符号所代表的含义，后续有时间会添加更多
>```cmd
>?? filename # 仅存在于工作区
>A  filename # 已添加到暂存区
>```

详细查看文件的修改内容
```cmd
git diff
git diff <file>
```
>修改完成后还需要重新执行命令再次提交才能生效
>```cmd
>git add .
>git commit -m <massage>
>```

如果想要查看提交到 **版本库** 的历史记录
```cmd
git log
git log --oneline # 简化版
git blame <file> # 单个文件的修改日志
```
>日志里包含了每次提交的版本号和备注信息

将 **版本区** 的文件恢复到 **暂存区**，**工作区** 不受影响
```cmd
git reset HEAD
git reset HEAD <file>
```
>这个操作通常用于清空暂存区的缓存

将 **暂存区** 的文件恢复到 **工作区**
```cmd
git restore .
git restore <file>
```

在不同的区域删除文件或文件夹
```cmd
git rm <file> # 删除工作区文件
git rm -r ./<dict> 
```
> 使用`-r`表示递归的删除文件夹里的全部文件

```cmd
git rm --cached <file> # 删除暂存区文件
git rm -f <file> # 删除工作区和暂存区文件
```

重命名或移动一个文件、目录、软连接
```cmd
git mv <old_name> <new_name>
git mv -f <old_name> <new_name> # 强制
```
>加入`-f`表示强制移动和重命名，如果有同名文件会被直接替换

-----
## 4.回退版本
版本回退是一个非常重要的内容，当代码出现严重问题时使用 **版本区** 中保存好的代码复原，首先要明确`HEAD`指针的作用，它指向了一个当前分支的最新版本，所以`HEAD = master or (other branch name)`，也就是说是下文中它可以和分支名替换
回退命令是`git reset [--soft | --mixed | --hard] [HEAD]`
当不加后缀或使用`--mixed`后缀时，只把文件回退到暂存区，工作区保持不变，这和之前的一个操作很像

```cmd
git reset HEAD^^^
git reset HEAD^^^ README.txt
git reset version_id
```
>其中`^^^`表示回退的代数，还可以用`~num`的方式表示，举例如下
>```cmd
>HEAD # 回退到当前版本
>HEAD^ # 回退到上一版本
>HEAD^^ # 回退到上上版本
>HEAD~0 # 回退到当前版本
>HEAD~1 # 回退到上一版本
>HEAD~2 # 回退到上上版本
>```
>因为只回退到了暂存区，所以这通常要搭配以下命令将工作区代码恢复
>```cmd
>git restore .
>```

使用`--soft`后缀，回退暂存区和版本区到某个版本，不改变工作区的内容
```cmd
git reset --soft HEAD^^^
git reset --soft version_id
```
使用`--hard`后缀，回退所有内容，包括工作区
```cmd
git reset --hard HEAD^^^
git reset --hard version_id
```
>使用`--soft --hard`是相对危险的，因为这样的版本回退方式修改了 **版本区** `HEAD`指针的位置，这样会导致部分版本无法找回，如果没有远程备份的话要谨慎使用

回退到远程版本一致的库，后续会有详解
```cmd
git reset --hard origin/master
```

-----
## 5.分支管理
创建和`master`分支完全独立的子分支可以在不影响主线的情况下继续工作，或者在不同开发环境下使用不同的分支工作
列举现有的分支

```cmd
git branch
```
创建和切换分支
```cmd
git branch <branch_name> # 创建
git checkout <branch_name> # 进入
git checkout -b <branch_name> # 创建并进入
```
>创建分支会复制当前分支 **版本区** 的内容，切换分支时会自动根据分支 **版本区** 内容替换 **工作区** ，所以多个分支只需要一个目录

合并分支到主分支
```cmd
git merge <branch_name>
```
>将分支`branch_name`下的文件会自动更新到`master`分支下，其中文件的增加和删除会同步，当出现两个分支文件内容冲突时，会提示手动修改不同的内容

合并冲突
```cmd
cat <conflict_file> # 查看冲突文件
vim <confilct_file> # 修改冲突文件
git diff # 查看是否一致
git add <confilct_file>
git commit -m <massage>
```
>执行冲突过程中是共同位于主分支和合并分支之间的，两个分支的不同都可以看到，提交完后会自动回到主分支

删除分支
```cmd
git branch -d <branch_name>
```

-----
## 6.版本标签
当代码达到了一个比较重要的阶段，我们可以将这个版本永久的保存下来，也就是一个文件快照功能，这个操作叫做打标签，这个版本不光具有了`commit`版本号，同时有了用户自定义的版本号
```cmd
git tag -a v1.0
git tag -a v1.0 <commit_version> # 追加标签
git tag # 查看标签
```
>使用`-a`命令时，需要附带一段标签备注，就像是提交时的`commit`一样，如果使用`-m`可以在不打开编辑器的情况下输入标签备注，注意`massage`的部分要加双引号
>```cmd
>git tag -a <tag> -m <massage>
>git tag -s <tag> -m <massage> # PGP签名标签
>```

-----
## 7.使用远程仓库
常用的远程仓库包括`github`或是国内的`gitee`，如果只是使用别人的仓库那不需要注册账号
直接使用`git clone`从现有的远程存储库中拷贝项目
在当前目录下克隆远程仓库

```cmd
git clone <url> <name>
```
`url`为仓库地址，`name`为用户自定义的本地仓库名称

当想要获取更新时，可以输入以下命令

```cmd
git pull
```
当管理自己的项目时，使用以下命令推送新的版本到服务器
```cmd
git push
```

>### 小提示：
>由于`github`的服务器在国外，因此在访问时可能会出现超时访问失败的现象，如果你没有科学上网工具，并且无法`ping`通`github.com`，可以尝试以下方法：
>`windows`用户打开`C:\Windows\System32\drivers\etc\hosts.ics`文件，在其末尾加入以下三行并保存，其中第一行的地址可以通过 [Github Addr](https://ipaddress.com/website/www.github.com) 获取
>
>```cmd
>140.82.112.4 github.com
>199.232.5.194 github.global.ssl.fastly.net
>54.231.114.219 github-cloud.s3.amazonaws.com
>```
>通过这个方法可以解决无法`ping`通`github.com`的问题
>如果你已经有科学上网工具的话，也可能会出现完全无法访问的问题（比不开代理更严重），此时>的解决方法是：
>在命令行配置`git`
>```cmd
>git config --global http.proxy http://127.0.0.1:11223
>git config --global https.proxy http://127.0.0.1:11223
>```
>这里`http://127.0.0.1:11223`是我的网络代理端口，根据需要换成你的即可
>当不使用代理时，取消配置
>```cmd
>git config --global --unset
>git config --global --unset
>```


下面主要以`github`为例介绍如何创建自己的远程仓库，`github`上无论是公有库还是私有库都是完全免费的，可以选择是否开源，首先需要准备的是与远程服务器之间的连接，这里使用的是`SSH`加密连接，这需要一个`SSH KEY`，在命令行输入以下代码
```cmd
$ ssh-keygen -t rsa -C "youremail@qq.com"
```
>然后连续按三次回车，不设置密码，将`user/~/.ssh/id_rsa`中的`KEY`拷贝到`github-setting-sshkeys-new_ssh_key`中

尝试在命令行连接`github`远程服务器
```cmd
$ ssh -T git@github.com
```
>连接成功的提示`Hi yourname! You've successfully authenticated, but GitHub does not provide shell access.`

之后需要在`github`新建一个自己的 **远程库** ，建立完成后会有简单教程说明如何上传自己的库，这里要详细讲解的是`origin`这个变量的作用，它是指向你远程库网址的指针，如果你只使用`github`上的这一个网址，那就没有重命名的必要，如果本地库对应了多个远程库地址，需要重命名区分这些地址了
当准备上传时，需要首先声明远程库如下

```cmd
git remote add <origin> <url>
git remote # 查看远程库
git remote -v # 详细版
```
使用以下命令上传本地分支到已经定义好名称为`origin`的远程库
```cmd
git push -u origin <branch>
```
>在2021年8月推出了令牌功能，需要进行一系列额外的设置才能使用`push`，参考[CSDN](https://blog.csdn.net/weixin_41010198/article/details/119698015)
上传成功后已经可以在网站上看到自己的代码了，但使用`push`命令并不会将`tags`上传，需要执行以下命令单独上传
>```cmd
>git push origin <tag> # 单独上传一个tag
>git push origin --tags # 上传所有本地tags到远程
>```

拉取远程仓库的更新，并合并到本地分支上
```cmd
git fetch <origin> <branch>
git merge <origin>/<branch> 
git fetch <origin> <b1> <b2> <b3> 
git merge <origin>/<b1> <b2> <b3> 
```
>注意这两个命令必须连续执行，下面两行代码表示拉取合并多个分支时的写法

删除远程仓库
```cmd
git remote rm <origin>
```

## 参考文献：

[官方教程]: https://git-scm.com/book/zh/v2
[配置host文档解决超时问题]: https://blog.csdn.net/Gherbirthday0916/article/details/125504214
[配置代理]: https://blog.csdn.net/Strive_For_Future/article/details/120962003?spm=1001.2101.3001.6650.1&amp;utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-120962003-blog-121664227.t0_edu_mix&amp;depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-120962003-blog-121664227.t0_edu_mix&amp;utm_relevant_index=2


-----
## @2022-09 @yubao 
