# Git & Repo Quick Start

Git 说明: [Git 官方文档](https://git-scm.com/book/zh/v2)

## Git 本地设置

名称设定请遵循示例规范，例如 `Ace Luckly`, `Bruce Ali`

请勿使用`abc.df` 或 `xxx`来进行命名，Git Pre-Commit Hook 会进行拦截

```bash
git config --global user.name "Your Name"
git config --global user.email "xxx.xxx@email.com"
```

## Git 更改

1. 分支保护:

   master 为主线分支，已开启分支保护，其他分支与 master 分支合并，须经过审核，不能直接在 master 代码上直接进行开发，请先创建你的个人分支进行开发，再发送 `pull request` 合并到 `master`.

2. 分支命名规范:

   个人开发分支命名请遵循使用 `develop-name` or `dev-project` 以便于辨识

3. 更改注意事项

   请预先检查你的分支状态是否为最新, 如不是, 请手动合并主线到你的开发分支

4. 代码合并

   在 web 端创建合并请求 (`Merge Request`), 增加 `Code Review` 成员, 待审核通过后会合并至主线分支

## 常见问题及解决方案

### Git 放弃本地所有更改并清除 Cache

```bash
git checkout . && git clean -xdf
```

### Ubuntu 安装 gitstats

用途：统计 git 仓库贡献率，查看代码提交量

安装： `sudo apt install gitstats`

使用： 在 git 仓库目录下，运行`gitstats repo gen`

查看 repo：`python -m SimpleHTTPServer 6666` 网页打开：`localhost:6666`进行查看

### 设定 Git 存贮使用密码，无需每次输入密码

```bash
git config --global credential.helper store
```

### Git 删除本地无用分支

```bash
git fetch --prune
```

### Git 删除远端分支(请勿使用)

```bash
git push --origin delete branch-name
```

## 使用 Repo 管理多个 Git 仓库

**仅支持 Linux/Mac 平台.**

### 安装 repo

```bash
sudo apt install repo
```

### Skip automatic update

```bash
cd ~/depot_tools/ 
export DEPOT_TOOLS_UPDATE=0
```

### 使用 SW 内部 Repo

```bash
cd ${HOME}
mkdir swpe-repo && cd swpe-repo

# Online Method
# Need normal access google and can be get repo.

export REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo'

repo init -u http://10.18.6.89/swpe/manifest.git


repo sync
```

### 提交变更

1. 使用 `git checkout`

   ```bash
   cd swrepo
   git checkout develop-x
   # make changes
   git add changes
   git commit -m "Add changes"
   git push
   ```

2. 使用 `repo start`

   ```bash
   cd swpe-repo
   repo start newbranch project
   # repo start develop-x swrepo
   cd swrepo
   # make changes
   gti add changes
   git commit -m "subject: add changes"
   git push
   ```

### 同步repo 下仓库更改

```bash
repo sync
```

### Fix repo sync error

```bash
repo sync 提示 `xxx/xxx checkout error`.

解决方案：进入提示目录, 输入`git reset --hard`
```
