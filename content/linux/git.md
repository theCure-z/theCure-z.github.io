# Git 常用概念总结

Git 是一个**分布式版本控制系统**，用于管理文件的历史版本、多人协作开发以及代码发布。

---

# 1. Git 基本概念
## 1.1 版本控制

版本控制用于记录文件随时间的变化，使开发者能够：

- 查看历史修改
- 回退到旧版本
- 比较不同版本差异
- 多人协作开发

常见版本控制系统：

| 类型 | 代表工具 | 特点 |
|-|-|-|
| 集中式版本控制 | SVN | 中央服务器保存全部版本 |
| 分布式版本控制 | Git | 每个人都有完整仓库 |

---

# 2. Git 三大区域

Git 文件状态主要存在三个区域：

```
工作区 Working Directory
        |
        | git add
        ↓
暂存区 Staging Area
        |
        | git commit
        ↓
本地仓库 Local Repository
        |
        | git push
        ↓
远程仓库 Remote Repository
```

---

## 2.1 工作区（Working Directory）

当前电脑中实际看到的文件目录。
例如：

```
~/project
├── main.cpp
├── README.md
└── src/
```

特点：

- 可以直接修改文件
- 修改不会自动进入 Git 历史

查看状态：

```bash
git status
```

---

## 2.2 暂存区（Staging Area）

也叫：

- index
- stage

作用：
保存下一次提交（commit）的内容。
加入暂存区：

```bash
git add filename
```

全部加入：

```bash
git add .
```

查看暂存状态：

```bash
git status
```

---

## 2.3 本地仓库（Local Repository）

Git 保存版本历史的位置。
提交：

```bash
git commit -m "message"
```

例如：

```bash
git commit -m "add login function"
```

每一次 commit 都产生一个版本节点。

---

## 2.4 远程仓库（Remote Repository）

存放在服务器上的 Git 仓库。

常见平台：

- GitHub
- GitLab
- Gitee

---

# 3. Git 仓库（Repository）

仓库就是 Git 管理的项目目录。
创建仓库：

```bash
git init
```

生成：

```
.git/
```

目录。例如：

```
project
├── .git
├── main.cpp
└── README.md
```

`.git` 保存：

- 提交记录
- 分支信息
- 配置信息
- 对象数据库

不能手动修改 `.git`。

---

# 4. Git 对象模型

Git 内部主要保存四类对象：

```
Blob
 |
 | 保存文件内容

Tree
 |
 | 保存目录结构

Commit
 |
 | 保存一次提交

Tag
 |
 | 给提交打标签
```

关系：

```
Commit
  |
  ↓
Tree
  |
  ↓
Blob
```

---

# 5. Commit（提交）

Commit 是 Git 最核心概念。
一次 commit 表示：

> 项目在某个时间点的完整状态。

查看提交：

```bash
git log
```

简洁查看：

```bash
git log --oneline
```

示例：

```
a82f3c1 add README
b712e20 fix bug
9a112cc initial commit
```

其中：

```
a82f3c1
```

是 commit hash。

---

# 6. Git Hash

Git 使用 SHA-1/SHA-256 哈希标识对象。
例如：

```
a82f3c1d7e8f...
```

特点：

- 唯一
- 内容改变，hash改变
- 用于定位版本

查看当前 commit：

```bash
git rev-parse HEAD
```

---

# 7. HEAD

HEAD 表示：

> 当前所在的位置

通常指向当前分支最新 commit。
例如：

```
HEAD
 |
main
 |
commit C
 |
commit B
 |
commit A
```

查看：

```bash
git log --decorate
```

---

# 8. 分支（Branch）

分支本质：

> 一个指向 commit 的指针。

例如：

```
        C---D  feature
       /
A---B
       \
        E---F  main
```

main 和 feature 都基于 B 开发。

---

# 9. 创建分支


创建：

```bash
git branch dev
```

切换：

```bash
git checkout dev
```

或者：

```bash
git switch dev
```


创建并切换：

```bash
git checkout -b dev
```

新版：

```bash
git switch -c dev
```

---

# 10. 查看分支

```bash
git branch
```

结果：

```
* main
  dev
```

`*` 表示当前分支。

查看远程分支：

```bash
git branch -r
```

查看所有：

```bash
git branch -a
```

---

# 11. 合并（Merge）

将一个分支合入当前分支。
例如：
当前：

```
main
```

合并：

```bash
git merge dev
```

结果：

```
      C---D dev
     /
A---B---E main
```

---

# 12. 快进合并（Fast Forward）

如果目标分支没有新的提交：

```
A---B main
     \
      C---D dev
```

merge 后：

```
A---B---C---D main
```

没有产生新的 commit。

---

# 13. 三方合并（Three-way Merge）

两个分支都有修改：

```
      C---D dev
     /
A---B
     \
      E---F main
```

Git 创建新的 merge commit：

```
      C---D
     /     \
A---B       G
     \     /
      E---F
```

---

# 14. 冲突（Conflict）

当两个分支修改同一位置：

```
<<<<<<< HEAD

当前分支内容

=======

合并分支内容

>>>>>>> dev
```

解决：

1. 手动修改文件
2. 删除冲突标记
3. 添加：

```bash
git add file
```

4. 提交：

```bash
git commit
```

---

# 15. Rebase

变基：

> 修改提交历史，将分支移动到新的基础上。

merge:

```
A---B---C main
     \
      D---E dev
```


rebase:

```
A---B---C---D'---E'
```

特点：

- 历史更线性
- 不产生 merge commit


命令：

```bash
git rebase main
```

---

# 16. Merge 和 Rebase 区别

| | Merge | Rebase |
|-|-|-|
| 是否修改历史 | 否 | 是 |
| 是否产生新commit | 可能 | 不一定 |
| 历史结构 | 分叉 | 线性 |
| 推荐场景 | 公共分支 | 个人开发分支 |

---

# 17. 远程仓库 Remote

查看：

```bash
git remote
```

详细：

```bash
git remote -v
```

添加：

```bash
git remote add origin URL
```

例如：

```bash
git remote add origin git@github.com:user/repo.git
```

---

# 18. Push

上传本地提交：

```bash
git push
```

第一次：

```bash
git push -u origin main
```

其中：

```
origin
```

表示远程仓库名称。

```
main
```

表示分支。

---

# 19. Pull

下载并合并远程代码：

```bash
git pull
```

等价：

```
git fetch
+
git merge
```

---

# 20. Fetch

只下载远程信息，不合并：

```bash
git fetch
```

例如：

```
远程:

A---B---C

本地:

A---B
```

fetch 后：

```
origin/main

A---B---C

main

A---B
```

---

# 21. Clone

复制远程仓库：

```bash
git clone URL
```

例如：

```bash
git clone https://github.com/user/project.git
```

作用：

- 下载代码
- 自动建立 remote

---

# 22. Git 常用查看命令

查看状态：

```bash
git status
```

查看提交：

```bash
git log
```

查看差异：

```bash
git diff
```

查看暂存区：

```bash
git diff --cached
```

查看分支：

```bash
git branch
```

查看远程：

```bash
git remote -v
```

---

# 23. 撤销操作
## 撤销工作区修改

```bash
git checkout -- file
```

新版：

```bash
git restore file
```

---

## 撤销 add

取消暂存：

```bash
git restore --staged file
```

---

## 修改最后一次提交

```bash
git commit --amend
```

---

## 回退版本

软回退：

```bash
git reset --soft HEAD~1
```

保留修改。

混合回退：

```bash
git reset HEAD~1
```

取消commit，保留文件。

硬回退：

```bash
git reset --hard HEAD~1
```

删除修改。

---

# 24. Git 标签 Tag

给版本命名：

```bash
git tag v1.0
```

查看：

```bash
git tag
```

上传：

```bash
git push origin v1.0
```

常用于：

- 软件发布
- 版本管理

---

# 25. .gitignore

指定不提交的文件。
例如：

```
node_modules/

*.log

.env

build/
```

查看忽略：

```bash
git status --ignored
```

---

# 26. Git 工作流程

## 简单个人流程

```
修改文件
↓
git add .
↓
git commit
↓
git push
```

---

## 团队开发流程

```
main
 |
 | clone
 ↓
feature branch
↓
开发
↓
commit
↓
push
↓
Pull Request
↓
merge main
```

---

# 27. Git 常见名词总结

| 名词 | 含义 |
|-|-|
| Repository | 仓库 |
| Working Tree | 工作区 |
| Stage | 暂存区 |
| Commit | 提交 |
| Branch | 分支 |
| HEAD | 当前指针 |
| Remote | 远程仓库 |
| Origin | 默认远程名称 |
| Clone | 克隆 |
| Fetch | 获取远程更新 |
| Pull | 获取并合并 |
| Push | 上传 |
| Merge | 合并 |
| Rebase | 变基 |
| Tag | 标签 |
| Hash | 唯一编号 |
| Conflict | 冲突 |

---

# 28. 最常用 Git 命令速查

```bash
# 初始化
git init

# 查看状态
git status

# 添加文件
git add .

# 提交
git commit -m "message"

# 查看历史
git log --oneline

# 创建分支
git branch dev

# 切换分支
git switch dev

# 创建并切换
git switch -c dev

# 合并分支
git merge dev

# 拉取远程
git pull

# 上传
git push

# 克隆
git clone URL

# 查看远程
git remote -v

# 查看差异
git diff

# 标签
git tag v1.0
```

---

# 总结

Git 的核心思想：

```
文件变化
    ↓
工作区
    ↓ git add
暂存区
    ↓ git commit
本地仓库
    ↓ git push
远程仓库
```

核心对象：

```
Commit
 |
Tree
 |
Blob
```

核心操作：

```
修改
 → add
 → commit
 → branch
 → merge/rebase
 → push
```