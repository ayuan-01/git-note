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

   ```git
   git branch <分支名>
   git checkout <分支名>
   git merge <分支名>
   ```

   - 查看当前分支

     ```git
     git branch
     可以使用命令查看更加详细的分支信息，包括远程和本地分支
     git branch -a
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

