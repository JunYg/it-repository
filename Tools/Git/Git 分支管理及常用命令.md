# `Git` 架构、分支管理及常用命令

`Git`，分布式版本控制系统

`Git` 帮助文档地址：

- [Pro Git](https://git-scm.com/book/en/v2) / [中文](https://git-scm.com/book/zh/v2)
- [Git 指令文档](https://git-scm.com/docs)
- windows：`file:///{git install dir}/mingw64/share/doc/git-doc/`
  - `--help` 直接跳转
- linux： `git [command] --help`

[Git 动画教程](https://learngitbranching.js.org/)

## Git 架构

<image src="https://upload-images.jianshu.io/upload_images/3670077-b97c6a8e14d1dafa.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp" style="width: 600px;"/>

### 工作区 `working`

工作区，当前工作的区域。

对于`git` 而言，就是的本地工作目录。

工作区的内容会包含提交到暂存区和版本库（当前提交点）的内容，同时也包含自己的修改内容。

### 暂存区 `staging`（索引区 `index`）

是我们把修改提交版本库前的一个过渡阶段。

查看`Git` 自带帮助手册的时候，通常以`index` 来表示暂存区。

在工作目录下有一个`.git` 的目录，里面有个`index` 文件，存储着关于暂存区的内容。

`git add` 命令将工作区内容添加到暂存区。

### 本地仓库 `local`

版本控制系统的仓库，存在于本地。

当执行`git commit` 命令后，会将暂存区内容提交到仓库之中。

在工作区下面有`.git` 的目录，这个目录下的内容不属于工作区，里面便是仓库的数据信息，暂存区相关内容也在其中。

#### 远程仓库副本

可以理解为存在于本地的远程仓库缓存。

如需更新，可通过`git fetch/pull` 命令获取远程仓库内容。

使用`fech` 获取时，并未合并到本地仓库，此时可使用`git merge` 实现远程仓库副本与本地仓库的合并。

### 远程仓库 `remote`

远程版本库与本地仓库概念基本一致，不同之处在于存在远程，可用于远程协作。

通过`push/pull`可实现本地与远程的交互；

## `Git` 应用

[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-Git-%E5%BC%95%E7%94%A8)

### 分支管理

<image src="https://ask.qcloudimg.com/http-save/yehe-7650548/0svb8qf6v4.png?" style="width: 700px;"/>

#### 常用分支介绍

- `master`，主分支；
- `hotfixes`，线上 `bug` 修复分支，**分支命名：`fix_{bug 序号}`**；
- `release`，用于 `uat`；
- `develop`，开发依赖分支，不可以直接做代码修改，只能通过 `feature` 分支提交 `MR`，并用于测试；
- `feature`，功能开发分支，**分支命名：`feat_{版本}_{功能}[_{子功能}]`**；

#### 分支管理简化

<image src="./Git 分支管理.jpg" style="width: 1000px;"/>

**功能开发流程：**

1. 项目负责人维护需求，开发人员认领需求；
1. 开发人员 `checkout` 出新的 `feat_{版本}_{功能}[_{子功能}]` 独立分支开发；
1. 功能开发自测完成将本地分支 `push` 到远程，提交 `MR`；
1. 项目负责人将代码合并到 `develop` 分支，根据开发进度考虑测试介入；
1. 测试问题 `checkout` 新的分支做测试 bug 修复；
1. 版本对应所有功能开发与测试完成后，从 `develop` 切出 `release` 进行 `UAT` 测试；
1. `UAT` 测试产生的 `bug`，从 `release` 分支 `checkout` 修复分支做 `bug` 修复，然后 `MR` 到 `release` 分支；
1. `UAT` 完成，合并代码到 `master` 分支，打 `tag` 用于历史版本的备份；
1. 运维人员发布最新的 `master` 分支（或者是根据 `tag` 进行发布）；
1. 如果 `UAT` 有代码更新，则项目负责人 `rebase` 最新的 `master` 分支到 `develop`；
1. 开发人员将开发分支 `rebase` 最新的 `develop`，合并已经修复或已经开发完的功能；

**线上 `bug` 修复流程：**

1. 测试或产品提交线上 `bug`；
1. 开发人员认领 `bug`，从 `mater` `checkout` 出 `fix_{bug 序号}` 分支修复。
1. 测试人员测试确认后，`bug` 修复人员提交 `MR` 合并 `master`；
1. 项目负责人审核代码后，打 `tag`;
1. 运维人员发布最新的 `master` 分支（或者是根据 `tag` 进行发布）；
1. 线上紧急需求走 `bugfix` 流程；

> **最佳实践建议：**
>
> 1. 使用权限控制，防止开发人员修改 `master/develop` 等敏感分支以及直接对敏感分支合共代码；
> 1. 不要在客户端进行代码的 `merge`，代码开发人员通过 `web` 端提交 `MR`，项目负责人代码审查之后进行合并；
> 1. 每个 `MR` 只允许一个 `commit`，如果要对已经提交的代码进行修改可以使用 `commit --amend` 和 `push -f` 进行代码提交，这样保证每个开发分支对应一个功能。后期如果做 `cherry-pick` 更方便，代码审查更高效；
> 1. 新的 `MR` 要详细记录本次合并请求涉及的需求功能，便于项目负责人代码审查和把握项目进度；
> 1. 合并其他分支时，优先考虑使用 `rebase`，代码合并时不会有多个 `commit` 记录，`git log` 也会更加清晰；
> 1. 即使项目开发只有一个人也要遵循分支管理规范；

## 常用命令

### **`clone`**

### **`init`** 初始化 `.git`

### **`status`** 查看当前状态

常用 `git status` 可以帮助开发人员更好的使用 `git`，可以通过 `git status` 快速了解下一步该做什么。

### **`log`** 查看提交日志

`git log --graph --oneline`，查看提交记录，`--oneline` 在一行展示信息

### **`fetch`** 从远程下载最新的对象和索引

### **`pull`** 从远程拉取代码到本地仓库

### **`push`** 推送到远程

### **`branch`** 分支管理

### **`switch`** 切换分支

### **`checkout`** 切换分支或恢复工作区文件

### **`add`** 添加文件到暂存区

### **`commit`** 将暂存区文件提交到本地仓库

### **`merge`** 分支合并

### **`rebase`** 变基

> 变基

### **`cherry-pick`** 检出提交记录

### **`reset`**

- `git reset --soft`：保留工作目录和暂存区的修改，只更改HEAD指向。
- `git reset --hard`：恢复工作目录和暂存区到指定提交，丢弃之后的所有提交。
- `git reset --mixed`（默认）：保留工作目录的修改，恢复暂存区到指定提交。

### **`restore`** 恢复工作区文件树

还原指定文件到某个提交时的状态。它会直接更改工作目录和暂存区,不会创建提交。

### **`revert`** 恢复分支到直径提交记录

生成一个新的反向提交（`commit`）来撤销指定提交。它不会修改提交历史,之后提交的修改都会被保留。

## Git 原理

[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-%E5%BA%95%E5%B1%82%E5%91%BD%E4%BB%A4%E4%B8%8E%E4%B8%8A%E5%B1%82%E5%91%BD%E4%BB%A4)

### .git 目录

```shell
$ ls -al
total 13
-rw-r--r-- 1 RH 197121  24 Apr 19 08:56 HEAD
-rw-r--r-- 1 RH 197121 301 Apr 19 08:56 config
-rw-r--r-- 1 RH 197121  73 Apr 19 08:56 description
drwxr-xr-x 1 RH 197121   0 Apr 19 08:56 hooks/
-rw-r--r-- 1 RH 197121 137 Apr 19 08:56 index
drwxr-xr-x 1 RH 197121   0 Apr 19 08:56 info/
drwxr-xr-x 1 RH 197121   0 Apr 19 08:56 logs/
drwxr-xr-x 1 RH 197121   0 Apr 19 08:56 objects/
-rw-r--r-- 1 RH 197121 183 Apr 19 08:56 packed-refs
drwxr-xr-x 1 RH 197121   0 Apr 19 08:56 refs/
```

- **`objects`** 目录存储所有数据内容；
- **`refs`** 目录存储指向数据（分支、远程仓库和标签等）的提交对象的指针；
- **`HEAD`** 文件指向目前被检出的分支；
- **`index`** 文件保存暂存区信息；
- `config` 文件包含项目特有的配置选项；
- `info` 目录包含一个全局性排除（`global exclude`）文件， 用以放置那些不希望被记录在 `.gitignore` 文件中的忽略模式（`ignored patterns`）；
- `hooks` 目录包含客户端或服务端的钩子脚本（`hook scripts`）；

### `Git` 对象

存储在 `.git/objects` 下。

[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-Git-%E5%AF%B9%E8%B1%A1)

**上层命令：**

- `git add`
- `git commit`

#### `blob` 数据对象

- `git cat-file`：查看对象。
  - `git cat-file -p master^{tree}`，查看项目当前最新的树对象。
  - `100644`：普通文件；
  - `100755`：可执行文件；
  - `120000`：符号链接；

#### `tree` 树对象

- `git write-tree`：从当前书树对象创建树对象。
- `git read-tree`：读取树信息到索引。

#### `commit` 提交对象

- `git commit-tree`: 创建一个提交对象。

#### `tag` 标签对象

标签对象通常指向一个提交对象，而不是一个树对象。

一个永不移动的分支引用——永远指向同一个提交对象。

### `Git` 引用

存储在 `.git/refs` 下。

[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-Git-%E5%BC%95%E7%94%A8)

上层命令：

- `git branch`

底层命令：

- `git update-ref`，更新引用
- `git symbolic-ref`，符号引用
- `git show-ref`，本地仓库索引列表

#### `HEAD` 引用

存储在 `.git/HEAD` 与 `.git/refs/heads/`。

- `.git/HEAD`，当前 `HEAD` 应用。
- `.git/refs/heads/`，个分支引用。

#### 标签应用

存储在 `.git/refs/tags/`

- 轻量标签
  - `git update-ref refs/tags/{annotation}`
- 附注标签
  - `git tag`

#### 远程引用

存储在 `.git/refs/remotes/`。

只读。

### 包文件

[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-%E5%8C%85%E6%96%87%E4%BB%B6)

`git gc`，对仓库进行重新打包以节省空间。

## 常见问题

1. `git rebase` 如何把开发分支变基到最新的开发提交。

    1. `git fetch`
    1. `git pull develop`
    1. `git rebase -i develop`，`-i` 交互进行合并选择
    1. 解决冲突文件
    1. `git add .`
    1. `git rebase --continue`
    1. `git commit --amend`，提交变更，`--amend` 是对上次提交进行修补不会产生额外的 `commit log`
    1. `git push -f`，强制推送，因为上一步 `--amend` ，处于安全考虑无法直接推送，需要 `-f` 强制推送

    ```shell
    # Commands:
    # p, pick <commit> = use commit 保留该提交(默认命令)
    # r, reword <commit> = use commit, but edit the commit message 保留该提交,但我需要修改提交信息
    # e, edit <commit> = use commit, but stop for amending 保留该提交,但我需要停下来修改该提交(不仅仅修改信息) 
    # s, squash <commit> = use commit, but meld into previous commit 将该提交和前一个提交合并
    # f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
    #                    commit's log message, unless -C is used, in which case
    #                    keep only this commit's message; -c is same as -C but
    #                    opens the editor 将该提交和前一个提交合并,但我只想保留前一个提交的信息
    # x, exec <command> = run command (the rest of the line) using shell 在该提交上执行一些 shell 命令
    # b, break = stop here (continue rebase later with 'git rebase --continue')
    # d, drop <commit> = remove commit 我要丢弃该提交
    # l, label <label> = label current HEAD with a name
    # t, reset <label> = reset HEAD to a label
    # m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
    #         create a merge commit using the original merge commit's
    #         message (or the oneline, if no original merge commit was
    #         specified); use -c <commit> to reword the commit message
    # u, update-ref <ref> = track a placeholder for the <ref> to be updated
    #                       to this position in the new commits. The <ref> is
    #                       updated at the end of the rebase
    ```

    ![rebase -i](./Git%20rebase.png)

1. `merge/rebase` 代码冲突解决。

    ```shell
    <<<<<<< HEAD
    second commit
    =======
    first commit
    >>>>>>> 5874284a836e0ca3336dcf1f97ec191373a76313
    ```

    - `<<<<<<< HEAD` 与 `=======` 之间的是本地代码变更
    - `=======` 与 `>>>>>>> {ref no}` 之间是来自合并代码变更

1. 代码撤销。
    1. 工作区代码撤销

        `git restore <file>...`

    1. 暂存区（索引区）代码撤销

        `git restore --staged <file>...`

    1. 本地仓库撤销

        `git reset [--soft | --mixed [-N] | --hard | --merge | --keep] [-q] [<commit>]`

    1. 远程仓库撤销

        1. `git revert`，会保留一个提交记录。
        1. `git reset`，移动 `HEAD` 引用，不会保留提交记录。
