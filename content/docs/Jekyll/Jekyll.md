---
title: 快速开始
type: docs
prev: docs/first-page
weight: 1
---

Jekyll 是一款免费的开源静态网站生成器，核心功能是将纯文本内容转换为包含 HTML、CSS 和 JavaScript 的静态文件，最终生成可直接部署的完整静态网站。

官方网站：[https://jekyllrb.com](https://jekyllrb.com)

## 一、安装前提

在安装 Jekyll 前，需确保系统满足以下两个必要条件：


1. 已安装 **Ruby 2.5.0 及以上版本**（Jekyll 依赖 Ruby 的 gems 包管理系统运行）；

2. Ruby 自带的 gem 工具可正常使用（用于后续安装 Jekyll 相关组件）。

## 二、安装 Ruby（Windows 系统）

本文以 Windows 操作系统为例，通过 RubyInstaller 工具安装 Ruby，具体步骤如下：

1. 访问 RubyInstaller 官网下载页面：[R](https://rubyinstaller.org/downloads/)[ubyIn](https://rubyinstaller.org/downloads/)[stall](https://rubyinstaller.org/downloads/)[er](https://rubyinstaller.org/downloads/)，根据系统位数选择对应的 Ruby 安装程序；

2. 运行下载的安装程序，**务必勾选「Add Ruby executables to your PATH」选项**（该操作会将 Ruby 可执行文件路径添加到系统环境变量，方便后续在命令行中调用 Ruby）；

3. 安装程序完成基础安装后，会弹出命令行窗口，在窗口中选择「MSYS2 and MINGW development tool chain」（该组件为后续依赖安装提供必要支持）。

![MSYS2 组件选择界面](/images/msys2-installation-choice.png)

### 验证 Ruby 安装结果

打开命令行窗口，输入以下两条命令，若能正常显示版本号，则说明 Ruby 及 gem 工具安装成功：



```
# 查看 Ruby 版本
ruby -v&#x20;

# 查看 gem 工具版本
gem -v
```

## 三、安装 Jekyll

完成 Ruby 安装后，通过 gem 工具安装 Jekyll 及 Bundler（Jekyll 项目依赖管理工具），具体步骤如下：

### 1. 更换 gem 下载源（解决默认源下载慢问题）

默认的 gem 下载源（[https://rubygems.org/](https://rubygems.org/)）访问速度较慢，建议先更换为国内源，操作命令如下：



```
# 1. 查看当前 gem 下载源
gem source

# 2. 删除默认的国外下载源
gem source -r https://rubygems.org/

# 3. 添加国内下载源（Ruby China 源）
gem source --add https://gems.ruby-china.com

# 4. 再次查看源列表，验证是否更换成功
gem source
```

### 2. 安装 Jekyll 与 Bundler

在新打开的命令行窗口中，输入以下命令完成安装：
```
gem install jekyll bundler
```

### 3. 验证 Jekyll 安装结果

输入以下命令，若能正常显示 Jekyll 版本号，则说明安装成功：

```
jekyll -v
```

## 四、快速创建 Jekyll 网站

Jekyll 安装完成后，可通过几条命令快速创建并运行一个基础的 Jekyll 网站，步骤如下：

```
# 1. 创建名为 "my_blog" 的 Jekyll 项目（项目名可自定义）
jekyll new my_blog

# 2. 进入项目根目录
cd my_blog

# 3. 启动本地开发服务器（默认端口为 4000）
bundle exec jekyll serve
```

### 访问本地网站

服务器启动成功后，打开浏览器，在地址栏输入 `http://127.0.0.1:4000`，即可查看当前 Jekyll 网站的预览效果。

## 五、Jekyll 目录结构

一个基础的 Jekyll 项目，其目录结构如下（核心文件 / 目录已标注）：

```
.
├── _config.yml          # 项目核心配置文件（全局设置）
├── _drafts              # 存放未发布的草稿文章（可选目录）
|   ├── begin-with-the-crazy-ideas.textile
|   └── on-simplicity-in-technology.markdown
├── _includes            # 存放可复用的页面片段（如头部、底部组件）
|   ├── footer.html
|   └── header.html
├── _layouts             # 存放页面布局模板（如文章页、首页模板）
|   ├── default.html     # 默认布局模板
|   └── post.html        # 文章页布局模板
├── _posts               # 存放已发布的文章（核心目录）
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
|   └── 2009-04-26-barcamp-boston-4-roundup.textile
├── _site                # Jekyll 生成的静态网站文件（部署时只需上传该目录）
├── .jekyll-metadata     # Jekyll 自动生成的元数据文件（无需手动修改）
├── assets               # 存放静态资源（如图片、CSS、JS，需手动创建）
|   └── images           # 文章中引用的图片文件（需手动创建）
└── index.html           # 网站首页（访问网站时的默认入口页面）
```

### 核心文件 / 目录说明



| 文件 / 目录         | 描述                                                        |
| --------------- | --------------------------------------------------------- |
| `_config.yml`   | 项目全局配置文件，可设置网站标题、作者、域名、插件等参数                              |
| `_posts`        | 文章存放目录，文件命名必须遵循 `YYYY-MM-DD-文章标题.文件后缀` 格式（如 .md、.textile） |
| `_site`         | Jekyll 编译后生成的静态网站文件目录，部署时直接上传该目录即可                        |
| `assets/images` | 静态图片资源目录，建议将文章中引用的图片统一存放在此（需手动创建目录）                       |
| `index.html`    | 网站首页文件，可通过该文件定义首页的布局和内容                                   |

更多详细的目录结构说明，可参考 Jekyll 官方文档：[Directory Structure↗](https://jekyllcn.com/docs/structure/)
