# 在git Bash中提交的时候遇到的问题

## 1.git add之前存在提交失败的大文件时

<img src="../images/image-20230119182936767.png" alt="image-20230119182936767" style="zoom:80%;margin-left:0px;" />

翻译：另一个git进程似乎正在这个存储库中运行，例如。“gitcommit”打开的编辑器。请确保所有流程请终止，然后重试。如果它仍然失败，git进程可能早在该存储库中崩溃：手动删除该文件以继续。

解决方法：

1.误操作比如(git commit) 直接提交 导致检索不到要保存的信息他就会锁定当前的文件

2.git下的`index.lock`文件，在进行某些比较费时的git操作时自动生成，操作结束后自动删除，相当于一个锁定文件，目的在于防止对一个目录同时进行多个操作。有时强制关闭进行中的git操作，这个文件没有被自动删除，之后就无法进行其他git操作，必须手动删除。
<img src="../images/image-20230119183149200.png" alt="image-20230119183149200" style="zoom:80%;margin-left:0px;" />

删除之后就能够正常使用git指令进行提交了。

## 2.如果远端的github仓库存在改变时

首先使用

```bash
git pull
```

将仓库的最新情况拉取下来。

如果出现一下场景，说明更新之后有冲突，要查看之后再决定。

解决方法：

只需要正常:wq 保存并退出。即可正常使用git操作。

<img src="../images/image-20230119183511386.png" alt="image-20230119183511386" style="zoom:80%;margin-left:0px;" />



## 3.git中使用python

一般直接在git bash中直接输入[python](https://so.csdn.net/so/search?q=python&spm=1001.2101.3001.7020)后会无反应

 

1. 可以在python前添加 winpty

   ```bash
   winpty python
   ```

<img src="../images/image-20230111135757188.png?" alt="image-20230111135757188" style="zoom:80%;margin-left:0px;" />

2. 每次在python前添加winpty有些麻烦，我们可以执行指令生成一个名为“python”的别名

   ```bash
   alias python='winpty python.exe'
   ```

<img src="../images/image-20230111135835029.png?" alt="image-20230111135835029" style="zoom:80%;margin-left:0px;" />



## 4.git commit大于100MB文件报错

当我们在使用git commit提交文件的时候如果大于100MB会报错，因为git hub一个接收文件的大小最大时100MB。

解决方案：

网上的解决方案：https://www.yisu.com/zixun/620433.html

亲测有效的：使用git reset进行版本回滚

```python
#查询提交日志，获取提交head
git log

#撤销commit到指定的版本，本地修改的文件不会变动
git reset --soft 92e076f29644031bf8430d63bbf1f9f85b625d5c
```

然后再正常使用git操作即可。

## 5.报错  Fa iled to connect to github.com port 443: Timed out或者errno 10054

原因：一般是这是因为服务器的SSL证书没有经过第三方机构的签署，所以才报错

解决方案：解除ssl验证后，再次git即可

```bash
git config --global http.sslVerify false
```

如图：

<img src="../images/image-20230203005230455.png" alt="image-20230203005230455" style="zoom:70%;margin-left:0px;" />

## 6.上传大文件

1. .首先下载git-lfs 需要配置lfs密钥(注意不是ssh的密钥)的请看https://github.com/git-lfs/git-lfs

   ```bash
   git lfs install

2. 安装好后进入本地仓库目录，执行下面的命令

```bash
	git lfs track "filename"
```



3.  `filename`是需要上传的大文件。执行完命令后会发现目录下生成了一个".gitattributes"文件，文件内记录了我们要上传文件的信息。只有先把".gitattributes"传上去，才可以上传大文件。

   ```bash
   git add .gitattributes
   git commit -m "submit file"
   git push

4. 上传完毕后，开始上传大文件。

   ```bash
   git add filename
   git commit -m "add file"
   git push 

5. 出现下面的进度条，就说明文件正在上传中

   Uploading LFS objects:   0% (0/1), 2.1 GB | 720 KB/s
   <font color="blue">需要注意的是:</font>

  通过git-lfs上传文件是**有空间限制的**，免费用户如果上传的文件超过了1G，账号就会被冻结，所以大家在上传前一定要检查一下自己还剩多少空间。

  点击自己的头像，进入"Settings"，选择Billing"就可以看到自己还剩多少空间。

<img src="../images/image-20230204230611369.png" alt="image-20230204230611369" style="zoom:70%;margin-left:0px;" />

如果发现自己空间不足，可以删去一些大文件或者购买更多的空间。

如果在上传过程中出现如下报错：

batch response: Git LFS is disabled for this repository.
Uploading LFS objects:   0% (0/1), 0 B | 0 B/s, done
就说明你的账号被冻结了，需要在GitHub后台提交解封申请。

工作日一般几个小时就会帮你把账号解封，解封后才可以继续上传大文件
