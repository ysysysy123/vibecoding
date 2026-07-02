# Git 实战练习指南

跟着下面的步骤动手练一遍，Git 就掌握了！

---

## 练习一：基础操作（工作区 → 暂存区 → 版本库）

### 目标
理解 Git 三大区域，熟练使用 add/commit/status/log

### 步骤

```bash
# 1. 进入 git-tutorial 目录
cd git-tutorial

# 2. 创建一个练习文件
echo "第一行内容" > practice.txt

# 3. 查看状态（practice.txt 是未追踪状态）
git status

# 4. 添加到暂存区
git add practice.txt

# 5. 再查看状态（现在在暂存区了）
git status

# 6. 提交到版本库
git commit -m "练习：创建practice文件"

# 7. 查看提交历史
git log --oneline

# 8. 修改文件
echo "第二行内容" >> practice.txt

# 9. 查看差异
git diff practice.txt

# 10. 再次提交
git add practice.txt
git commit -m "练习：添加第二行内容"

# 11. 查看历史
git log --oneline
```

---

## 练习二：撤销操作

### 目标
掌握各种撤销场景

### 步骤

```bash
# 1. 修改文件但不 add
echo "错误的修改" >> practice.txt

# 2. 查看状态，然后撤销工作区的修改
git checkout -- practice.txt
# 或 git restore practice.txt

# 3. 验证文件恢复了
cat practice.txt

# 4. 这次 add 之后再撤销
echo "暂存的修改" >> practice.txt
git add practice.txt

# 5. 从暂存区撤回（不影响工作区）
git reset HEAD practice.txt
# 或 git restore --staged practice.txt

# 6. 查看状态（又回到工作区修改了
git status

# 7. 彻底丢弃
git checkout -- practice.txt
```

---

## 练习三：分支基础操作

### 目标
掌握创建、切换、合并分支

### 步骤

```bash
# 1. 查看当前分支
git branch

# 2. 创建并切换到新分支
git switch -c feature-demo

# 3. 在新分支上修改
echo "功能分支的内容" > feature.txt
git add feature.txt
git commit -m "添加功能文件"

# 4. 再提交一次
echo "第二行功能" >> feature.txt
git add feature.txt
git commit -m "更新功能文件"

# 5. 查看分支历史
git log --oneline --graph --all

# 6. 切回主分支
git switch main

# 7. 看看 feature.txt 在不在（不在！因为在别的分支）
ls

# 8. 合并功能分支
git merge feature-demo

# 9. 再看看（feature.txt 出现了！
ls

# 10. 查看历史
git log --oneline --graph --all

# 11. 删除功能分支
git branch -d feature-demo
```

---

## 练习四：解决合并冲突

### 目标
学会处理合并冲突

### 步骤

```bash
# 1. 创建分支 A
git switch -c branch-a
echo "A分支的内容" > conflict.txt
git add conflict.txt
git commit -m "branch-a: 创建文件"

# 2. 切回 main，创建分支 B
git switch main
git switch -c branch-b
echo "B分支的内容" > conflict.txt
git add conflict.txt
git commit -m "branch-b: 创建文件"

# 3. 切回 main，尝试合并 branch-a
git switch main
git merge branch-a

# 4. 再合并 branch-b（会冲突！
git merge branch-b

# 5. 查看冲突文件
cat conflict.txt

# 6. 手动编辑文件，解决冲突（把内容改成你想要的）
# 比如保留两个版本，或者只留一个
# 编辑完保存

# 7. 标记解决
git add conflict.txt
git commit -m "解决合并冲突"

# 8. 查看结果
git log --oneline --graph --all

# 9. 清理测试分支
git branch -D branch-a branch-b
rm conflict.txt
git add .
git commit -m "清理冲突练习文件"
```

---

## 练习五：stash 暂存

### 目标
掌握临时保存修改

### 步骤

```bash
# 1. 在 main 分支上写了一半代码
echo "写到一半的功能" >> practice.txt

# 2. 突然要切换分支，但代码不能提交
# 先 stash 起来
git stash

# 3. 查看 stash 列表
git stash list

# 4. 切到别的分支做事
git switch -c temp-branch
# ...做别的事...
git switch main

# 5. 恢复 stash
git stash pop

# 6. 验证内容回来了
cat practice.txt

# 7. 清理临时分支
git branch -D temp-branch
```

---

## 练习六：git worktree

### 目标
学会使用工作树

### 步骤

```bash
# 1. 查看当前工作树
git worktree list

# 2. 创建一个新工作树（放在上一级目录）
git worktree add ../worktree-demo main

# 3. 查看所有工作树
git worktree list

# 4. 去新工作树看看
cd ../worktree-demo
ls
git status

# 5. 在新工作树里创建个分支
git switch -c worktree-test
echo "worktree里的内容" > worktree.txt
git add worktree.txt
git commit -m "worktree测试提交"

# 6. 回到主工作树
cd /workspace/git-tutorial
# （主工作树完全不受影响！）

# 7. 查看所有工作树
git worktree list

# 8. 删除工作树
git worktree remove ../worktree-demo

# 9. 验证删除了
git worktree list

# 10. 分支还在，可以继续用
git branch
```

---

## 练习七：远程仓库操作

### 目标
掌握和远程仓库交互

### 步骤（如果有远程仓库的话）

```bash
# 1. 查看远程仓库
git remote -v

# 2. 推送当前分支到远程
git push -u origin main

# 3. 拉取远程更新
git pull

# 4. 获取远程有新分支
git fetch
git branch -a

# 5. 切换到远程分支
git switch origin/feature-xxx
```

---

## 常用命令速记口诀

```
改完代码 git add，
暂存好了 git commit，
想看状态 git status，
想看历史 git log。

新开分支 switch -c，
切来切去 switch 名，
写完功能合过来，
git merge 分支名。

改到一半要切走？
git stash 先藏走，
回来接着继续干，
git stash pop 出手。

多分支同时搞？
git worktree 真奇妙，
一个分支一目录，
并行开发效率高。
```

---

## 学习建议

1. **多练**：Git 是工具，用多了自然会
2. **别怕**：Git 有后悔药（reset/reflog），基本删不了东西的
3. **看状态**：拿不准的时候先 `git status`
4. **小步提交**：一次提交做一件事，说明写清楚
5. **用图形工具**：SourceTree、GitKraken、VS Code 内置 Git 都很好用

祝你 Git 愉快！🚀
