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