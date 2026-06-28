# Git Academy

[English](README.md) | [中文](README_CN.md)

个人 Git 命令参考手册 —— 每条命令的作用、使用时机，以及命令之间的关系。

---

## 目录

1. [git init](#git-init)
2. [git clone](#git-clone)
3. [git remote](#git-remote)
4. [git status](#git-status)
5. [git add](#git-add)
6. [git commit](#git-commit)
7. [git push](#git-push)
8. [git pull](#git-pull)
9. [git fetch](#git-fetch)
10. [git pull vs git fetch](#git-pull-vs-git-fetch)
11. [git branch](#git-branch)
12. [git checkout](#git-checkout)
13. [git merge](#git-merge)
14. [git rebase](#git-rebase)
15. [git merge vs git rebase](#git-merge-vs-git-rebase)
16. [git stash](#git-stash)
17. [git cherry-pick](#git-cherry-pick)
18. [git revert](#git-revert)
19. [git reset](#git-reset)
20. [git revert vs git reset](#git-revert-vs-git-reset)
21. [git log](#git-log)
22. [git diff](#git-diff)
23. [git blame](#git-blame)
24. [git tag](#git-tag)
25. [git bisect](#git-bisect)

---

## git init

在当前文件夹中创建一个全新的 Git 仓库。

```bash
git init                    # 在当前目录初始化仓库
git init my-project         # 新建文件夹并在其中初始化仓库
```

它会创建一个隐藏的 `.git` 文件夹，Git 的所有历史记录、分支、提交都存储在这里。没有它，Git 对这个文件夹一无所知。

新项目的典型流程：
```bash
mkdir my-project && cd my-project
git init
# 创建一些文件...
git add .
git commit -m "Initial commit"
git remote add origin <github-url>
git push -u origin main
```

可以理解为"把这个普通文件夹变成 Git 追踪的项目"。只在项目开始时运行一次。如果是从 GitHub 克隆已有仓库，则不需要 `git init`——`git clone` 会自动处理。

---

## git clone

第一次从 GitHub（或任何远程）将仓库的完整副本下载到本地。

```bash
git clone <url>
```

这是一次性的初始化操作。之后使用 `pull`/`push` 来同步。可以理解为 `pull`/`push` 生效之前的起点。

---

## git remote

管理与远程仓库（如 GitHub）的连接。

```bash
git remote -v                          # 列出所有远程及其 URL
git remote add origin <url>            # 添加名为 "origin" 的远程
git remote remove origin               # 删除远程
git remote set-url origin <new-url>    # 修改远程 URL
```

"origin" 只是默认远程的惯用名称，并不特殊，只是 GitHub URL 的别名。

`git remote -v` 示例输出：
```
origin  https://github.com/username/repo.git (fetch)
origin  https://github.com/username/repo.git (push)
```

可以理解为本地仓库的通讯录——将 `origin` 这样的简短名称映射到完整的 GitHub URL，省去每次推送或拉取时手动输入 URL 的麻烦。

---

## git status

显示工作目录和暂存区的当前状态。

```bash
git status
```

它会告诉你：
- 哪些文件已**修改**但尚未暂存
- 哪些文件已**暂存**并准备提交
- 哪些文件是**未追踪**的（新文件，Git 尚不知晓）
- 当前所在分支
- 是否领先/落后于远程

示例输出：
```
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  modified:   README.md

Untracked files:
  new-file.txt
```

可以理解为"当前状态快照"，在决定暂存和提交之前查看。大多数人频繁运行它——这是日常使用最多的 Git 命令。

---

## git add

将文件加入暂存区，告诉 Git"把这些改动包含在下次提交中"。

```bash
git add README.md           # 暂存指定文件
git add .                   # 暂存当前目录下的所有改动
git add -p                  # 交互式暂存（逐块选择要包含的内容）
```

暂存区的存在让你可以有所取舍——你可能改动了 5 个文件，但只想提交其中 2 个。

```bash
# 典型流程
git status        # 查看改动
git add README.md # 只暂存需要的文件
git commit -m "update readme"
```

可以理解为发货前打包——`git add` 把东西放进箱子，`git commit` 封箱发出。

---

## git commit

将暂存的改动作为永久快照保存到仓库历史中。

```bash
git commit -m "提交说明"          # 带说明的提交
git commit -am "提交说明"         # 暂存所有已追踪文件并一步提交
git commit --amend                # 修改最后一次提交（推送前）
```

每次提交包含：
- 唯一哈希值（如 `fdb14c4`）
- 作者 + 时间戳
- 描述改动的说明
- 所有暂存文件的快照

好的提交说明描述的是**为什么**，而不是做了什么：
```bash
# 不好
git commit -m "改了 README"

# 好
git commit -m "在前提条件中添加 GitHub Token 获取指引"
```

完整流程：
```bash
git add README.md          # 暂存
git commit -m "说明"       # 快照
git push                   # 推送到 GitHub
```

可以理解为游戏存档——创建一个随时可以回溯的存档点。

---

## git push

将本地提交发送到 GitHub 上的远程仓库。

```bash
git push                        # 推送当前分支到其追踪的远程
git push origin main            # 明确推送 main 到 origin
git push -u origin main         # 推送并设置上游（仅第一次需要）
git push origin v1.0.0          # 推送指定标签
git push --force                # 强制覆盖远程历史（危险）
```

`-u` 标志（上游）只需设置一次——它将本地分支与远程关联，之后 `git push` 不带参数即可运行。

可以理解为让你的工作对外界可见的最后一步——在此之前的所有操作（add、commit）都只是本地的。不推送，GitHub 上什么都不会变。

---

## git pull

从 GitHub 下载远程改动并立即合并到当前分支（fetch + merge 一步完成）。

```bash
git pull                        # 从追踪的远程分支拉取
git pull origin main            # 明确从 origin 拉取 main
```

---

## git fetch

下载远程改动，但**不**应用它们。本地文件保持不变——只是更新本地对 GitHub 上内容的认知。

```bash
git fetch                       # 获取所有远程
git fetch origin                # 从 origin 获取
```

---

## git pull vs git fetch

| | `git fetch` | `git pull` |
|---|---|---|
| 下载远程改动 | 是 | 是 |
| 更新本地文件 | 否 | 是 |
| 随时运行安全 | 是 | 通常安全 |

- **`git fetch`** = "查看有什么变化"
- **`git pull`** = "查看并应用变化"

---

## git branch

创建、列出或删除分支。

```bash
git branch                    # 列出所有本地分支
git branch feature-x          # 创建新分支
git branch -d feature-x       # 删除分支（安全，未合并会警告）
git branch -D feature-x       # 强制删除
git branch -a                 # 列出所有分支（含远程）
```

分支只是指向某个提交的指针——让你可以独立开展工作，不影响 `main`。

常见流程：
```bash
git branch feature-x          # 创建
git checkout feature-x        # 切换过去
# 开发、提交...
git checkout main
git merge feature-x           # 合并回来
```

---

## git checkout

在分支之间切换，或恢复文件。

```bash
git checkout main              # 切换到 main 分支
git checkout feature-x         # 切换到另一个分支
git checkout -b feature-x      # 创建并切换，一步完成
git checkout abc1234           # 切换到指定提交（游离 HEAD 状态）
git checkout -- README.md      # 丢弃对文件的改动，恢复到最后一次提交
```

它做两件不太相关的事：
1. **切换分支** — 将工作目录切换到不同分支
2. **恢复文件** — 丢弃对特定文件的未提交改动

因为功能太混杂，Git 后来将其拆分为两个更清晰的命令：
- `git switch` — 用于切换分支
- `git restore` — 用于恢复文件

`git checkout` 仍然可用且被广泛使用，但 `switch`/`restore` 是现代替代方案。

---

## git merge

通过创建新的"合并提交"将分支合并。完整保留两个分支的历史，原样呈现。

```bash
git checkout main
git merge feature-x            # 将 feature-x 合并到 main
```

可以理解为："两条路在路口汇合"——历史中两条路径都清晰可见。

---

## git rebase

将你的提交重新"播放"到另一个分支之上，就像你是从那里分叉出去的一样。历史看起来线性整洁，但提交被重写了。

```bash
git checkout feature-x
git rebase main                # 将 feature-x 的提交重放到 main 之上
```

可以理解为："假装你一直是从那条路出发的"——一条干净的线性路径。

---

## git merge vs git rebase

| | `git merge` | `git rebase` |
|---|---|---|
| 历史 | 保留两条路径 | 线性，已重写 |
| 已推送分支是否安全 | 安全 | 危险 |
| 可读性 | 保留完整上下文 | 日志更整洁 |

- 与他人协作或在共享分支上工作时，使用 **merge**。
- 合并前想要整洁的本地分支历史时，使用 **rebase**。

---

## git stash

临时搁置未提交的改动，方便切换上下文，之后再取回继续。

```bash
git stash        # 保存未提交改动，恢复到干净状态
git stash pop    # 恢复搁置的改动
git stash list   # 查看所有暂存条目
```

可以理解为进行中工作的剪贴板。没有提交，没有丢失——只是暂时放在一边。

---

## git cherry-pick

从历史中任意位置取出一个特定提交，应用到当前分支，而无需合并整个分支。

```bash
git cherry-pick abc1234   # 只把那一个提交应用到这里
```

使用场景：在 `main` 上修了一个 bug，想把这个修复也用到旧分支上——但不想把 `main` 上的其他所有改动也带过来。

可以理解为"我只要那一个提交，不要整个分支"。

---

## git revert

创建一个**新提交**来撤销之前的某次提交。历史得以保留。在共享/已推送的分支上使用是安全的。

```bash
git revert abc1234        # 创建一个新提交来撤销 abc1234
```

可以理解为："我犯了个错，让我记录一下修正。"

---

## git reset

将分支指针向后移动，从历史中**抹去**提交。具有破坏性。在已推送的分支上危险，因为它重写了别人可能已经拉取的历史。

```bash
git reset HEAD~1           # 撤销最后一次提交，保留改动在暂存区
git reset --hard HEAD~1    # 撤销最后一次提交，彻底丢弃改动
```

可以理解为："假装那次提交从未发生过。"

---

## git revert vs git reset

| | `git revert` | `git reset` |
|---|---|---|
| 历史 | 保留（添加新提交） | 重写（抹去提交） |
| 已推送分支是否安全 | 安全 | 不安全 |
| 使用时机 | 已推送到 GitHub | 仅本地，尚未推送 |

简单原则：
- 已推送 → 用 `revert`
- 仅本地 → `reset` 没问题

---

## git log

显示仓库的提交历史。

```bash
git log                      # 完整历史
git log --oneline            # 简洁版，每个提交一行
git log --oneline --graph    # 可视化分支/合并树
git log -5                   # 最近 5 次提交
git log -- README.md         # 指定文件的历史
```

可以理解为所有提交过内容的完整时间线。`git log` 告诉你*有哪些提交*；`git diff` 告诉你每次提交*具体改动了什么*。

---

## git diff

逐行显示具体的改动内容。

```bash
git diff                        # 未暂存的改动（工作区 vs 最后一次提交）
git diff --staged               # 已暂存的改动（即将提交的内容）
git diff abc1234 def5678        # 两次提交之间的差异
git diff main feature-branch    # 两个分支之间的差异
git diff README.md              # 指定文件的改动
```

输出用 `+` 表示新增行，`-` 表示删除行。

---

## git blame

显示文件每一行最后由谁修改、在哪次提交中修改。

```bash
git blame README.md
```

示例输出：
```
abc1234 (Alan  2026-06-20) ## Overview
def5678 (Alan  2026-06-18) 这里是某些内容...
```

使用场景：某行代码有问题或令人困惑——`git blame` 告诉你是谁写的、什么时候写的、应该看哪次提交来了解上下文。

这个名字听起来像是在"指责"，其实只是"这行找谁问"。

---

## git tag

用命名标签标记某个特定提交，通常用于标记发布版本。

```bash
git tag v1.0.0           # 在当前提交创建标签
git push origin v1.0.0   # 将标签推送到 GitHub
git tag                  # 列出所有标签
```

与分支名称随提交向前移动不同，标签永远固定在那个特定的历史点上。

最常见用途：标记发布版本（`v1.0`、`v2.1.3`），这样你随时可以检出当时发布的确切代码。

---

## git bisect

通过二分查找在提交历史中定位引入 bug 的那次提交。

```bash
git bisect start
git bisect bad           # 当前提交有问题
git bisect good v1.0.0   # 这个旧提交是正常的
# Git 检出中间的某次提交——你测试后输入：
git bisect good          # 或：git bisect bad
# Git 不断缩小范围，直到找到罪魁祸首
git bisect reset         # 完成，回到正常状态
```

不用逐一检查 100 次提交，Git 每次将搜索范围减半——大约 7 步就能找到问题提交，而不是 100 步。

---

*由 [Git Academy](https://github.com/zzpy20/Git-Academy) 整理*
