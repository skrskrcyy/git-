# 前言

&emsp;Git是一个开源的分布式版本控制系统。其核心就在于**版本控制**。


&emsp;在实际编码过程中，我们往往会忘记上次对文件的修改内容。若是刚刚修改的还好说，撤销操作即可。但若这是你昨天做的修改并关闭了IDE呢？所以我们需要有一个可以回退版本的工具。

&emsp;Git还有另一个作用便是**多人协作开发**——将代码文件上传至云端，partner可以随时下载至自己的电脑，又可以随时做好修改再上传至云端。


<br>
<br>

&emsp;我将学习Git分为三块，大家跟着我的步骤做一遍也就会了。
1. **配置Git及配置Github**
2. **本地Git操作**
3. **远程推送操作**

---
<br>
# 配置Git及配置Github

&emsp;首先分清楚Git和Github的作用分别是什么。你们可以理解为——**Git是运输的小车，Github是仓库**，我们将东西装上车，然后让他送到仓库去。

&emsp;要想使用Git及Github服务，必须得创建Github账号，Github官网：<a href='https://github.com/' target='_blank'>https://github.com/</a>。

&emsp;创建好账号后，再new一个repository。然后，我们接着配置Git。

&emsp;我们需要先下载Git工具，这是Git官网：<a href='https://git-scm.com' target='_blank'>https://git-scm.com</a>,根据自己计算机的系统下载即可。

&emsp;而后我们需要在本地创建ssh key：

&emsp;`$ ssh-keygen -t rsa -C "your_email@youremail.com"`

&emsp;后面的your_email@youremail.com改为你在github上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在~/下生成.ssh文件夹，进去，打开id_rsa.pub，复制里面的ssh key。

&emsp;回到github上，点击右上角头像，选择Settings（账户配置），选择左边栏的SSH and GPG keys,点击new SSH key，title随便填，粘贴上你电脑上生成的key。

&emsp;配置完ssh key后，我们还需要配置username和email：

```
	$ git config --global user.name "your name"
	$ git config --global user.email "your_email@youremail.com"
```

&emsp;至此，Git的配置和Github的配置已经结束了，你已经有了可用的Git和可用的Github。接下来我们来学习如何进行本地Git操作。

---

<br>
# 本地Git操作

&emsp;首先要学习几个概念——**工作区，暂存区，版本库**。他们之间的关系如下图

![引用自Runoob](https://img2018.cnblogs.com/blog/1564471/201902/1564471-20190213195338685-156335578.png)

&emsp;这三个区域其实没有什么神秘的，他们都是个文件夹（为树结构）。他们的作用分别为
- **工作区：实际持有的文件夹，即你可以在 我的电脑 里找到的文件夹。用于实际项目开发。**
- **暂存区：他就如同一个缓存，可以频繁地将工作区中的文件发送到其中，从而使发送过来的修改后的文件覆盖暂存区中的原文件。**
- **版本库：暂存区将他的文件发送到版本库中，用于发送到Github仓库中，作为最新版本。**


&emsp;这三个区域之间的关系及功能搞清楚了，接下来我会先说最重要的Git操作，学会这些操作后就可以直接进行生产活动了。而后，我再陈列其他较重要的的Git操作及更深层的辨析。

&emsp;首先，创建一个文件夹做为工作区，不过要想这个文件夹成为工作区，需要cd到该文件夹，输入`git init`哦！这样其才是工作区，而不是普通的文件夹，切记。

&emsp;然后，我们在文件夹中随意进行修改，如创建txt文件等。我在文件夹中创建了一个experiment.txt文件，并写入内容。根据上图，我们需要将工作区的文件发送到暂存区，使用`git add -A`，该命令的意义为将所有的改动发送到暂存区中。（实际上还有很多种git add方法，但一般来说git add -A已经足够。）

&emsp;再然后，将暂存区的文件发送到版本库中，使用`git commit -m 'comments'`，其中comments为你对这个版本的说明文字。

&emsp;好了，本地Git操作已经结束了，是不是非常简单？只要三步就好了，我们来复习一遍。
```
	$ git init
	$ git add -A
	$ git commit -m 'comments'
```

<br>
<br>

&emsp;基础的讲好了，我们接下来进入更深的层次，如果觉得已经够用的，可以跳过这一阶段。

## 为什么要有暂存区

&emsp;为什么要有暂存区，我们每次将实际改动的文件直接发送到版本库不就好了？这样也没有任何问题啊，还省了一步。

&emsp;其实你想的没有错，这样是没有问题的。不过这与软件开发的一大基础原则背道而驰了——单一职责原则。若是没有暂存区，那么版本库就得既负责提交版本，又得负责保存修改，使得Git一点也不优雅。这是两个完全不同的功能，应该由两个模块去分别完成。

## 分支

&emsp;引用自https://guides.github.com/activities/hello-world/ 的一句话：
> Have you ever saved different versions of a file?
> 
> Branches accomplish similar goals in GitHub repositories.

&emsp;意思就是：你是否为一个文件备份了许多不同版本，在GitHub中分支完成同样的目标。

&emsp;我们之所以为一个文件备份好多份文件，就是担心我们不备份的话，代码打着打着，突然发现出现了bug，而我们却实在改不过来了，最简单的方法就是回到没有修改前的状态，但我们已经回退不回去了。这样的话，真的就是心态爆炸。

&emsp;所以我们备份，但是最终我们发现，桌面一团糟。

&emsp;所以分支就是这样的作用——**绝缘特性开发**，简而言之就是，给你一个调试bug的分支，同时不影响其他分支。

&emsp;默认情况下，都有一条主分支——master分支

&emsp;以下是有关分支的操作：
```
	$ git branch 分支名 //用于创建新的分支，该分支的内容就是当前主分支（master）的内容。
	$ git checkout 分支名 //切换到某一分支上
	$ git branch -d 分支名 //删除某一分支
	$ git merge 分支名 //将该分支合并到当前分支上
```

&emsp;要注意一点的是，`git checkout 分支名`切换分支后，工作区、暂存区、版本区也都对应的全都改变，即**一个分支对应一套独立的工作区、暂存区、版本区**，其中我们可以直观地看到的是，切换分支后，工作区（也就是你电脑里的那个文件夹）的文件内容发生了改变。

&emsp;其中，我要讲的是`$ git merge 分支名`这一操作。

### 分支合并（$ git merge 分支名）

&emsp;master是我们的主分支，而new是我们新建的分支，用于调试代码。而当我们在该分支上调试成功了，我们是不是应该将该分支的内容复制到主分支上？毕竟该分支new仅仅是我们为了调试bug而创建出来的，master才是真正的主分支。

&emsp;所以分支合并其实就是**将在B分支中修改的文件覆盖掉A分支中对应的文件**，未修改的文件不覆盖。

&emsp;那么，又有一个问题来了——如果A分支和B分支都修改了这个文件呢？那么应该听谁的呢？

&emsp;首先，这个情况我们称为**分支合并冲突**，其次，**当发生分支合并冲突时，他会将两个分支的修改都保存下来，但是要求你得手动修改文件后才能commit提交。**

&emsp;其中，Git用<<<<<<<，=======，>>>>>>>标记出来自不同分支的内容。

## 回退版本

&emsp;之前我们说过，Git的核心在于**版本控制**。那么就自然要求Git得有版本回退的功能。

&emsp;何为版本？**每次commit到版本库后就是一个版本。**

&emsp;首先，**第一步——`git log`查看历史版本**：

![](https://img2018.cnblogs.com/blog/1564471/201902/1564471-20190213230103684-1889293130.png)

&emsp;第二步——**截取commit字样后的任意字数，用作版本标识。如10feb9882453e57。**

&emsp;第三步——**`git reset --hard 第二步截取的版本标识`回退版本。**

&emsp;就这样，我们就成功地回退版本啦！终于不用担惊受怕自己一不小心脑子犯浑commit了一个错误的版本却无法恢复了。

&emsp;但由此又出现了一个问题，当你回退到以前的版本，那么在以前的版本后的所有版本就都无法通过`git log`查找出来了。

&emsp;对此，我们通过`git reflog`来解决。其意义为**查看所有分支的提交操作，其结果也含有版本标识。**

&emsp;<p font-weight='lighter' style='color:green'>小技巧：如果你想要知道各个版本文件内容修改的话，使用`git log -p`命令即可。<p>

## 查看修改内容

&emsp;有时，我们想要知道还有什么文件需要add/commit，或是自己究竟做了什么样的修改内容，那么我们可以通过`git status`和`git diff`来查看。

### git status

&emsp;`git status`的意义为**检索本地仓库的文件更新状态。**

&emsp;这说的太笼统了，执行该命令显示的内容如下：
1. **已修改的，未暂存的文件名称**
2. **已暂存，为提交的文件名称**
3. **未追踪到的文件**

&emsp;其中，要解释一下**未追踪文件**的意义：
- 已追踪文件：在仓库之前的版本快照中包含文件的记录，在用户工作一段时间过后，这些文件同样能被追踪到（如文件的修改和删除）。可以理解为，原来就有的文件，非新增文件。
- 未追踪文件：一些文件的新增

### git diff

&emsp;`git diff`的意义为**查看所有文件进行的具体的修改，添加到暂存区则看不了**。

&emsp;与其对应的是`git diff staged`，意义为**查看添加至暂存区所有的内容修改**

---
<br>
# 远程推送操作

&emsp;当你学到这里的时候，离成功就只剩一步之遥了，坚持下去，加油！

&emsp;现在我们在本地仓库中的版本库中准备好要推送到远程仓库的版本文件了，可是我们现在连要发送到哪一个远程仓库的地址都不知道，所以第一步是——**建立本地仓库与远程仓库的连接。**

## 建立连接

&emsp;建立连接的方法有一下几种：
1. **`git remoted add 远程主机名 git@github.com:GitHub名称/远程仓库名.git`。这个命令用于将本地仓库和远程仓库连接起来。**
2. **`git clone git@github.com:GitHub名称/远程仓库名.git`。会直接连接远程仓库，并将远程仓库的文件夹克隆到本地目录中。**

&emsp;连接成功了之后，便可以进行push和pull操作了。我们要清楚的是，**push和pull也是一种分支合并操作**，只不过不再是本地的分支之间进行合并，而是远程分支和本地分支之间的合并。

## git push

&emsp;该命令的意义是**将本地版本库的分支推送到远程版本库的对应分支进行合并**。

&emsp;`git push`的一般形式为`git push <远程主机名> <本地分支名>:<远程分支名>`。如git push origin master:master。

&emsp;其中，一旦使用上面两种方法连接远程仓库成功，则远程仓库名称为origin。

## git pull

&emsp;该命令的意义是**将远程仓库的分支下载至本地，并将其合并到本地对应分支**，其实这一个命令是`git fetch`和`git merge 分支名`的缩写。

&emsp;`git pull`的一般形式为`git pull <远程主机名> <本地分支名>:<远程分支名>`。如git pull origin master:master。


<br>
<br>

&emsp;好了，这些就是远程推送操作的基本操作。不过，在实际的操作过程中还是会遇到好多好多棘手的问题，大家可以先进行实验，在实验的过程中遇到问题了，再来对比接下来的内容。

&emsp;接下来，我来为大家列举一些远程推送操作中比较经常出现的问题。**注意，接下来我都是基于只远程连接一个远程仓库的情况。**

## 分支追踪

&emsp;大家可能看到网上的其他教程中，只使用`git push`便将版本上传，而我们这样做却往往会报错（但我前面写的第二种连接方式不会报错），为什么呢——**因为没有使本地分支跟踪到远程分支**。

&emsp;大家可以先看下这个链接的内容：<a href='https://www.cnblogs.com/qianqiannian/p/6008140.html' target='_blank'>https://www.cnblogs.com/qianqiannian/p/6008140.html</a>。

&emsp;其中清楚地说明了，要想省略参数的话，就得使分支之间建立追踪关系。

&emsp;如何建立追踪关系呢——**`git branch --set-upstream-to=远程主机名/远程分支名 本地分支名`**。如此，便可将本地分支追踪到远程分支，也就可以省去部分参数。

<span style='color:green'>如果想要知道本地仓库和远程仓库之间所有的追踪关系的话，使用**`git branch -vv`**。如果你想要知道远程都有什么分支，使用**`git branch -r`**。</span>

## 出现! [rejected] master -> master  (non-fast-forward)

&emsp;出现这种情况的原因：
1. **如果是push时报的错，则是远程仓库有本地仓库没有的文件或者远程仓库有本地仓库没有的修改内容，则需要先pull下来，再push。**
2. **如果是pull时报的错，则是本地仓库有远程仓库没有的文件或者本地仓库有远程仓库没有的修改内容，则需要先push上去，再pull。**

&emsp;**如果git pull无效的话，就使用`git pull origin 远程分支名 --allow-unrelated-histories`便可解决**

&emsp;当push的时候，还会有种报错的情况——分支合并冲突。解决方案是**先pull，在本地解决完分支冲突的问题后再push。**

<span style='color:green'>小知识点</span>
<br>
<span style='color:green'>1.当你在本地新建一个分支，然后直接上传是可行的，会自动在远程仓库中新建一个同名分支，并将内容上传至此。</span>
<br>
<span style='color:green'>2.你从远程仓库中pull下来的内容，其工作区，暂存区，版本库都是“干净“的，即不需要你将新增的内容再add到暂存区，再commit到版本库</span>
<br>
<span style='color:green'>3.切记，分支合并（包括push和pull）不是一个分支覆盖另一个分支，而是一个分支的修改内容增添到另一个分支。</span>
<br>

<br>
<br>

&emsp;全文结束。

---

# 参考资料

1. git add documents:<a href='https://www.git-scm.com/docs/git-add' target='_blank'>https://www.git-scm.com/docs/git-add</a>
2. 为什么要先 git add 才能 git commit ？<a href='https://www.zhihu.com/question/19946553' target='_blank'>https://www.zhihu.com/question/19946553</a>
3. GitHub简明教程：<a href='http://www.runoob.com/w3cnote/git-guide.html' target='_blank'>http://www.runoob.com/w3cnote/git-guide.html</a>
4. git status和git diff:<a href='https://www.cnblogs.com/kkz-org/p/9313205.html' target='_blank'>https://www.cnblogs.com/kkz-org/p/9313205.html</a>
5. git 远程仓库和本地仓库建立连接：<a href='https://blog.csdn.net/wowoniuzailushang/article/details/78545971' target='_blank'>https://blog.csdn.net/wowoniuzailushang/article/details/78545971</a>
6. Git push：<a href='https://www.cnblogs.com/qianqiannian/p/6008140.html'>https://www.cnblogs.com/qianqiannian/p/6008140.html</a>
7. git中出现“non-fast-forward”errors时的终极解决方案：<a href='https://blog.csdn.net/kiddd_fu/article/details/78247290' target='_blank'>https://blog.csdn.net/kiddd_fu/article/details/78247290</a>
8. 使用Git的Push出现rejected - non-fast-forward错误：<a href='https://www.cnblogs.com/xiaoyunxia/p/5975169.html' target='_blank'>https://www.cnblogs.com/xiaoyunxia/p/5975169.html</a>
