# Git 分支（Branch）详细教程

## 一、什么是分支？

### 通俗理解：
**分支就是一条独立的开发线。你可以在自己的分支上写代码，不影响别人，也不影响主分支的稳定代码。

### 形象比喻：
- 主分支（main/master）是**高速公路主路
- 每个开发分支是**辅路/岔路
- 你在岔路上开（开发新功能），不影响主路通行
- 开发完了，通过**合并（merge）**把辅路并入主路

---

## 二、为什么要用分支？

1. **隔离开发新功能不影响主线**：在新功能分支上随便折腾，搞坏了也不怕
2. **多人协作**：每个人在自己的分支上开发，互不干扰
3. **版本管理**：可以同时维护多个版本（比如正式版、开发版）
4. **快速切换**：一秒切换到不同分支，查看不同版本的代码

---

## 三、分支命令速查表

| 命令 | 作用 |
|------|------|
| `git branch` | 查看所有本地分支 |
| `git branch -a` | 查看所有分支（包括远程） |
| `git branch 分支名` | 创建新分支 |
| `git checkout 分支名` | 切换到指定分支 |
| `git switch 分支名` | 切换分支（Git 2.23+ 新命令） |
| `git checkout -b 分支名` | 创建并切换到新分支 |
| `git switch -c 分支名` | 创建并切换（新命令写法） |
| `git merge 分支名` | 把指定分支合并到当前分支 |
| `git branch -d 分支名` | 删除分支（合并后删除） |
| `git branch -D 分支名` | 强制删除分支（没合并也删） |
| `git log --graph` | 图形化查看分支历史 |

---

## 四、分支的本质

### Git 的内部原理

Git 的分支本质上就是一个**指向提交对象的指针**。

```
         master
           │
           ▼
A ◄── B ◄── C
           ▲
           │
         HEAD
```

- 每个提交（commit）都有一个唯一的ID
- 分支就是一个指向某个提交的"标签/指针
- `HEAD` 是一个特殊指针，指向当前所在的分支

创建新分支时，只是创建了一个新指针：

```
         master  new-branch
           │        │
           ▼        ▼
A ◄── B ◄── C
                    ▲
                    │
                  HEAD
```

在新分支上提交后：

```
         master
           │
           ▼
A ◄── B ◄── C ◄── D ◄── E
                         ▲
                         │
                       new-branch
                         ▲
                         │
                       HEAD
```

---

## 五、实战操作流程

### 场景：开发一个新功能

#### 1. 创建并切换到新分支

```bash
# 方式一：传统写法
git checkout -b feature-login

# 方式二：新写法（推荐）
git switch -c feature-login
```

#### 2. 在新分支上开发

```bash
# 修改代码...
git add .
git commit -m "完成登录界面"
# 继续修改...
git add .
git commit -m "完成登录逻辑"
```

现在分支历史变成：

```
main:    A -- B -- C
                    \
feature-login:       D -- E
```

#### 3. 切换回主分支

```bash
git switch main
```

#### 4. 把功能分支合并到主分支

```bash
git merge feature-login
```

合并后：

```
main:    A -- B -- C -- D -- E
                    /
feature-login:    D -- E
```

#### 5. 删除功能分支（可选）

```bash
git branch -d feature-login
```

---

## 六、合并冲突（Merge Conflict）

### 什么是合并冲突？

当两个分支修改了**同一个文件的同一行**，Git 不知道该用哪个，就会报错，需要手动解决。

### 冲突长什么样？

打开冲突的文件会看到：

```
<<<<<<< HEAD
这是 main 分支的内容
=======
这是 feature 分支的内容
>>>>>>> feature-login
```

- `<<<<<<< HEAD` 到 `=======` 是当前分支的内容
- `=======` 到 `>>>>>>> feature-login` 是要合并的分支的内容

### 如何解决冲突？

1. 打开冲突文件，手动编辑保留想要的内容
2. 删除 `<<<<<<<`、`=======`、`>>>>>>>` 这些标记
3. `git add 文件名`（标记冲突已解决）
4. `git commit` 完成合并

---

## 七、常用分支策略

### 1. Git Flow（经典模型）

```
master（主分支，始终保持稳定）
  │
  └── develop（开发分支）
        │
        ├── feature-xxx（功能分支）
        ├── feature-yyy
        └── release（发布分支）
```

### 2. GitHub Flow（简单模型）

- `main`：主分支，生产环境
- 直接从 `main` 切功能分支
- 功能开发完提 Pull Request 合并回 `main`

### 3. 日常开发建议

- **main/master：主分支，永远保持可发布状态
- **develop**：开发分支，集成最新功能
- **feature/xxx**：功能分支，开发新功能
- **bugfix/xxx**：bug修复分支
- **hotfix/xxx**：线上紧急修复

---

## 八、实用技巧

### 1. 查看分支重命名

```bash
# 重命名当前分支
git branch -m 新名字

# 重命名指定分支
git branch -m 旧名字 新名字
```

### 2. 临时保存修改（stash）

正在开发到一半，需要切到别的分支怎么办？

```bash
# 保存当前修改（工作区+暂存区）
git stash

# 查看保存的列表
git stash list

# 恢复最近的保存
git stash pop

# 恢复指定的保存
git stash apply stash@{0}
```

### 3. 图形化查看分支历史

```bash
git log --oneline --graph --all
```

输出类似：
```
*   abc1234 Merge branch 'feature'
|\
| * def5678 完成功能开发
|/
*   ghi9012 初始提交
```

---

## 九、远程分支

### 推送到远程

```bash
# 第一次推送，建立关联
git push -u origin 分支名

# 之后直接推送
git push
```

### 拉取远程分支

```bash
# 拉取所有远程更新
git fetch

# 拉取并合并当前分支
git pull
```

### 删除远程分支

```bash
git push origin --delete 分支名
```
