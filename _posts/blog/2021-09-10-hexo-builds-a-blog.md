---
title: 使用 Hexo 搭建博客
date: 2021-09-10 00:34:00 +0800
categories: [Blogging]
tags: [Hexo]
---

Hexo 是一个快速、简洁且高效的博客框架。 Hexo 使用 Markdown（或其他标记语言）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
Hexo 官网：<https://hexo.io/zh-cn/>


## 1.安装前提
Hexo 支持多种平台，只需要先安装如下依赖：
- Git
- Node.js
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->

> Node.js 版本需不低于 10.13，建议使用 Node.js 12.0 及以上版本。
{: .prompt-info }
<!-- markdownlint-restore -->

## 2.安装 Git
本文环境为 Windows 操作系统，前往 Git 官网：<https://git-scm.com/> 下载安装包安装即可。安装完成后打开命令行工具，执行`git version`命令，返回 Git 版本信息说明安装成功。

## 3.安装 Node.js
Node.js 官方提供了安装程序，前往 Node.js 官网：<https://nodejs.org/en/> 下载安装即可，安装时注意记得勾选`Add to PATH`选项，安装完成后执行`node --version`，`npm --version`命令验证。

## 4.安装 Hexo
以上依赖环境安装完成后，打开命令行工具使用 npm 安装Hexo。
```shell
$ npm install -g hexo-cli
```
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->

> 如果安装速度过慢，可以先将 npm 的下载源更换为国内的淘宝镜像，然后再进行安装，使用以下命令更换 npm 下载源。 
{: .prompt-info }
<!-- markdownlint-restore -->
 
```shell
$ npm config set registry https://registry.npm.taobao.org
```

安装完成后执行`hexo --version`命令，返回如下内容则安装成功。
```shell
hexo-cli: 4.3.2
os: win32 10.0.19045 undefined
node: 20.15.0
acorn: 8.11.3
ada: 2.7.8
ares: 1.28.1
base64: 0.5.2
brotli: 1.1.0
cjs_module_lexer: 1.2.2
cldr: 45.0
icu: 75.1
llhttp: 8.1.2
modules: 115
napi: 9
nghttp2: 1.61.0
nghttp3: 0.7.0
ngtcp2: 1.1.0
openssl: 3.0.13+quic
simdutf: 5.2.8
tz: 2024a
undici: 6.13.0
unicode: 15.1
uv: 1.46.0
uvwasi: 0.0.21
v8: 11.3.244.8-node.23
zlib: 1.3.0.1-motley-7d77fb7
```

## 5.搭建博客
Hexo 安装完成后，可以使用`hexo init hexo-blog`命令初始化博客。该命令会在当前路径生成一个专门保存博客项目的目录，然后从 Github 上克隆博客项目和默认的主题。
```shell
$ hexo init hexo-blog
INFO  Cloning hexo-starter https://github.com/hexojs/hexo-starter.git
INFO  Install dependencies
INFO  Start blogging with Hexo!
```
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->

> 如果不想把博客文件放存放到当前路径，先切换到指定路径后再执行该命令。
{: .prompt-info }
<!-- markdownlint-restore -->

初始化后，项目文件如下所示：
```text
.
├── _config.yml  # 博客的配置文件
├── package.json  # 博客的依赖项文件
├── scaffolds  # Markdown 文件的模板，也就是向新添加的 Markdown 文件中默认填充的内容
├── source
|   ├── _drafts
|   └── _posts  # Hexo 会将该目录下的 Markdown 文件处理成博客的静态页面，生成的静态页面将置于 public 目录
└── themes  # 博客的主题目录
```

然后使用`npm install`命令安装`package.json`中指明的依赖。
```shell
$ npm install
```
依赖安装完成后，使用`hexo generate`命令生成博客静态文件。
```shell
$ hexo generate
```
最后使用`hexo server`命令启动 Hexo 服务器，默认访问地址为：http://localhost:4000/。
```shell
$ hexo server
```

## 6.更换主题
此时已经完成了博客框架框架的搭建，接下来可以根据自己的喜欢更换一个适合自己的主题，Hexo 主题地址：<https://hexo.io/themes/>。

本文使用 butterfly 主题，前往 [hexo-theme-butterfly](https://github.com/jerryc127/hexo-theme-butterfly) 下载，然后将下载后将主题放到博客项目的`themes`目录下，最后修改配置文件`_config.yml`文件如下：
```yaml
theme: butterfly # 填主题文件夹名称
```

重启服务器后就可以看到主题的效果，如需更改一些功能和配置参考 [butterfly 配置文档](https://butterfly.js.org/categories/Docs%E6%96%87%E6%AA%94/) 修改。

## 7.GitHub Pages
在 GitHub Pages 上部署 Hexo，第一步在 GitHub 上创建名为`<GitHub用户名>.github.io`的仓库，然后将 Hexo 项目中的文件 push 到仓库中。
```shell
$ git add .
$ git commit -m "update"
$ git remote add origin https://github.com/slinjing/slinjing.github.io.git
$ git branch -M main
$ git push -u origin main
```

push 成功后开启 GitHub Pages 并修改部署文件，点击 Settings --> Pages 把`Build and deployment`下的`Ddploy form a branch`修改为`GitHub Actions`。最后创建`.github/workflows/pages.yml`文件，内容如下：
```yaml
name: Pages

on:
  push:
    branches:
      - main # default branch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          # If your repository depends on submodule, please see: https://github.com/actions/checkout
          submodules: recursive
      - name: Use Node.js 20
        uses: actions/setup-node@v4
        with:
          # Examples: 20, 18.19, >=16.20.2, lts/Iron, lts/Hydrogen, *, latest, current, node
          # Ref: https://github.com/actions/setup-node#supported-version-syntax
          node-version: "20"
      - name: Cache NPM dependencies
        uses: actions/cache@v4
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
<!-- markdownlint-capture -->
<!-- markdownlint-disable -->

> 将文件中的 20 修改为自己的 Node.js 版本，并且因为此时仓库中比本地多一个文件，下次 push 之前先 pull 一下，不然 push 会失败。
{: .prompt-info }
<!-- markdownlint-restore -->

后期无论是修改了博客内容还是主题配置，只要 push 到 GitHub 后都会自动进行部署，可以在仓库中点击`Actions`查看部署进度，部署完成后在浏览器输入：`https://<github用户名>.github.io/`即可在公网访问。
