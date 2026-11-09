# 第 1 月｜工程环境 + Git / GitHub 命令手册

> 适用范围：第 1 月“工程环境 + Git/GitHub”学习内容  
> 目标：不是背命令，而是知道 **命令改变了什么、发生在哪一层、什么时候该用、什么时候不该用、出错后怎么恢复**。  
> 命令清单以本月学习计划实际出现的内容为主；语义说明用于帮助理解这些命令的正确使用边界。

---

## 0. 先记住 Git 的四层模型

```text
Working Directory
    ↓ git add
Staging Area
    ↓ git commit
Local Repository
    ↓ git push
Remote Repository
```

| 层 | 含义 | 典型命令 |
|---|---|---|
| Working Directory | 当前正在编辑的文件 | `git status`、`git diff`、`git restore` |
| Staging Area | 下一次提交准备包含的内容 | `git add`、`git restore --staged` |
| Local Repository | 本机已经形成的提交历史 | `git commit`、`git log`、`git revert` |
| Remote Repository | GitHub 等远程仓库 | `git push`、`git fetch`、`git pull`、`git clone` |

遇到 Git 问题时，先问四个问题：

1. 问题发生在哪一层？
2. 当前真实状态是什么？
3. 我希望变化到哪一层？
4. 这个命令会不会改写、删除或覆盖历史？

**任何不确定时，优先运行：**

```bash
git status
```

---

# 1. Terminal / 文件系统基础命令

## 1.1 `pwd`

**作用**：显示当前所在目录的完整路径。

```bash
pwd
```

**正确使用**：不确定自己在哪个目录时；执行 `git init`、删除、移动文件前确认位置。

**相似命令**：`ls` 看当前目录内容；`cd` 改变当前目录。

**边界**：只查看，不修改任何文件或目录。

---

## 1.2 `ls`

**作用**：列出当前目录中的文件和子目录。

```bash
ls
```

**正确使用**：确认文件是否存在、检查目录结构、执行 `cd/cp/mv/rm` 前确认目标。

**相似命令**：`pwd` 看路径；`git status` 看 Git 状态，而不是普通目录内容。

**边界**：不是 Git 命令，只查看文件系统。

---

## 1.3 `cd`

**作用**：切换目录。

```bash
cd <目录>
cd ..
cd ./projects
cd ~/dev
```

| 写法 | 含义 |
|---|---|
| `.` | 当前目录 |
| `..` | 上一级目录 |
| `~` | 当前用户 Home 目录 |
| `/完整路径` | 绝对路径 |
| `./xxx` | 相对当前目录的路径 |

**正确使用**：在执行 Git 命令前进入正确的仓库目录。

**边界**：Git 命令通常作用于当前所在仓库。进入错目录后操作，是初学阶段常见错误。

---

## 1.4 `mkdir`

**作用**：创建目录。

```bash
mkdir project
```

**相似命令**：`touch` 创建文件；`cp` 复制；`mv` 移动/重命名；`rm` 删除。

**边界**：`mkdir` 不会让目录自动成为 Git 仓库；还需要 `git init`。

---

## 1.5 `touch`

**作用**：创建空文件。

```bash
touch README.md
```

**正确使用**：快速创建 `README.md`、`notes.md`、`todo.md`、`.gitignore`、`.env` 等。

**边界**：创建文件不等于 Git 已跟踪它。创建后仍需 `git status → git add → git commit`。

---

## 1.6 `cp`

**作用**：复制文件或目录。

```bash
cp source.txt copy.txt
```

| 命令 | 结果 |
|---|---|
| `cp` | 原文件保留，同时产生副本 |
| `mv` | 原位置不再保留，移动或重命名 |
| `rm` | 删除 |

**边界**：复制到仓库中的新文件是否进入版本控制，要用 `git status` 确认。

---

## 1.7 `mv`

**作用**：移动文件，或给文件重命名。

```bash
mv old.txt new.txt
```

**相似命令**：`cp` 复制；`rm` 删除。

**边界**：移动 Git 已跟踪文件后，应先 `git status` 确认 Git 识别到的变化。

---

## 1.8 `rm`

**作用**：删除文件。

```bash
rm file.txt
```

**相似命令**：`git restore <file>` 是放弃未提交修改；`rm` 是直接删除文件系统中的文件。

**边界 / 风险**：`rm` 不是“Git 撤销”工具。删除前先确认：

```bash
pwd
ls
```

如果只是想放弃已跟踪文件的未提交修改，优先考虑：

```bash
git restore <file>
```

---

# 2. Git 初始化与身份配置

## 2.1 `git --version`

**作用**：确认 Git 是否安装并查看版本。

```bash
git --version
```

**边界**：只读取信息。

---

## 2.2 `git config`

**作用**：配置 Git 用户信息。

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global --list
```

**相似概念**：Git 用户信息用于 commit 身份；GitHub 账号是远程平台账号。二者相关，但不是同一个东西。

**边界**：`--global` 是当前用户的全局配置；要明确自己是在改全局还是单仓库设置。

---

## 2.3 `git init`

**作用**：把普通目录初始化为 Git 仓库。

```bash
git init
```

**实际发生**：创建 `.git` 元数据目录，使当前目录可以记录版本历史。

**相似命令**：

| 命令 | 场景 |
|---|---|
| `git init` | 本地已有目录，从零开始版本控制 |
| `git clone <url>` | 已有远程仓库，复制到本地 |

**边界**：不会自动创建 GitHub Repo、不会自动 commit、不会自动 push。

---

## 2.4 `git clone`

**作用**：把远程仓库复制到本地。

```bash
git clone <url>
```

**相似命令**：`git init` 本地从零开始；`git pull` 更新已经存在的本地仓库。

**边界**：已 clone 的仓库通常不需要再次 `git init`。`clone` 是第一次完整复制，`pull` 是后续同步。

---

# 3. 状态、差异与历史

## 3.1 `git status`

**作用**：查看当前仓库状态。

```bash
git status
```

它能告诉你：当前分支、哪些文件修改了、哪些未跟踪、哪些已暂存、是否有冲突等。

**相似命令**：

| 命令 | 看什么 |
|---|---|
| `git status` | 当前状态 |
| `git diff` | 当前内容差异 |
| `git log` | 已提交历史 |

**正确使用原则**：遇到 Git 问题时，`git status` 优先于盲目执行修复命令。

**边界**：主要查看状态，不自动修复问题。

---

## 3.2 `git diff`

**作用**：查看尚未暂存的修改差异。

```bash
git diff
```

**正确流程**：

```text
修改
↓
git status
↓
git diff
↓
git add
```

**边界**：本月主要用于提交前检查和错误恢复前收集证据。

---

## 3.3 `git log`

**作用**：查看提交历史。

```bash
git log
```

**正确使用**：检查 commit 是否成功、确认提交顺序、找到需要 revert 的 commit、检查历史是否清晰。

**边界**：主要查看历史，不修改历史。

---

# 4. Git 核心提交循环

## 4.1 `git add`

**作用**：把选中的修改送入 Staging Area。

```bash
git add README.md
```

```text
Working Directory
        ↓
Staging Area
```

**相似命令**：`git commit` 创建本地历史；`git push` 把本地历史发送到远程。

**正确流程**：

```bash
git status
git diff
git add <file>
git status
git commit -m "..."
```

**边界**：`git add` 不是 commit，也不是上传 GitHub。add 错了：

```bash
git restore --staged <file>
```

---

## 4.2 `git commit -m`

**作用**：把 Staging Area 中的内容创建成一个本地历史节点。

```bash
git commit -m "docs: add installation notes"
```

```text
Staging Area
      ↓
Local Repository
```

**正确使用**：一个 commit 尽量对应一个可以解释的逻辑变化。

推荐：

```text
docs: add installation notes
test: add merge conflict practice
feat: add project structure
fix: correct broken README link
```

避免：

```text
update
test
final
修改
```

**边界**：commit 后仍只在本地；GitHub 要看到变化还需 `git push`。

---

## 4.3 `git commit --amend`

**作用**：修正最近一次 commit。

```bash
git commit --amend
```

**适合**：最近一次本地 commit message 写错，或最近一次提交需要小范围修正。

**相似命令**：

| 命令 | 用途 |
|---|---|
| `git commit --amend` | 修改最近一次提交 |
| `git revert <commit>` | 新建反向提交撤销旧提交 |
| `git reset` | 移动历史指针，可能改写历史 |

**边界**：`amend` 会改变最近一次 commit 本身。已经 push、并被别人基于它开发时要谨慎；公开错误历史本月优先 `revert`。

---

# 5. `.gitignore`

`.gitignore` 不是命令，但属于本月核心仓库能力。

```gitignore
.env
*.log
temp/
```

**作用**：告诉 Git 哪些文件/目录不应纳入版本控制。

**边界**：

- `.gitignore` 不是安全保险箱。
- 已经被 Git 跟踪的文件，不会因为后来加入 `.gitignore` 就自动从历史消失。
- 本月核心原则：**`.env` 不应该被提交。**

---

# 6. Branch / 分支

## 6.1 `git branch`

**作用**：本月主要用于理解和查看分支。

```bash
git branch
```

**边界**：Branch 的核心价值是隔离任务，不让所有修改都直接发生在 `main`。

---

## 6.2 `git switch -c`

**作用**：创建并立即切换到新分支。

```bash
git switch -c feature/readme
```

**标准场景**：

```text
Issue
↓
Branch
↓
修改
↓
Commit
↓
Push
↓
PR
```

**相似命令**：`git switch <branch>` 只切换已有分支。

**边界**：不要把所有任务都直接做在 `main`。分支应有明确任务边界。

---

## 6.3 `git switch`

**作用**：切换到已有分支。

```bash
git switch main
```

**边界**：有未完成修改时切换分支，Git 可能阻止操作或要求先处理当前工作。需要时可 `stash`，但 stash 只是临时保存。

---

# 7. Merge / 合并

## 7.1 `git merge`

**作用**：把指定分支历史整合到当前分支。

```bash
git switch main
git merge feature/readme
```

意思是：把 `feature/readme` 合并进当前 `main`。

**相似概念**：Branch=隔离开发；Merge=汇合历史；PR=GitHub 上提出和审查合并；Conflict=无法自动决定如何整合。

**边界**：Merge Conflict 需要人工判断最终内容。标准处理：

1. `git status`
2. 找冲突文件
3. 阅读冲突标记
4. 人工决定最终内容
5. `git add`
6. 完成提交

不要看到 Conflict 就随机执行恢复命令。

---

# 8. Remote / 远程仓库

## 8.1 `git remote -v`

**作用**：查看当前本地仓库关联的远程地址。

```bash
git remote -v
```

**正确使用**：push 失败、怀疑 remote 配错、月底验收前检查配置。

**边界**：只查看，不上传也不下载。

---

## 8.2 `git remote add origin <url>`

**作用**：为本地仓库添加名为 `origin` 的远程地址。

```bash
git remote add origin <url>
```

**相似命令**：`git clone` 通常会自动建立远程关系；`remote add` 用于本地 init 后再连接远程。

**边界**：添加 remote 后仍未上传代码，还需要 `git push`。

---

# 9. Push / Fetch / Pull

## 9.1 `git push`

**作用**：把本地已经存在的 commit 发送到远程仓库。

```bash
git push
```

```text
Local Repository
       ↓
Remote Repository
```

**边界**：push 的是 commit，不是 Working Directory 中尚未提交的修改。

---

## 9.2 `git fetch`

**作用**：获取远程变化，但不直接整合进当前工作分支。

```bash
git fetch
```

**适合**：先看看远端发生了什么，再决定如何处理。

**相似命令**：`git pull` = 获取并整合。

**边界**：如果只想“先获取信息，不动当前工作”，fetch 更保守。

---

## 9.3 `git pull`

**作用**：获取远程变化并整合到本地当前工作。

```bash
git pull
```

**正确使用**：PR merge 后同步 main；多人协作获取别人变化；第二个 clone 落后时同步。

**边界**：本地有未处理修改时，不要机械 pull。先：

```bash
git status
```

必要时先 commit 或 stash。

---

# 10. Restore / 文件与暂存恢复

## 10.1 `git restore <file>`

**作用**：放弃 Working Directory 中尚未提交的修改。

```bash
git restore README.md
```

**适合**：“文件内容改错了，而且这些修改确定不要了。”

**相似命令**：

| 命令 | 操作层 |
|---|---|
| `git restore <file>` | Working Directory |
| `git restore --staged <file>` | Staging Area |
| `git revert <commit>` | 已提交历史 |
| `git reset` | 历史指针/暂存等，风险更高 |

**边界 / 风险**：可能丢弃未提交修改。执行前先：

```bash
git status
git diff
```

---

## 10.2 `git restore --staged <file>`

**作用**：把文件从 Staging Area 移回 Working Directory。

```bash
git restore --staged notes.md
```

**适合**：“我 add 错文件了，但文件内容还要保留。”

```text
git restore --staged
= 保留修改，只取消暂存

git restore <file>
= 放弃 Working 修改
```

---

# 11. Revert / 已提交错误恢复

## 11.1 `git revert <commit>`

**作用**：创建一个新的反向 commit，抵消指定 commit 的影响。

```bash
git revert <commit>
```

**适合**：已经 commit，特别是已经公开/push 的错误提交。

**相似命令**：restore=未提交文件；restore --staged=取消暂存；amend=修改最近提交；revert=新建反向提交；reset=可能改写历史。

**边界**：revert 不是删除旧 commit，而是：

```text
错误 commit
↓
新的反向 commit
↓
最终内容恢复，历史仍可追踪
```

因此公开/共享历史优先考虑 `revert`。

---

# 12. Reset / 高风险边界命令

## 12.1 `git reset`

本月目标不是熟练掌握各种 reset，而是理解风险边界。

**本月原则**：

> 不确定 `reset` 的后果时，不执行。  
> 不知道 `git reset --hard` 会删除什么时，不使用它。

恢复优先级：

```text
文件改错
→ restore

add 错
→ restore --staged

最后一次本地 commit 需要修正
→ amend（确认影响范围）

已公开错误 commit
→ revert

不确定 reset 后果
→ 停下，不执行
```

**边界 / 风险**：`git reset --hard` 可能导致本地修改丢失。本月只在练习仓库理解风险，不作为“万能撤销”。

---

# 13. Stash / 临时保存未完成工作

## 13.1 `git stash`

**作用**：临时收起当前未完成工作。

```bash
git stash
```

**典型场景**：正在做 feature A，但需要临时切换任务、切换分支或同步远程。

**相似概念**：commit 是正式历史；stash 是临时保存。

**边界**：stash 不是长期任务管理工具，也不能替代有意义的 commit。

---

## 13.2 `git stash pop`

**作用**：恢复此前 stash 的工作，并在成功应用后移除对应 stash。

```bash
git stash pop
```

**边界**：恢复时仍可能发生冲突。操作后应检查：

```bash
git status
```

---

# 14. Issue / PR / Review / Milestone：不是 Git 命令，但属于本月工作流

## Issue

用于描述明确任务或问题。

```text
Issue
↓
Branch
↓
Commit
↓
Push
↓
PR
```

**边界**：Issue 不是 Branch，也不是 Commit。

## Pull Request（PR）

用于提出把某分支变化合入目标分支，并让变化可审查、讨论、验证。

```text
PR = 提出和审查变化的协作过程
Merge = 实际把历史合并
```

**边界**：PR 不是 Merge 本身。

## Review

重点检查 Files changed、Commits、是否符合 Issue、是否应该 Merge。

**边界**：Review 不是“看一眼就 Merge”。

## Milestone

把多个 Issue/PR 组织到阶段目标中，例如 `MVP v0.1`。

**边界**：Milestone 是项目管理工具，不改变 Git 历史。

---

# 15. 相似命令总对照

## 15.1 `init` vs `clone`

| `git init` | `git clone` |
|---|---|
| 本地从零创建仓库 | 从已有远程仓库复制 |
| 通常还需配置 remote | 通常已有 remote |
| 适合新项目 | 适合已有项目 |

## 15.2 `status` vs `diff` vs `log`

| 命令 | 核心问题 |
|---|---|
| `git status` | 现在仓库处于什么状态？ |
| `git diff` | 当前具体改了什么？ |
| `git log` | 过去提交了什么？ |

## 15.3 `add` vs `commit` vs `push`

```text
git add
Working → Staging

git commit
Staging → Local Repository

git push
Local Repository → Remote Repository
```

## 15.4 `fetch` vs `pull`

```text
fetch
= 获取远程变化，不直接整合

pull
= 获取远程变化 + 整合
```

## 15.5 `restore` vs `restore --staged`

```text
restore
= 文件改错了，我不要这些 Working 修改

restore --staged
= add 错了，我保留修改，只取消暂存
```

## 15.6 `amend` vs `revert` vs `reset`

- `commit --amend`：修改最近一次提交，适合尚未共享的最近提交。
- `revert`：新建反向提交，适合公开/共享历史中的错误。
- `reset`：可能移动/改写历史，本月只理解风险，不把 `--hard` 当万能撤销。

## 15.7 `stash` vs `commit`

| `stash` | `commit` |
|---|---|
| 临时保存 | 正式历史 |
| 适合短期任务切换 | 适合形成可解释版本 |
| 不用于长期记录 | Git 历史核心 |

---

# 16. 本月标准工作流

## 16.1 本地基础流程

```bash
git status
git diff
git add <file>
git status
git commit -m "..."
git log
```

## 16.2 本地 → GitHub

```text
修改
↓
status
↓
diff
↓
add
↓
commit
↓
push
```

## 16.3 标准协作流程

```text
Issue
↓
Branch
↓
修改
↓
Commit
↓
Push
↓
Pull Request
↓
Review
↓
Merge
↓
同步 main
```

---

# 17. 出错时的决策树

```text
发现 Git 问题
│
├─ 先运行 git status
│
├─ 文件还没 add？
│    └─ git diff
│       └─ 确定不要修改 → git restore <file>
│
├─ 文件已经 add，但不想提交？
│    └─ git restore --staged <file>
│
├─ 最近一次 commit 有小错误？
│    └─ 尚未共享 → 谨慎考虑 git commit --amend
│
├─ 错误 commit 已经公开？
│    └─ 优先 git revert <commit>
│
├─ 正在做一半，需要临时切任务？
│    └─ git stash
│       ...
│       git stash pop
│
├─ 想看远程变化但暂时不整合？
│    └─ git fetch
│
├─ 确认要同步远程并整合？
│    └─ git pull
│
└─ 想使用 git reset --hard？
     └─ 先停下
        确认会删除什么以后再决定
```

---

# 18. 本月最重要的安全规则

1. **任何不确定时先 `git status`。**
2. `git diff` 后再决定是否 `add` 或 `restore`。
3. `git add` 不等于 commit。
4. `git commit` 不等于 push。
5. `git push` 只推已经 commit 的历史。
6. `.env` 不提交。
7. 不把所有任务直接做在 `main`。
8. Merge Conflict 要人工理解冲突，不要随机删标记。
9. `git restore <file>` 可能丢弃未提交修改。
10. `git commit --amend` 会改变最近一次 commit。
11. 已公开的错误提交优先考虑 `git revert`。
12. `git stash` 是临时保存，不是长期存储。
13. `git fetch` 适合“先看远端发生了什么”。
14. 不知道 `git reset --hard` 会删什么时，不执行。
15. 删除、恢复、reset 前先确认仓库、分支和当前状态。

---

# 19. 第 1 月命令速查表

| 目的 | 命令 | 核心含义 |
|---|---|---|
| 当前路径 | `pwd` | 我在哪 |
| 查看目录 | `ls` | 这里有什么 |
| 切换目录 | `cd <path>` | 去另一个目录 |
| 创建目录 | `mkdir <dir>` | 新建目录 |
| 创建文件 | `touch <file>` | 新建空文件 |
| 复制 | `cp` | 保留原文件并复制 |
| 移动/重命名 | `mv` | 改位置或名称 |
| 删除 | `rm` | 删除文件，谨慎 |
| Git 版本 | `git --version` | 检查安装 |
| Git 配置 | `git config` | 配置身份等 |
| 初始化 | `git init` | 普通目录 → Git Repo |
| 克隆 | `git clone <url>` | Remote → 新 Local |
| 查看状态 | `git status` | 不确定时先看 |
| 查看差异 | `git diff` | 检查未暂存修改 |
| 暂存 | `git add <file>` | Working → Staging |
| 提交 | `git commit -m "..."` | Staging → Local |
| 查看历史 | `git log` | 检查历史 |
| 查看分支 | `git branch` | 查看分支 |
| 新建并切换 | `git switch -c <branch>` | 创建任务分支 |
| 切换分支 | `git switch <branch>` | 切换工作分支 |
| 合并 | `git merge <branch>` | 指定分支合入当前分支 |
| 查看远程 | `git remote -v` | 查看 remote |
| 添加远程 | `git remote add origin <url>` | 关联远程仓库 |
| 推送 | `git push` | Local → Remote |
| 仅获取 | `git fetch` | 获取远程变化，不直接整合 |
| 拉取 | `git pull` | 获取并整合 |
| 恢复文件 | `git restore <file>` | 丢弃 Working 修改 |
| 取消暂存 | `git restore --staged <file>` | Staging → Working |
| 修正最后提交 | `git commit --amend` | 谨慎修改最近 commit |
| 安全撤销提交 | `git revert <commit>` | 新建反向 commit |
| 临时保存 | `git stash` | 暂时收起未完成修改 |
| 恢复暂存工作 | `git stash pop` | 恢复 stash |
| 高风险恢复 | `git reset` | 本月主要理解风险边界 |

---

# 20. 月底自测

- `git add` 和 `git commit` 的本质区别是什么？
- `git commit` 和 `git push` 的本质区别是什么？
- Working、Staging、Local、Remote 分别是什么？
- `git status` 为什么值得高频使用？
- `git diff` 在什么阶段最有价值？
- `git init` 和 `git clone` 的区别是什么？
- `git fetch` 和 `git pull` 的区别是什么？
- `git restore` 和 `git restore --staged` 分别操作哪一层？
- `git commit --amend` 适合什么场景？
- 为什么公开历史优先 `git revert`？
- `git stash` 适合什么，不适合什么？
- 为什么不能把 `git reset --hard` 当作万能撤销？
- Branch 解决了什么工程问题？
- PR 和 Merge 有什么区别？
- Merge Conflict 为什么发生？
- 为什么 `.env` 不应该被提交？
- 为什么所有修改都直接做在 `main` 不是好的协作方式？
- 遇到 Git 错误时，你的第一步应该是什么？

---

# 21. 最终原则

> **会解释、会操作、会恢复、留下证据。**

Git 学习的目标不是记住最多命令，而是形成以下稳定习惯：

```text
先判断状态
↓
再判断层级
↓
再选择命令
↓
执行后再次验证
↓
留下可追踪历史
```

当你不知道下一步该输入什么时，不要立刻搜索“神奇命令”。

先运行：

```bash
git status
```

然后问自己：

> **问题现在到底发生在哪一层？**
