# git 入门

## 核心概念

### 核心价值

- 版本控制：记录“谁在什么时候改了什么”。
- 分布式：每个开发者本地都有完整的仓库和历史。
- 高效协作：通过分支、合并、代码评审来并行开发。

### 配置

配置命令：

```shell
git config -l # 查看当前生效的所有配置
git config 键名 # 查看当前生效的某个配置
git config --范围 键名 "键值" # 更新配置
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