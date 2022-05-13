## 一、Git基本操作

### 配置信息

> 该处是用来提交时当作签名使用的

```
git config --global user.name "我的用户名"
git config --global user.email "我的邮箱"
#删除配置信息
git config --global --unset user.name
git config --global --unset user.email
```

注意：`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

### 工作区和暂存区

1. 工作区

   > 平时写代码的文件目录

2. 暂存区

   > `git add` 后提交暂存的地方

3. 版本库

   > `git commit` 后给你生成版本的地方,注意`push`是提交到远程仓库而不是版本库,请勿混淆

#### 版本库（Repository）

工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![](https://www.liaoxuefeng.com/files/attachments/919020037470528/0)

我们把文件往Git版本库里添加的时候，是分两步执行的：

第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

### 创建版本库

1.选择一个合适的地方，创建一个空目录

```
$ mkdir learngit
$ cd learngit
$ pwd
/Users/michael/learngit
```

`pwd`命令用于显示当前目录。在我的电脑上，这个仓库位于`/Users/michael/learngit`。

> 注意：如果你使用Windows系统，为了避免遇到各种莫名其妙的问题，请确保目录名（包括父目录）不包含中文。

2.通过 `git init `命令把这个目录变成 Git 可以管理的仓库

```
$ git init
Initialized empty Git repository in /Users/michael/learngit/.git/
```

瞬间Git就把仓库建好了，而且告诉你是一个空的仓库（empty Git repository），细心的读者可以发现当前目录下多了一个`.git`的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。

> 如果你没有看到`.git`目录，那是因为这个目录默认是隐藏的，用`ls -ah`命令就可以看见。

### 把文件添加到版本库

1.`git add`把文件添加到暂存区

1. 会将工作目录的修改,保存成git对象
2. 在工作目录修改几个文件,就会生成几个git对象(一个文件对应一个git文件)
3. 同一个文件,每次修改再add的时候都会生成一个新的git对象,是`增量`而不是覆盖
4. 所以说git是绝对安全的,就算我只存到暂存区没有提交 git也会给我保存

添加单个或多个文件：

```
 $ git add 要添加的或更改后的文件名 文件2 。。。
```

所有需要添加或已修改的文件

```
$ git add .
```

2.把暂存区文件提交到版本库

```
$ git commit -m "本次提交文件的说明"
```

`git commit`命令执行成功后会提示信息，例如：

1. `1 file changed`：1个文件被改动（我们新添加的`readme.txt`文件）
2. `2 insertions`：插入了两行内容（`readme.txt`有两行内容）。

跳过使用暂存区，直接把所有已经跟踪的文件提交到仓库

```
$ git commit -a -m "描述消息"
```

### 查看文件状态

我们已经成功地添加并提交了一个`readme.txt`文件，现在，是时候继续工作了，于是，我们继续修改`readme.txt`文件，改成如下内容：

```
Git是一个分布式的版本控制系统。
Git是一款免费软件
```

运行`git status`命令看看结果：

```js
$ git status
```

- `modified` ：文件已修改
- `Untracked` ：文件从未添加过

以精简模式展示文件状态

```
$ git status -s
```

使用`git diff`命令查看上次怎么修改的`readme.txt`：

```
$ git diff readme.txt 
```

提交修改和提交新文件是一样的两步

```
$ git add readme.txt
$ git commit -m "本次提交说明"
```

### 查看版本库提交历史

```
1. #按时间先后顺序列出所有的提交记录，最近的提交排在上面
$ git log 
空格键往下翻页 b向上翻页 q退出日志查阅

2. #只展示最新的两条提交记录
$ git log -2 

3. #在一行上展示最近两条提交历史的信息
$ git log -2 --pretty=oneline

4. #在一行上展示最近两条提交历史的信息，并自定义输出的格式
 $ git log -2 --pretty=format:"%h | %an | %ar| %s"
```

- `%h` 提交的版本号
- `%an`作者名字 
- `%ar`作者修订日期，按多久以前的方式显示 
-  `%s`提交说明

### 取消暂存的文件

```
$ git reset HEAD 要移除的文件名
$ git reset HEAD . // 移除暂存的所有文件
```

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[版本回退](https://www.liaoxuefeng.com/wiki/896043488029600/897013573512192)一节，不过前提是没有推送到远程库。

### 撤销文件修改

`git checkout`将在工作目录中对文件的修改撤销

```
$ git checkout -- 文件名
```

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

> `git checkout -- file`命令中的`--`很重要，没有`--`，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到`git checkout`命令。

### 移除文件

1.从 Git仓库和工作区同时移除 `index.js` 文件

```
$ git rm -f index.js
```

2.只从 Git仓库中移除` index.css`文件，但保留工作区的 `index.css`文件

```
$ git rm --cached index.css
```

### Git忽略文件

一般我们总会有些文件无需纳入Git的管理，也不希望它们总出现在未跟踪文件列表。在这种情况下，我们可以创建一个名为`.gitignore`的配置文件，列出忽略的文件的匹配模式。文件`.gitignore`的格式规范如下：

- 以 # 开头的是 注释
- 以 / 结尾的是 目录
- 以 / 开头 防止递归
- 以 ！开头表示 取反
- 可以使用 glob 模式 进行文件和文件夹的匹配

#### glob模式

glob模式：是指简化了的正则表达式

- 星号`*` ：匹配零个或多个任意字符
- `[abc]` ：匹配任何一个列在方括号中的字符（此案例匹配一个a或一个b或一个c)
- 问号`?` ：只匹配一个任意字符
- 在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如[0-9]表示匹配0到9的数字）
- 两个星号`**` ：表示匹配任意中间目录（比如`a/**/z`可以匹配`a/z`、`a/b/z`或`a/b/c/z`等）

`.gitignore` 文件的例子：

```
1. # 忽略所有的 .a 文件
*.a

2. # 但跟踪所有的 lib.a 即使你在前面忽略了 .a 文件
!lib.a

3. # 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

4. # 忽略任何目录下名为 build 的文件夹
build/

5. # 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

6. # 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf

```

### 版本回退

#### 回退到之前版本

在Git中，用`HEAD`表示当前版本，也就是最新的提交`1094adb...`（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

我们要把当前版本`append GPL`回退到上一个版本`add distributed`，就可以使用`git reset`命令：

```
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
```

看看`readme.txt`的内容是不是版本`add distributed`：

```
$ cat readme.txt
Git is a distributed version control system.
Git is free software.
```

> 使用上面命令回退，穿越过去想回到未来就只能根据`commit id`回来了，方法如下：

#### 回退到指定版本

```
# 在一行上显示所有的提交历史
$ git log --pretty=oneline

# 使用 git reset --hard 命令，根据指定的提交ID 回退到指定版本
$ git reset --hard <CommitID>

# 在旧版本中使用 git reflog --pretty=oneline 命令。查看命令操作的历史
$ git reflog --pretty=oneline

# 再次根据最新的提交ID ，跳转到最新的版本
$ git reset --hard <CommitID>
```

> <`CommitID>`版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。

总结：

- `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
- 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
- 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

### 添加远程库

```
$ git remote add origin 仓库地址
```

### 查看远程库信息

```
$ git remote -v
```

### 删除远程库

```
$ git remote rm origin
```

### 推送到远程库

第一次

```
$ git push -u origin main 
```

以后

```
$ git push origin main
```

