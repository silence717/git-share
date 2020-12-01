### 说在前面：设置一些常用的别名

1. 查看我们设置的一些命令
   
   ```shell
   cat .bashrc
   ```

2. 编辑
   
   ```shell
   vi .bashrc
   ```

3. 配置一些别名
   
   ```shell
   alias ga="git add"
   ```

4. 重新reload一下会生效
   
   ```shell
   source .bash_profile
   ```

5. 查看配置了哪些别名
   
   ```shell
   alias
   ```

### git 与 github 什么关系？

#### git

> open source version control software

#### github

- reposity hosting
- issue
- pull request
- forks

### 配置信息

#### 查看自己当前全局的配置

```
cd ~
la
cat .gitconfig
```

或者

```
git config --list
```

#### 常用设置name和email

```
git config --global user.name "fang.yang"
git config --global user.email fang.yang@shuyun.com
```

#### 查看当前设置是否生效

```
git config user.name
git config user.email
```

### git基础

当前目录使用git来进行管理，在当前文件夹下执行

```
git init
```

查看

```
cd .git
la
```

### 重命名文件 mv

```git
git mv <from_file> <to_file>
```


与以下3条命令效果一样：

```git
mv README.md README
git rm README.md
git add README
```
### 

### 查看记录日志 log

```git
// 查看所有记录信息
git log

// 添加数字查看最近 n 条
git log -2

// 添加 --stat 查看被修改过的文件情况
git log --stat -2

// 查看某个人的提交
git log --author=**

// 其余可配置，但是不常用的
git log  --[since, after, until, before, ...]=**
```

### 撤销操作

```git
// 文件add以后，执行reset可以取消暂存,只会影响暂存区
git reset HEAD <filename>

// commit之后发现msg错误或者忘记提交有些文件，使用合并提交
git commit --amend

// 危险命令：修改了文件，确定不想要了，使用 checkout 恢复为上一次提交的文件状态
git checkout -- <file>
```

# 

### 远程仓库的使用

```git
// 查看远程仓库简写
git remote
// clone一个仓库，会自动添加 origin 为remote-name的简写

// 参数 -v 显示远程仓库的 url
git remote -v 

// 添加
git remote add <remote-name> <url>

// 同步远程仓库
git fetch <remote-name>

// 从远程分支更新代码
git pull [remote-name] [branch-name]

// 为远程分支推送代码
git push [remote-name] [branch-name]
```

> 这块的`origin`与`master`分支一样(从2020年10月1日起改为`main`)，都是`git`默认生成的，与其他分支或者名称一样，没有什么特殊意义。

### 标签管理 tag

```git
// 显示所有的tag
git tag

// 新建一个tag
git tag -a <tag-name> -m "tag info"

// 显示某个tag信息
git show <tag-name>

// 将当前tag推送到远程
git push origin <tag-name>

// 删除本地的tag
git tag -d <tag-name>

// 危险：删除远程tag
git push origin :refs/tags/<tag-name>
```

### 

### 分支管理 branch

```git
// 查看本地所有branch
git branch

// 查看所有分支和它的最后一次提交
git branch -v

// 新建分支
git branch <branch-name>
// 切换到新建的分支
git checkout <branch-name>
git switch <branch-name>

// 新建分支、并且切换到当前分支
git checkout -b <branch-name>

// 推送到远程
git push origin <branch-name>

// 强制删除本地分支
git branch -D <branch-name>

// 删除远程分支
git push origin --delete <branch-name>

// 查看更多的分支信息
git branch -vv
// behind 2 落后于远程分支2条commit
// ahead 3 本地有2条commit没有push到远程上
// test 未跟踪任何远程分支
// feature/form 代表和远程一样
```


### 

### 暂存文件 stash

临时存储一些代码，正在开发的功能做了一半，需要切换分支，有的代码还在报错，没有通过`eslint`校验，因此`commit`是不能解决这个问题的，当然在这种情况下`commit` 代码是非常不合理的做法，因此可以使用`git stash`。

```git
// 查看状态
git status

// 使用stash命令
git stash 
git stash save

// 查看存储的代码
git stash list

// 重新应用stash的某一个，如果不添加stash-name，默认是最近的一条
git stash apply <stash-name>
// 应用完以后从栈上删除
git stash drop <stash-name>

// 直接应用并且删除它
git stash pop
```

### 

### merge 与 rebase

#### merge

将两个分支最新的提交与二者最近的共同的祖先进行三方合并，合并的结果生成一个新的记录并且提交。

#### rebase

找到这两个分支最近的共同祖先，然后对比当前分支相对于祖先的历次提交，提取相应的修改存为临时文件，将当前分支的目标基底指向最新的提交，然后将存储的临时文件的修改依次应用，这块产生的是新的`commit`，而不是原来的。

**如果其余小伙伴需要更新代码，一定要使用git pull --rebase 而不是git pull。**

#### 相同

效果都是一致的，将代码更新到最新，合并所有的提交。

#### 不同

`rebase`是将一系列提交按照原有次序依次应用到另一分支上，而合并是把最终结果合在一起。

#### 为什么 使用`rebase`？

之前发现有的同学并不会使用或者不知道有`stash`这个命令的存在，因此不断在自己的分支上面`commit`代码，这样造成最后的最后一个小小的功能产生非常多的`commit`记录，也有可能`commit`到最后都不知道填写什么`message`信息，因此出现非常多的又没有什么意义的commit记录，不便于后期问题的追踪与记录。

在内部项目中这么做其实也没什么关系，如果去开源社区提交一个`pr`，作者如果看到你这样的`commit`信息会觉得你非常不专业，可能都不会去看你的代码。

#### 我偏爱rebase的原因

提交历史更加整洁，有时候`merge`产生的`commit`记录对于我来说是毫无意义的。虽然实际上我们的开发是异步的，但是历史提交记 录看起来是一条直线，没有分叉，看起来像同步的效果一样。**强迫症患者推荐使用此种方式** 🙈🙈🙈

### 

### 常用的 git 命令

```git
// clone
git clone 

// 更新代码
git pull origin 

// status
git status
git status -s

// 添加文件到 staging area
git add 

// 添加msg信息
git commit -m "commit message"

// 更新repo
git push origin 

// 同步远程仓库信息
git fetch --all
```

### git pull 与 git fetch 区别

`git fetch` 只是从远程拉取本地没有的代码，不会修改工作目录中的内容。

`git pull`会拉取最新的数据，并且合并到当前分支。相当于`git fetch`命令执行完紧接着执行了一条`git merge`命令。

### 推荐可视化工具

**个人观点：作为程序员使用可视化工具并不可耻！**

![](/Users/silence/Library/Application Support/marktext/images/2019-09-16-11-24-11-image.png)

## 多人协作git版本开发

### 分支管理的意义？

- 一个人并行多个任务，相互之间不影响

- 多个人合作开发一个任务，不断提交，另外的人需要不停合并代码

- 害怕丢失代码，需要每天commit
  
  

### 达成分支命名的共识

`master`：现在改为`main`，作为主分支，代码永远与线上生产环境发布的保持一致

`pre-release`：预发布分支，作为上线前的回归测试使用的分支。

`dev`：测试分支，所有提交测试的功能代码全部合并到dev进行测试。

`feature/**`：开发功能的分支

`bugfix/**`：修复issue的分支



### 开发流程

1、新建分支，按照约定去命名，**需要注意的是这个分支是基于master新建**。

2、开发自己负责的功能（可以做一些模版之类的东西，使用脚本生成一些基础代码）

3、提交代码。

4、需要测试的时候合并代码到dev分支，提供给测试。



## commit注意事项

- one feature, one issue,  one commit

- message信息需要足够清晰准确，便于问题追踪，可以附带一些issue号啥的

- 开发一半需要切换分支怎么办？ 
  
  - 使用 git stash 将当前代码暂存起来，清理干净工作区，切换分支做其他开发，完成后切换回来使用 git stash pop即可继续做开发。
  
  - 直接commit，提交信息随便写，但是请不要push，不允许在一个功能里面一直commit，每次改一丢丢，然后随便编写一个message就提交，对于代码review和查追溯提交的时候没有意义。
  
  - 合理正确的使用amend commit，将本次提交合并到最后一次的提交里面去。
    
    

### 发布版本

1、 先做code review（如果有的话，没有直接忽略）

2、 合并当前分支代码到dev分支，不删除自己的当前分支

3、 发布 dev 分支到qa环境进行测试

4、 测试有问题，继续在原分支进行修复，重复 1，2，3的步骤

5、 没有问题，需要发布，提交`pull request`到`pre-release`分支进行回归测试。小问题：为什么不直接合并到`master`？

6、回归验证成功，从`pre-release`分支`pr`到`master`分支（使用pr的方式还是为了再做最后一次的check，有人去看一下代码，没有问题进行合并，谨慎）。

7、代码合并到`master`之后，需要对于当前的代码进行打`tag`处理，还是追踪问题，或者应付一些特殊的需求开发。

> 其中5和6步骤中建议使用`rebase`的方式进行代码合并，这样会使得整个代码的主线是一条线，强迫症患者推荐。 还有`pre-release`分支酌情考虑是否需要存在。



### 举个🌰



