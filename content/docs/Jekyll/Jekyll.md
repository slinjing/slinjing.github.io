---
title: Jekyll
type: docs
prev: docs/first-page
weight: 1
---

Jekyll 是一个免费的开源静态网站生成器，主要用于将纯文本转换为静态的 HTML、CSS 和 JavaScript 文件，从而生成完整的静态网站。Jekyll 官网：[https://jekyllrb.com](https://jekyllrb.com)。

## 安装前提
- 必须安装 Ruby 2.5.0 及以上版本，Jekyll 依赖 Ruby 的 gems 包管理系统。
- 需确保 Ruby 的 gem 工具可正常使用。

## 2.安装 Ruby
本文环境为 Windows 操作系统，通过 RubyInstaller 进行安装 Ruby，点击 [RubyInstaller](https://rubyinstaller.org/downloads/) 下载对应的 Ruby 安装程序，然后运行安装程序，在安装过程中勾选 `Add Ruby executables to your PATH` 以便于在命令行中使用 Ruby。最后在弹出的命令行窗口中选择 `MSYS2 and MINGW development tool chain`。
![](/images/msys2-installation-choice.png)

检查是否安装成功：
```shell
ruby -v 
gem -v
```

## 3.安装 Jekyll
打开新的命令行，使用 gem 安装 Jekyll 和 Bundler。
```shell
gem install jekyll bundler
```
由于默认的下载源非常慢，可以使用以下命令更换下载源，使用 `gem source` 命令查看默认下载源后，删除默认的下载源。
```shell
gem source
gem source -r https://rubygems.org/
```
添加新的下载源后，使用 `gem source` 命令验证。
```shell
gem source --add https://gems.ruby-china.com
gem source
```

检查是否安装成功：
```shell
jekyll -v
```


## 4.快速创建
Jekyll 安装好后，使用以下命令可以快速创建一个新的 Jekyll 网站。
```shell
jekyll new my_blog
cd my_blog
bundle exec jekyll serve
```
在浏览器访问 <http://127.0.0.1:4000/>。

## 5.Jekyll 目录结构
一个基本的 Jekyll 网站的目录结构如下：
```shell
.
├── _config.yml
├── _drafts
|   ├── begin-with-the-crazy-ideas.textile
|   └── on-simplicity-in-technology.markdown
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   └── post.html
├── _posts
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
|   └── 2009-04-26-barcamp-boston-4-roundup.textile
├── _site
├── .jekyll-metadata
└── index.html
```

| 文件/目录                    |  描述 |
| :--------------------------- |  :------ |
| _config.yml         |  配置文件 |
| _posts               |  文章内容，文件命名格式为 `YYYY-MM-DD-TITLE.EXTENSION` |
| _site | Jekyll 生成的网站文件 |
| assets/images | 文章中的图片文件 |
| index.html | 网站主页 |

详见官方文档 [Directory Structure](https://jekyllcn.com/docs/structure/)。

## 6.主题
Jekyll 提供了网站页面的布局和样式，详见官方文档 [Themes](https://jekyllrb.com/docs/themes/)。若要安装一套主题，需要先在 Gemfile 文件中添加如下内容：
```yaml
gem 'jekyll-theme-chirpy'
```

在命令行执行`bundle install`命令安装主题，最后在`_config.yml`文件中启用主题。
```yaml
theme: jekyll-theme-chirpy
```