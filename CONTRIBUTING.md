# CONTRIBUTING GUIDE

## 文档共享中心提交及更改指南

维护一个共同的成长环境，而非闭门造车。

文档共享平台，使用 Markdown.

网页链接: <http://10.18.6.89:2020>, 欢迎贡献！

~~Git 存储库位置 <http://10.18.6.58/helin.liu/docs.git>~~

## 提交步骤

### 1. 克隆文档存储库

```bash
git clone http://10.18.6.89/swpe/gitbook.git
```

### 2. 创建你的个人分支

```bash
git checkout -b dev-changes
```

### 3. 文档结构说明

* 其中笔记文档均采用`Markdown`编写
* 所有笔记存储在`src`目录下，包含多级目录
* 目录页生成均由`SUMMARY.md`控制
* 其余内容为服务配置文档，请勿更改

### 4. 本地更改

Example: 修改`/src/example.md` 文件，在`SUMMARY.md` 增加相关目录

### 5. 保存更改并提交您的更改

```bash
git add .
git commit -m "docs: update some notes"
git push -u
```

### 6. 在 Web 端创建合并请求

### 7. 等待合并完成，网页端会同步刷新
