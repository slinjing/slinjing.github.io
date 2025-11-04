---
title: Chirpy
type: docs
prev: docs/Jekyll/
---

Chirpy 是适用于技术写作的简约、响应迅速且功能丰富的 Jekyll 主题，文档地址：<https://chirpy.cotes.page/> ，Github 地址：[jekyll-theme-chirpy
](https://github.com/cotes2020/jekyll-theme-chirpy)。

## 1.开始
- 打开 [chirpy-starter](https://github.com/cotes2020/chirpy-starter) 仓库，点击按钮 `Use this template` --> `Create a new repository`。

- 将新仓库命名为 `<username>.github.io`，其中 `<username>` 是你的 GitHub 用户名，如果包含大写字母需要转换为小写。

## 2.安装依赖
使用 `git clone` 将新创建的仓库克隆到本地，并在项目根目录下执行 `bundle` 命令安装依赖。如果速度过慢可以使用以下命令，移除默认源并添加新的镜像源。
```shell
bundle config mirror.https://rubygems.org https://gems.ruby-china.com
```

## 3. 配置
根据需要更新 `_config.yml` 中的变量，例如 `url`、`avatar`、`timezone`、`lang` 等。

## 4.本地启动
如果要在本地预览网站内容，执行以下命令：
```shell
bundle exec jekyll serve
```
在浏览器访问 <http://127.0.0.1:4000/>。

## 5.部署
GitHub Pages 是一个通过 GitHub 托管和发布网页的服务，官方文档：<https://docs.github.com/en/pages>。在部署之前，需要将 _config.yml 文件中的 url 配置为`https://<username>.github.io`。

之后在 GitHub 上打开仓库设置，点击左侧导航栏 `Pages`，在 `Build and deployment` - `Source` 下拉列表选择 `GitHub Actions`。
![](/img/Snipaste_2024-12-05_14-27-29.png)

提交本地修改并推送至远程仓库，将会触发 Actions 工作流。在仓库的 Actions 标签页将会看到 Build and Deploy 工作流正在运行。构建成功后，即可通过配置的 URL 访问自己的博客网站。

## 6.评论系统
Jekyll 生成的博客网站是静态的，没有后端和数据库，因此本身无法实现评论功能。然而，可以使用 [disqus](https://disqus.com/)、[utterances](https://utteranc.es/) 和 [giscus](https://giscus.app/zh-CN) 等评论系统来实现评论功能。

本文使用 giscus，它是利用 [GitHub Discussions](https://docs.github.com/en/discussions) 实现的评论系统，并且是开源、免费的。开启评论系统的步骤如下。

(1) 安装 giscus app，访问 <https://github.com/apps/giscus> 点击右侧的 install 按钮进行安装即可。

(2) 在仓库设置页面 Features 一节中勾选 Discussions，开启仓库的 GitHub Discussions 功能。
![](/img/enable-github-discussions.png)

(3) 在仓库的 Discussions 标签页，点击 Categories 旁边的编辑按钮，自定义用于博客评论的类别名称（例如 Comments）。

(4) 打开 <https://giscus.app/zh-CN>，按以下配置：
- 仓库：`<username>/<username>.github.io`
- 页面 <--> discussion 映射关系：勾选 `Discussion 的标题包含页面的 pathname`
- Discussion 分类：`选择上一步创建的类别名称 Comments`

![](/img/b18fdaf70bc3c840a4085cf4f5e35227.png)

之后找到启用 giscus，将生成的配置填写到 _config.yml 文件中 comments.giscus 的对应选项。

```yaml
comments:
  active: giscus
  giscus:
    repo: ZZy979/zzy979.github.io
    repo_id: R_kgDOKOkhRA
    category: Comments
    category_id: DIC_kwDOKOkhRM4CZCpN
    mapping: pathname
```

重启 Jekyll 服务器，在文章底部将会看到评论区，使用 GitHub 账号登录即可发表评论。
