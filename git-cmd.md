# 使用流程

1. 初始化仓库

   ```git
   git init
   ```

   这将创建一个隐藏的 `.git` 文件夹，Git 会在其中存储版本控制信息。

2. 克隆远程仓库

   ```git
   git clone https://github.com/user/repo.git
   ```

3. 检查状态，添加文件到暂存区（将所有修改添加），提交更改

   ```git
   git add <文件名>
   git add .
   git commit -m "提交信息"
   ```

4. 查看提交历史

   ```git
   git log
   ```

5. 推送到远程仓库

   - 添加远程仓库的连接
   - 查看当前连接的远程仓库
   - 拉取远程仓库的更新
   - 将本地文件添加到仓库中
   - 将本地修改推送到远程仓库

   ```git
   git remote add origin https://github.com/ayuan-01/git-note
   git remote -v
   git pull origin <分支名>
   git add .
   git commit -m "Initial commit"
   git push origin main(分支名)
   ```

6. 分支

   - 创建新分支
   - 切换分支
   - 合并分支

   ```sh
   git branch <分支名>
   git checkout <分支名>
   git merge <分支名>
   git merge brc2 # 是把brc2分支合并到当前分支上
   
   # 切换分支时 Git 会自动更新工作区的文件，使其与目标分支的最新提交内容一致。这是 Git 分支切换的核心机制。
   ```
   
   - 查看当前分支
   
   
   ```sh
   git branch
   # 可以使用命令查看更加详细的分支信息，包括远程和本地分支
   git branch -a
   ```

远程次分支的创建，删除

```sh
# 给远程仓库创建新的分支
git checkout -b feature/new-page
# 将本地分支推送到远程仓库
git push -u origin  feature/new-page
# 查看跟踪的远程分支
git remote show origin
# 删除远程分支和本地分支
git push origin --delete feature/new-page
git branch -d feature/new-page
# 修剪本地仓库中远程已经不存在的追踪分支
git remote prune origin
```



## git checkout

主要任务是切换分支和回到之前某个特定的commit/file。

1. 查看之前提交的历史版本。

```sh
git log --oneline		# 获取想要得到的某个commit的哈希值
git checkout e4f5g6h	# 切换到这个提交
#    Note: switching to 'e4f5g6h'.
#	You are in 'detached HEAD' state...
# “detached HEAD”（分离头指针）状态意味着你的 `HEAD` 不再指向任何一个分支的末端，而是直接指向了一个具体的提交。 
git log --oneline 		# 会发现 HEAD 指向了 e4f5g6h，而不是 main。

# 当你确认完毕后，想回到最新的 main 分支，只需：
git checkout main
```

关键点：这个操作是安全的。它没有删除任何提交。commit 3 依然存在，只是 HEAD 暂时离开了它。

当切换到之前的某个提交时，本地的文件夹作为工作区会恢复的之前的状态，当返回main分支的时候，又会回来。需要注意的是当本地有修改没提交时，这个修改可能会被保留。

2. 撤销某个文件的未提交修改。

加入在commit 3的基础上修改了`profile.html`文件，但改的一团糟。想要放弃所有修改，回到`commit 3`时候的版本。

```sh
git status
# 输出：
# On branch main
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git restore <file>..." to discard changes in working directory)
#         modified:   profile.html
#
# no changes added to commit (use "git add" and/or "git commit -a")

git checkout -- profile.html

git status
# 输出：
# On branch main
# nothing to commit, working tree clean
```

## git stash

临时储物柜。**临时保存你当前未提交的修改**，让工作区变得干净，以便你可以切换到其他分支或处理其他任务。之后你可以再把这些修改“取出来”。

```sh
# 你正在 main 分支上开发一个新功能（已经修改了 feature.js 和 style.css，但还没提交）。突然，你的 leader 说：“线上有个紧急 Bug，立刻切到 hotfix 分支去修复！”

# 你不能直接 git checkout hotfix，因为 Git 会阻止你切换分支，说你的工作区有未提交的修改。
git stash/git stash push -m "WIP: 正在开发新功能，一半"
# Git 会把你的所有修改（包括暂存和未暂存的）从工作区和暂存区“拿走”，存到一个栈（stash list）里，然后你的工作区会变得和 `HEAD` (`commit 3`) 完全一样。

# 切换分支，修改bug
git checkout hotfix
# ... 修复 Bug, 提交 ...
git checkout main
git merge hotfix

# 查看储藏列表
git stash list
# stash@{0}: On main: WIP: 正在开发新功能，一半

# 恢复储藏的内容（并从列表中删除）
git stash pop

# 或者，如果你只想查看，不想删除
# git stash apply

# 现在，你对 `feature.js` 和 `style.css` 的修改又回到了工作区，你可以继续开发了。
```

## git reset

它会**移动当前分支的 HEAD 指针**，从而“重写”提交历史。**主要用于本地的提交历史整理，千万不要对已经推送到远程仓库的提交使用它**。回退到某个提交去修改，然后再次提交。

```sh
# 问题：你刚刚提交了 commit 3 (feat: 添加了用户个人资料页面)。但紧接着你发现，这个提交里有一个小错误（比如有个 console.log 没删掉），或者你想把这次提交拆分成两个更小的提交。

# HEAD~1 表示 HEAD 的前一个提交，也就是 commit 2
git reset --soft HEAD~1

# - **发生了什么？**
#     - `main` 分支的指针从 `commit 3` 移动回了 `commit 2`。
#     - `commit 3` 这个提交记录“消失”了（从分支引用上看）。
#     - **但是**，`commit 3` 中的所有文件修改都被保留在了**暂存区**。
# - **现在状态**：
git status
# 输出：
# On branch main
# Changes to be committed:
#   (use "git restore --staged <file>..." to unstage)
#         new file:   profile.html
#         modified:   app.js

# - **后续操作**：
#     你可以修改文件（比如删除 `console.log`），然后再次 `git add` 和 `git commit`，形成一个“干净”的新提交。
# - 如果新提交（commit 3'）的修改与原 commit 3 完全一致，Git 会认为这是“重复提交”。
# - 如果部分修改被删除（如 console.log），则新提交会覆盖原 commit 3 的对应内容。
# - 原 commit 3 的提交记录已消失（因为 HEAD~1 回退了它），但修改已整合到新提交中。

git reset --hard HEAD~1 # 彻底丢弃提交和修改：就像 `commit 3` 从未存在过一样。
git reset --mixed HEAD~1# 默认选项 git reset HEAD~1。commit 3的修改被保留在了工作区，但不在暂存区。
```

## git revert

撤销远程的某个commit。

```sh
# 撤销一个已经推送到远程的提交
# 你不仅提交了 commit 3，还 git push origin main 把它推送到了远程仓库。然后，测试团队发现这个“用户个人资料页面”功能有一个严重的性能问题，导致整个网站变慢，需要立刻回滚。
# 绝对不能使用 git reset！因为 reset 会重写本地历史，导致你的本地历史和远程历史不一致，git push 会被拒绝。强行推送 (git push --force) 会覆盖远程仓库的历史，给团队其他成员带来灾难。

# 撤销 commit 3 的修改
git revert a1b2c3d
# - Git 会打开一个编辑器，让你为新提交填写信息。默认信息通常是 "Revert "feat: 添加了用户个人资料页面""，这很清晰。保存并关闭。

git log --oneline
# 输出：
# f9e8d7c (HEAD -> main) Revert "feat: 添加了用户个人资料页面" <- 这是新的 commit 4
# a1b2c3d (origin/main) feat: 添加了用户个人资料页面          <- 旧的 commit 3 依然存在
# e4f5g6h fix: 修复了登录页面的一个样式问题
# i7j8k9l feat: 完成了项目初始化和登录功能

# 安全推送
git push origin main
```

## 配置用户名和邮箱

- 用户名和邮箱用于查看提交信息
```git
$ git config --global user.name
ayuan-01
$ git config --global user.email
sywangxdu@163.com
```
- 配置当前仓库的用户名和邮箱
```git
$ git config --local user.name
$ git config --local user.email
```
- 查看配置
```git
git config --list
```

# 创建私人仓库并链接本地仓库

创建一个 Git 私人仓库并与本地仓库链接的流程如下：

### 1. 创建 Git 私人仓库

在常用的代码托管平台（如 GitHub、GitLab、Bitbucket）上，均可以创建私人仓库。以 GitHub 为例：

#### 1.1 登录 GitHub

- 访问 [GitHub](https://github.com) 并登录你的账户。

#### 1.2 创建新仓库

- 在主页点击右上角的 “+” 按钮，选择“New repository”。
- 输入仓库名称，选择“Private”（私有）。
- 选择是否初始化仓库（例如添加 README 文件等），然后点击“Create repository”。

### 2. 初始化本地仓库

#### 2.1 在本地创建新项目目录

```
mkdir my-project
cd my-project
```

#### 2.2 初始化本地 Git 仓库

```
git init
```

#### 2.3 添加文件并提交到本地仓库

```
echo "# My Project" >> README.md
git add README.md
git commit -m "Initial commit"
```

### 3. 连接本地仓库与 GitHub 私有仓库

#### 3.1 获取 GitHub 私有仓库的 URL

- 在 GitHub 仓库主页，点击 “Code” 按钮，复制仓库的 HTTPS 或 SSH URL。

#### 3.2 将本地仓库与远程仓库链接

```
git remote add origin <your-repository-url>
```

将 `<your-repository-url>` 替换为从 GitHub 复制的 URL。

#### 3.3 推送本地代码到远程仓库

```
git push -u origin master
```

执行上述命令后，你的本地仓库代码将被推送到 GitHub 上的私人仓库。

### 4. 日常工作流程

之后的日常开发流程：

- 修改代码后使用 `git add` 和 `git commit` 提交变更。
- 使用 `git push` 将提交推送到远程仓库。

### 其他注意事项

- 如果使用 SSH 方式，需要提前设置 SSH 密钥，并将公钥添加到 GitHub 账户。

# 作为private项目成员

作为一个 Git 私有项目的成员，如果你被授予了访问权限，你可以按照以下步骤操作：

### 1. 获得访问权限

项目所有者需要先将你添加为项目的**协作者**，通常有以下流程：

#### 1.1 项目所有者添加你为协作者（以 GitHub 为例）

- 项目所有者打开 GitHub 仓库页面，点击 "Settings"。
- 在左侧菜单中，选择 "Collaborators & teams"。
- 在 "Collaborators" 部分，输入你的 GitHub 用户名并发送邀请。
- 你会收到一封邀请邮件，点击邮件中的链接接受邀请，或者在 GitHub 上直接接受邀请。

### 2. 克隆私有仓库

在你被授予访问权限并接受邀请后，就可以克隆这个私有仓库。

#### 2.1 使用 HTTPS 克隆

你可以通过 HTTPS URL 克隆仓库：

```
git clone https://github.com/username/repository.git
```

你需要替换 `username` 为项目所有者的用户名，`repository` 为仓库名。

- 系统会提示你输入 GitHub 用户名和密码（推荐使用 GitHub 生成的 **Personal Access Token** 代替密码）。

#### 2.2 使用 SSH 克隆（推荐）

如果你已经配置了 SSH 密钥，可以通过 SSH 方式克隆仓库：

```
git clone git@github.com:username/repository.git
```

- SSH 方式不需要每次输入用户名和密码，但需要在 GitHub 上提前配置 SSH 公钥。

### 3. 提交更改

与公共仓库类似，你可以对本地仓库进行以下操作：

#### 3.1 拉取最新的仓库状态

在进行更改之前，先拉取远程仓库的最新代码：

```
git pull origin master
```

#### 3.2 提交本地更改

1. 添加更改：

```
git add .
```

1. 提交更改：

```sh
git commit -m "Your commit message"
```

1. 推送更改到远程仓库：

```sh
git push origin master
```

### 4. 日常工作流程

作为项目成员，你的日常工作流程通常包括以下步骤：

1. **拉取最新代码**：通过 `git pull` 获取远程仓库的最新代码。
2. **开发和修改**：在本地进行开发、修改文件。
3. **提交并推送**：通过 `git add`、`git commit` 提交更改，然后使用 `git push` 推送到远程仓库。

### 5. 可能的权限问题

如果你尝试推送代码时遇到权限问题：

- 确认你已被正确添加为协作者。
- 如果是 SSH 克隆，检查 SSH 公钥是否已添加到你的 GitHub 账户。
- 如果是 HTTPS 方式，确保你的用户名和 Personal Access Token 是正确的。

这样你就可以作为项目成员与 Git 私有仓库进行协作了。



# 删除文件

在 Git 中删除文件可以通过以下步骤完成：

### 1. 使用 `git rm` 命令删除文件

假设你想从 Git 仓库中删除某个文件，可以使用以下命令：

```
git rm <file-name>
```

这将从你的工作目录中删除文件，并同时将删除操作加入到暂存区（staging area），以便在下次提交时将该操作记录到仓库中。

#### 例子：

```
git rm myfile.txt
```

### 2. 提交删除操作

删除文件后，需要将该操作提交到 Git 仓库：

```
git commit -m "Removed myfile.txt"
```

### 3. 推送到远程仓库

最后，将删除操作推送到远程仓库：

```
git push origin master
```

### 4. 仅从 Git 中删除，但保留本地文件

如果你想仅从 Git 仓库中删除文件，但希望文件仍然保留在你的本地文件系统中，可以使用 `--cached` 选项：

```
git rm --cached <file-name>
```

这会将文件从 Git 仓库中移除，但不会从你的工作目录中物理删除该文件。

#### 例子：

```
git rm --cached myfile.txt
```

然后按照同样的步骤，提交并推送更改：

```
git commit -m "Removed myfile.txt from Git tracking but kept it locally"
git push origin master
```

### 5. 删除整个目录

如果你想删除整个目录及其内容，可以使用 `-r` 递归删除选项：

```
git rm -r <directory-name>
```

#### 例子：

```
git rm -r mydirectory
```

同样需要提交和推送更改：

```
git commit -m "Removed directory mydirectory"
git push origin master
```

### 总结

- `git rm <file-name>`：删除文件并提交删除操作。
- `git rm --cached <file-name>`：仅从 Git 中删除文件，保留本地文件。
- `git rm -r <directory-name>`：删除整个目录及其内容。

通过这些命令，你可以轻松管理 Git 仓库中的文件删除操作。

