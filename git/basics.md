# git 入门

## 核心概念

### 核心价值

- 版本控制：记录“谁在什么时候改了什么”。
- 分布式：每个开发者本地都有完整的仓库和历史。
- 高效协作：通过分支、合并、代码评审来并行开发。

### 配置

配置命令：

```shell
git config -l # 查看当前生效的所有配置。
git config 键名 # 查看当前生效的某个配置。
git config --范围 键名 "键值" # 更新配置。
```

#### 范围

- `local`：默认、仅对当前仓库有效。
- `global`：对当前用户的所有仓库有效。
- `worktree`：仅对当前工作数生效。

优先级从高到低：worktree > local > global。

#### 常见配置

- `user.name`：必配，配置用户名。后续提交使用该用户名。
- `user.email`：必备，配置用户邮箱。后续提交使用该邮箱。
- `core.editor`：配置默认文本编辑器。
- `core.ignorecase`：配置是否忽略文件名的大小写。默认是 `true`，即忽略大小写。
- `init.defaultbranch`：配置初始化后，默认的分支名。

使用举例：

```shell
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global core.editor "code --wait"
git config --global core.ignorecase false
git config --global init.defaultbranch "main"
```

### 初始化

使用 `git init` 命令初始化一个新的 git 仓库。该命令会在当前目录下创建一个 `.git` 目录里面记录了仓库的所有信息，该目录不可删除，一旦删除该仓库就不存在了。

### .gitignore 文件

使用 `.gitignore` 文件，将不需要的文件排除。

### 暂存和提交

![三层模型](./assets/three-model.svg)

#### 常见流程

1. 可选、查看当前状态。

    ```shell
    git status
    ```

2. 将改动内容暂存。

    ```shell
    git add . # 暂存当前目录及子目录下的所有文件。
    git add 文件1 文件2 # 暂存指定文件。
    ```

3. 将暂存内容提交。

    ```shell
    git commit # 打开默认文本编辑器，在文本编辑器中，编写提交信息。
    git commit -m "提交信息" # 直接在命令行中，编写提交信息。
    ```

#### 提交消息规范

| 提交类型 | 标识符 |
| --- | --- |
| 功能新增 | `feat` |
| 问题修复 | `fix` |
| 文档更新 | `docs` |
| 工具变更 | `chore` |
| 回滚操作 | `revert` |

#### 提交信息修改

该命令，修改最新的提交消息。

```shell
git commit --amend
```

#### 回滚

将指定的提交覆盖到当前工作区根目录。

要保证工作区干净。

```shell
git restore --source 提交id .
```

### 分支管理

分支本质上是一个可移动的提交指针。

不要在主分支上改动。

#### 分支基本操作

1. 查看分支。

    ```shell
    git branch
    ```

2. 分支重命名。

    ```shell
    git branch -m 新的分支名 # 重命名当前分支。
    git branch -m 指定分支名 新的分支名 # 重命名指定分支。
    ```

3. 创建分支。

    ```shell
    git branch 新的分支名 # 创建一个新分支，指针和当前分支相同。
    ```

4. 切换分支。

    ```shell
    git switch 指定分支名 # 切换到指定分支。
    git switch - # 切换到上一个分支。
    git switch -c 新的分支名 # 创建并切换到新分支。
    ```

5. 删除分支。

    ```shell
    git branch -d 指定分支名 # 删除指定分支。
    ```

### 远程仓库

#### 生成密钥对

```shell
ssh-keygen -t ed25519 -C "注册github的邮箱地址" -f ~/.ssh/xxx_github_key
```

#### 配置密钥对规则

1. 打开 `~/.ssh/config` （是一个无拓展名的文件），没有就创建一个。

2. 配置规则。

    ```yaml
    # 规则1。
    Host gmail.github.com # 当连接 git@gmail.github.com 时会匹配到此规则。
        HostName github.com # 实际上链接发出的主机。
        IdentityFile ~/.ssh/gmail_github_key # 使用的私钥文件。  
        User git # 固定写法。
    ```

3. 测试链接。

```shell
ssh -T git@配置的主机名
```

#### 仓库和别名

1. 查看当前远程仓库别名。

    ```shell
    git remote # 简要信息。
    git remote -v # 详细信息。
    ```

2. 添加别名。

    ```shell
    git remote add 远程仓库别名 远程仓库ssh地址
    ```

3. 修改别名。

    ```shell
    git remote rename 指定的别名 新别名
    ```

4. 删除别名。

    ```shell
    git remote remove 指定的别名
    ```

#### 分支同步

##### 将本地某分支，推送到指定仓库。

```shell
git push -u 远程仓库别名 本地分支名
```

细节：

- 如果远程没有相关分支，则在远程创建同名分支。
- 推送完成后，会在本地创建跟踪分支。
    - 跟踪分支自动命名为 `仓库别名/分支名`。
    - 跟踪分支是只读的，它的作用是同步远程分支。
    - 推送完成后，会自动同步跟踪分支。
- 由于使用了参数 `-u`，在跟踪分支创建后，会自动把本地分支绑定到跟踪分支。后续只需要使用 `git push`，即可完成相应的分支推送。

##### 从远程仓库，获取更新。

1. 拉取远程信息。不会自动合并。

```shell
git fetch 远程仓库别名 远程分支名
```

2. 合并分支。

```shell

```