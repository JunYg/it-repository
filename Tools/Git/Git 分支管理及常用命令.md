# Git 架构、分支管理及常用命令

Git，分布式版本控制系统

Git 帮助文档地址：

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

## `Git` 原理

### `Git` 应用

[官方文档](https://git-scm.com/book/zh/v2/Git-%E5%86%85%E9%83%A8%E5%8E%9F%E7%90%86-Git-%E5%BC%95%E7%94%A8)

## 分支管理

<image src="https://ask.qcloudimg.com/http-save/yehe-7650548/0svb8qf6v4.png?" style="width: 700px;"/>

### 分支介绍

- `master`，主分支；
- `hotfixes`，线上 `bug` 修复分支，**分支命名：`fix_{bug 序号}`**；
- `release`，用于 `uat`；
- `develop`，开发依赖分支，不可以直接做代码修改，只能通过 `feature` 分支提交 `MR`，并用于测试；
- `feature`，功能开发分支，**分支命名：`feat_{版本}_{功能}[_{子功能}]`**；

### 分支管理简化

<image src="./git.jpg" style="width: 1000px;"/>

**功能开发流程：**

1. 项目负责人维护需求，开发人员认领需求；
1. `checkout` 出新的 `feat_{版本}_{功能}[_{子功能}]` 分支开发；
1. 功能开发自测完成将本地分支 `push` 到远程，提交 `MR` 。
1. 项目负责人将代码合并到 `develop` 分支，根据开发进度考虑测试介入；
1. 测试问题 `checkout` 新的分支做测试 bug 修复；
1. 版本对应所有功能开发与测试完成后，从 `develop` 切出 `release` 进行 `UAT` 测试；
1. `UAT` 测试产生的 `bug`，从 `release` 分支 `checkout` 修复分支做 `bug` 修复，然后 `MR` 到 `release` 分支；
1. `UAT` 完成，合并代码到 `master` 分支，打 `tag` 用于历史版本的备份；
1. 运维人员发布最新的 `master` 分支（或者是根据 `tag` 进行发布）。
1. 如果 `UAT` 有代码更新，则 `rebase` 最新的 `master` 分支到 `develop`；
1. 开发中分支 `rebase` 最新的 `develop`，合并已经修复或已经开发完的功能；

**线上 `bug` 修复流程：**

1. 测试或产品提交线上 `bug`；
1. 开发人员认领 `bug`，从 `mater` `checkout` 出 `fix_{bug 序号}` 分支修复。
1. 测试人员测试确认后，`bug` 修复人员提交 `MR` 合并 `master`；
1. 项目负责人审核代码后，打 `tag`;
1. 运维人员发布最新的 `master` 分支（或者是根据 `tag` 进行发布）；

> **最佳实践建议：**
>
> 1. 通过权限控制，防止普通的开发人员修改 `master/develop` 等敏感分支；
> 1. 不要在客户端进行代码的 `merge`，代码开发人员通过 `gitlab` `web` 端提交 `MR`，项目负责人代码审查之后进行合并；
> 1. 每个 `MR` 只要有一个 `commit`，如果要对已经提交的代码进行修改可以使用 `commit --amend` 和 `push -f` 进行代码提交，这样保证每个分支对应一个功能。后期如果使用 `cherry-pick` 更方便；
> 1. 新的 `MR` 要详细记录本次合并请求有哪些功能，便于项目负责人代码审查和把握项目进度；
> 1. 合并其他分支时，有限考虑使用 `rebase`，代码合并时不会有多个 `commit` 记录，`git log --graph` 也会更加清晰；
> 1. 即使项目开发只有一个人也要遵循分支管理规范；

## 常用命令

### **`clone`**

### **`init`**

### **`fetch`**

### **`pull`**

### **`push`**

### **`branch`**

### **`switch`**

### **`checkout`**

### **`add`**

### **`commit`**

### **`merge`**

### **`rebase`**

> 变基

### **`cherry-pick`**

### **`reset`**

- git reset --soft:保留工作目录和暂存区的修改,只更改HEAD指向。
- git reset --hard:恢复工作目录和暂存区到指定提交,丢弃之后的所有提交。
- git reset --mixed(默认):保留工作目录的修改,恢复暂存区到指定提交。

### **`restore`**

还原指定文件到某个提交时的状态。它会直接更改工作目录和暂存区,不会创建提交。

### **`revert`**

生成一个新的反向提交（`commit`）来撤销指定提交。它不会修改提交历史,之后提交的修改都会被保留。

### **`log`**
