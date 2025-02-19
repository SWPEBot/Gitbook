# 配置 Gitbook 文档服务器

[参考链接](https://www.jianshu.com/p/ec1e7d2c76c6)      注: Ubuntu Linux

1. 安装 Package（首先需要安装 node.js）

    ```bash
    sudo npm install gitbook-cli -g
    ```

2. 配置 Gitbook

    ```bash
    cd ~/gitbook_path
    mkdir demo
    cd demo
    # 初始化后，你将看到两个文件README.md，SUMMARY.md
    gitbook init
    # 生成静态站点，当前目录将生成一个_book目录，即一个Web静态站点
    gitbook build ./
    # 启动网站，默认浏览地址: http://localhost:6666
    gitbook serve --port 6666
    #Clone book源
    git clone http://10.18.6.89/swpe/gitbook.git
    #安装book 内容
    gitbook install
    ```

## 插件维护备注

> 插件排序方式必选先加后减

* "-sharing": 移除分享按钮
* "-lunr": 与 search-pro 插件冲突，需禁用
* "-search": 与 search-pro 插件冲突，需禁用
* "-highlight": 与 prism 插件冲突，需禁用
* "theme-comscore": Gitbook comscore 主题
* "search-pro": 搜索增强, 实现中文搜索
* "splitter": 使左侧边栏的宽度可以自由调节
* "editlink": 添加编辑本页功能
* "prism": 代码语法高亮
* "back-to-top-button": 添加返回顶部按钮
* "code": 显示代码行号和复制按钮
* "chapter-fold": 导航目录折叠
* "expandable-chapters": 可扩展导航章节
* "advanced-emoji": 支持 emoji 表情
* "page-toc-button": 悬浮目录
* "git-author": 显示 git 作者及修订者信息
* "pageview-count": 阅读量计数
  ~~\* "auto-scroll-table": 表格滚动条~~
  ~~\* "signature": 页底显示版权信息~~
* "hide-published-with": 隐藏 Powerd by Gitbook 字符
* "page-footer-ex": 显示版权信息
