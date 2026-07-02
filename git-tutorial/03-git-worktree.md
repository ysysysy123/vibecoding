# Git 工作树（git worktree）详细教程

## 一、什么是工作树（Worktree）？

### 通俗理解

**工作树**就是让你可以同时打开同一个仓库的多个分支，每个分支在不同的文件夹里。**

以前：你只有一个工作目录，要切换分支必须 `git switch`，切来切去很麻烦。

现在：用 `git worktree`，你可以有多个文件夹，每个文件夹对应一个分支，同时打开、同时修改、互不干扰。

---

## 二、为什么要用 worktree？

### 场景1：开发到一半，需要紧急修bug

**以前（没有 worktree）：**
```bash
# 功能开发到一半，代码还不能提交
git stash           # 先临时存起来
git switch main     # 切到主分支
# ...修bug...
git switch feature  # 切回来
git stash pop      # 恢复之前的工作
```

**现在（有 worktree）：**
```
/my-project/           ← 功能分支，继续开发
/my-project-hotfix/   ← 主分支，修bug
```
两边同时进行，不用切来切去！

### 场景2：同时对比两个分支的代码

直接在两个文件夹里打开，用 diff 工具对比，一目了然。

### 场景3：跑测试不影响开发

在另一个 worktree 里跑长时间的测试，自己这边继续写代码。

---

## 三、worktree 命令速查表

| 命令 | 作用 |
|------|------|
| `git worktree list` | 查看所有工作树 |
| `git worktree add <路径> <分支名>` | 添加一个新的工作树 |
| `git worktree add -b <新分支> <路径>` | 新建分支并创建工作树 |
| `git worktree remove <路径>` | 删除工作树 |
| `git worktree prune` | 清理已删除的工作树记录 |

---

## 四、worktree 的结构

```
my-project/                    ← 主工作树（你 clone 下来的那个）
    ├── .git/                   ← 真正的 Git 仓库数据（只有一份！）
    ├── src/
    └── ...

my-project-feature/             ← 新增的工作树（链接到同一个 .git）
    ├── .git                    ← 只是个文件，指向主仓库
    ├── src/
    └── ...
```

**关键点：**
- 只有一个真正的 `.git` 目录存放所有 Git 数据
- 其他 worktree 的 `.git` 只是一个指针文件
- 所有 worktree 共享同一个仓库的提交历史、分支信息
- 每个 worktree 有自己独立的工作区和暂存区

---

## 五、实战操作

### 1. 查看当前工作树

```bash
git worktree list
```

输出示例：
```
/workspace  abc1234 [main]
```

### 2. 添加一个新工作树

#### 场景A：基于已有分支创建工作树

```bash
git worktree add ../my-project-dev develop
```

解释：
- `../my-project-dev`：新工作树的路径（放在当前目录外面）
- `develop`：要检出的分支名

#### 场景B：创建新分支并建立工作树

```bash
git worktree add -b feature-new ../my-project-feature
```

解释：
- `-b feature-new`：创建一个叫 `feature-new` 的新分支
- `../my-project-feature`：新工作树的路径
- 新分支基于当前分支的最新提交

### 3. 在新工作树里工作

```bash
cd ../my-project-feature
# 正常写代码、git add、git commit...
# 和普通的 Git 仓库一模一样！
```

### 4. 查看所有工作树

```bash
git worktree list
```

输出示例：
```
/workspace                     abc1234 [main]
/workspace/../my-project-dev   def5678 [develop]
/workspace/../my-project-feat  ghi9012 [feature-new]
```

### 5. 删除工作树

#### 方式一：直接删文件夹，然后清理

```bash
rm -rf ../my-project-feature
git worktree prune
```

#### 方式二：用命令删除

```bash
git worktree remove ../my-project-feature
```

---

## 六、使用注意事项

### ⚠️ 重要规则

1. **同一个分支不能同时在两个 worktree 里检出**
   - Git 会阻止你这么做，不然两个地方改同一个分支会混乱
   
2. **提交是共享的**
   - 在 worktree A 里 commit 了，worktree B 里能看到这个提交
   - 但不会自动切换过去，需要手动 `git pull` 或切换

3. **删除 worktree 不会删除分支**
   - 只是删除了工作目录
   - 分支还在仓库里，可以再次检出

4. **路径建议**
   - 建议把 worktree 放在主仓库目录外面
   - 或者主仓库叫 `project/main`，其他叫 `project/feature1`

---

## 七、worktree vs 克隆多份仓库

| 对比项 | git worktree | 克隆多份 |
|--------|-------------|----------|
| 仓库数据 | 只有一份，省空间 | 每份都有完整 .git，占空间 |
| 提交共享 | 天然共享，不用 push/pull | 需要互相 push/pull |
| 分支冲突 | 同一分支不能同时检出 | 可以同时改，push 时冲突 |
| 适用场景 | 临时切换、并行开发 | 完全独立的项目 |

---

## 八、典型工作流示例

### 场景：正在开发新功能，突然要修线上bug

```bash
# 1. 当前在功能分支上开发，代码写了一半
# （不用 stash，不用 commit 半成品）

# 2. 新建一个 worktree 来修 bug
git worktree add ../project-hotfix main

# 3. 去 hotfix 目录修 bug
cd ../project-hotfix
# 修改代码...
git add .
git commit -m "修复线上bug"
git push

# 4. 回到原来的工作目录继续开发
cd /workspace
# 完全不受影响，接着写！
```

---

## 九、小技巧

### 1. 给 worktree 命名规范

建议目录名清晰命名：
```
project-main/        ← 主分支
project-develop/      ← 开发分支
project-feat-xxx/   ← 功能分支
```

### 2. 批量查看所有 worktree 的状态

```bash
# 写个小脚本遍历所有 worktree 执行 git status
for wt in $(git worktree list --porcelain | grep '^worktree ' | sed 's/worktree //'); do
  echo "=== $wt ==="
  git -C "$wt" status -s
done
```

### 3. 在主仓库查看所有 worktree 的分支

```bash
git worktree list
```

---

## 十、什么时候该用 worktree？

✅ **推荐使用的场景：**
- 需要同时在多个分支上工作，频繁切换
- 开发到一半被打断，需要处理其他事情
- 想对比两个分支的运行效果
- 一边写代码一边跑测试

❌ **不需要用的场景：**
- 只是偶尔切一下分支
- 项目很小，切换很快
- 两个完全不相关的项目
