title: 记一次 Hexo 博客搭建全过程
excerpt: 只一篇博客搭建教程足以。
photos:
  - 'http://staticfile.lianwiki.cn/blog/github-travis-hexo.jpg'
date: 2019-03-06 17:38:20
tags:
---

[Hexo](https://hexo.io/zh-cn/) 作为 [众多静态博客框架](http://topspeedsnail.com/static-website-generators_or_tools/) 之一，具有快速、简洁且高效的特点，可基于 [Node](https://nodejs.org/cn/) 环境快速搭建博客，并且拥有众多美观的 [主题](https://hexo.io/themes/) 可以选择。

今天，将详细记录一次博客搭建的全过程，主要包括 **博客的初始化**、**主题的更换**、**部署到 [Github Pages](https://pages.github.com/)**、**将源码推送到 Gihub 仓库**、**[Hexo Admin](https://jaredforsyth.com/hexo-admin/) 的集成**、**使用 [Travis CI](https://travis-ci.com) 自动部署**、**博客在线编辑** 等。

> 环境说明： 博客搭建的全过程都是基于 [Node](https://nodejs.org/cn/) 和 [Git](https://git-scm.com/) 环境。

## 初始化博客

首先，全局安装 Hexo 脚手架。

```bash
npm install hexo-cli -g
```

然后，初始化博客，并进入到博客目录。

```bash
hexo init blog
cd blog
```
紧接着，安装项目依赖。
```bash
npm install
```
最后， 启动服务，并访问端口 http://localhost:4000/ ， 看到如下界面, 说明博客就搭建成功了。
```bash
hexo server
```
![4000 端口预览](http://staticfile.lianwiki.cn/blog/4000-preview.png)

## 更换主题

在正式开始之前，需要在 [Hexo 主题列表](https://hexo.io/themes/) 中选取一个喜欢的主题（该博客为 [Clover](https://clovertuan.github.io/) 主题）。

**安装主题**

首先，进入到所选主题的 [github 项目](https://github.com/esappear/hexo-theme-clover) 页面， 并拷贝主题 github 路径（该博客为: `https://github.com/esappear/hexo-theme-clover` ）。

然后，再进入到博客根目录，并执行如下命令将主题代码拷贝到 themes/clover 目录下。

```bash
git clone https://github.com/esappear/hexo-theme-clover themes/clover
```
> 其中， themes/clover 为主题要存放的目录。

**修改配置**

修改博客根目录下的 `_config.yml` 文件。

```bash
theme: clover
```
> 需要注意的是 **theme 主题的名称** 需要与 **主题存放目录的名称** 一致，否则 Hexo 是无法找到相应的主题。

**安装依赖**

每个主题需要安装的依赖可能有所不同，具体需要查看相应主题的 [说明文件](https://github.com/esappear/hexo-theme-clover)。

**下面以 Clover 为例，需要添加如下依赖：**

hexo-renderer-sass：

```bash
npm install hexo-renderer-sass --save
```
最后，执行下方的命令清除静态文件，并重启服务，访问端口 http://localhost:4000/ ， 看到如下界面就说明主题修改成功。

```
hexo clean & hexo s
```
![clover 主题](http://staticfile.lianwiki.cn/blog/theme-clover.png)

## 部署到 Github Pages

[Github Pages](https://pages.github.com/) 作为一个静态页面服务器，可以用来展示 Github 项目中的静态页面。所以 Hexo 生成的静态页面就可以直接放到 Github 中进行展示。

通过 [Github Pages 的官方文档](https://help.github.com/en/articles/configuring-a-publishing-source-for-github-pages) ，可以看到一共有三种方式来发布我们的博客，它们分别是 master 分支、gh-pages 分支 或 master分支下的 /docs 目录。

这里我们将采用 gh-pages 分支的方式，将 gh-pages 分支用来存放博客发布后的静态页面，而 master 分支将作为主分支存放博客源码，这样就可以实现发布和创作分离，互不干扰，还可以方便的将博客源码 clone 到不同的电脑上进行博客创作。

**创建 Github 仓库**

首先，访问 [Github](https://github.com/)，点击右上角的个人头像，登入 Github。

然后再点击右上角的加号，并选中 `New repository` ，保持默认选项，点击确认，创建一个全新的代码仓库。

![全新的仓库](http://staticfile.lianwiki.cn/blog/new-repository.png)

**修改配置**

修改博客配置文件 ` _config.yml`：

```bash
deploy:
  type: git # 部署类型
  repository: https://github.com/songxingguo/blog.git # 仓库地址
  branch: gh-pages # 目标分支
```
具体配置可以参看 [Hexo 部署文档](https://hexo.io/zh-cn/docs/deployment) 。

**安装依赖**

安装 hexo-deployer-git。

```bash
npm install hexo-deployer-git --save
```
**部署博客**

清除静态文件并部署博客。

```bash
hexo clean && hexo deploy
```
进入 Github， 点击仓库右上角的 Settings，找到 GitHub Pages ， 并访问博客地址。

![GitHub Pages](http://staticfile.lianwiki.cn/blog/GitHub-Pages.png)

**博客样式加载不出来**

要解决这个问题，首先，需要明白 Github Pages 拥有两种静态页面，一种是项目页面， 网站发布后的地址为： `http(s)://<username>.github.io/<projectname>`, 另外一种是用户页面， 以 `<username>.github.io` 进行命名，并且网站发布后的地址为： `http(s)://<username>.github.io` 。

样式之所以加载不出来，就是这里并没有使用 `<username>.github.io` 进行命名，网站的所有资源（包括样式、图片等）都会被发布在 `http(s)://<username>.github.io` 的子路径 `blog` 下，而 Hexo 会默认从 `http(s)://<username>.github.io` 加载资源，所以样式加载就会出现 `404` 错误。

因此，只需要修改一下 Hexo 配置文件，让 Hexo 从  `blog` 子目录加载样式就好了。

博客根目录下的 `_config.yml` 文件修改如下：

```bash
url: https://dayueorg.github.io # 网址
root: /blog # 网站根目录，默认值 :year/:month/:day/:title/
permalink: :year/:month/:day/:title/ #文章的 永久链接 格式
permalink_defaults: # 久链接中各部分的默认值	
```
> 只需将 **网站根目录** 修改为 **博客名称** 即可。

具体配置可以参看 [网址配置](https://hexo.io/zh-cn/docs/configuration#%E7%BD%91%E5%9D%80) 。

最后，重新执行上面的 **部署博客**，样式就可以显示了。

## 推送源码到 Github 仓库

上面我们已经将博客发布后的静态文件部署到了 Github Pages 上，也可以在线看到我们的博客了，但是  Github Pages 上的代码毕竟是发布后的静态文件，也就是说我们是无法通过它编辑我们的博客。

然而，如果有一天编辑博客的电脑出问题了或者想使用另外一台电脑编辑我们的博客，那么就这都是需要博客源码。

所以最稳妥的方法就是将博客源码也推送到 Github 仓库，我们需要对博客进行创作的时候就可以直接从 Github 仓库拷贝一份下来创作就好了。

**删除主题中的 .git 文件**

由于我们的主题是从代码仓库 clone 下来的，在主题放置目录下（该主题为 `themes/clover` ）会有 .git 文件 ，这样就会导致使用 Git 提交代码时会 **忽略** 主题放置目录下的所有文件，所以我们需要先把 .git 文件从 **主题放置目录** 中 **删除** 。

**推送源码**

```bash
git init #初始化代码仓库
git remote add origin https://github.com/songxingguo/blog.git # 关联远程仓库
git status 
git add -A
git commit -m "source code"
git push -u origin master # 推送并创建 master 分支
```
**设置默认分支**

进入 Github 仓库， 点击代码上部的 branches， 然后将默认分支设置为 master ， 这样博客源代码就推送到了 Github 仓库。

## 集成 Hexo Admin

Hexo Admin 作为一款 Hexo Web 编辑器，可以方便的进行博客的编辑并发布，并且还可以集成到服务器中进行在线编辑与发布。当然，除了 Hexo Admin 之外，还有其他一些优先的 Hexo 编辑器，比如 [hexo-editor](https://github.com/tajpure/hexo-editor)、[HexoEditor](https://github.com/zhuzhuyule/HexoEditor/) 等。

那下面就说说如何集成那么一款好用的 Hexo 编辑器到自己的博客中。

**安装 Hexo Admin**

首先，进入到博客根目录，执行下面的安装命令。

```bash
npm install --save hexo-admin
```
然后， 重启服务，并访问地址 http://localhost:4000/admin/ ，就可以看到 Hexo Admmin 编辑器的真身了。

```bash
hexo server -d
```
![Hexo Admin](http://staticfile.lianwiki.cn/blog/hexo-admin.png)

**配置用户名和密码**

作为一款可以在线编辑的 Hexo 编辑器，登录验证自然是少不了的了，所以我们需要配置一些登录相关的用户名和密码。

首先，修改博客配置文件 ` _config.yml`。

```bash
admin:
  username: myfavoritename
  password_hash: be121740bf988b2225a313fa1f107ca1
  secret: a secret something
```
username 不用多说了，就是用户名。password_hash 为一串 bcrypt  哈希值，是通过 [bcrypt](https://www.browserling.com/tools/bcrypt) 加密工具得到的。secret 是用于保证 cookies 安全，所以 secret 越长越复杂越好。

然后， 重启服务，再次访问  http://localhost:4000/admin/ ,发现需要登录了。

```bash
hexo server -d
```

![Hexo Admin 登录](http://staticfile.lianwiki.cn/blog/hexo-admin-login.png)

**配置发布**

> 在开始之前，首先声明该配置是针对 Linux 而言是可行的，对于 Windows 会出现 [`Error: spawn UNKNOWN`](https://github.com/jaredly/hexo-admin/issues/94) 错误。

Hexo 的发布

首先，修改博客配置文件  _config.yml。

```bash
admin:
  deployCommand: './hexo-deploy.sh'
```
然后，在博客根目录添加文件 hexo-deploy.sh，hexo-deploy.sh 内容如下：

```bash
hexo clean
hexo g -d
```
最后，修改权限。

> Linux/Unix 的文件调用权限分为三级 : 文件拥有者、群组、其他。利用 chmod 可以藉以控制文件如何被他人所调用。

```bash
chmod +x hexo-deploy.sh
```
发布成功后出现如下界面：

![Hexo Admin 发布](http://staticfile.lianwiki.cn/blog/hexo-admin-deploy.png)

## 使用 Travis CI 自动部署

经过上面一些列的步骤之后，我们已经搭建了一个比较完善的博客了，可以部署到 Github Pages 、也可以可视化编辑了。

但是唯一的不足就是每次写好博客之后，我都要进行两项操作，第一、部署博客到 Github Pages； 第二、提交源码的 Github 仓库。那有没有什么操作可以让两者合二为一的呢？

当然是有的，也就是下面要介绍的 Travis CI，有了它就只需要把源代码提交代码仓库就行了，部署发布 Travis 就会自动完成，是不很棒(๑•̀ㅂ•́)و✧啦。 

如果对 Travis CI 还不太了解，可以先看一下阮一峰的 [持续集成服务 Travis CI 教程](http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html) 。

如果已经有了一定了解，那我们就可以进入正题了。

**选择仓库**

首先，访问官方网站 [travis-ci.com](https://travis-ci.com)，点击右上角的个人头像，使用 Github 账户登入 Travis CI。

> Travis 会列出 Github 上面你已经激活的项目，以及你已经被授权的组织（对于没有授权的组织可以点击 `MISSING AN ORGANIZATION?` 下面的 [Review and add](https://github.com/settings/connections/applications/88c5b97de2dbfc50f3ac) 进行授权  ）。

然后，点击 [Manage repositories on GitHub](https://github.com/settings/installations/181965) 选择你需要 Travis 帮你构建的仓库。一旦激活了一个仓库，Travis 会监听这个仓库的所有变化。
 
![Manage repositories](http://staticfile.lianwiki.cn/blog/manage-repositories.png)
 
**创建 .travis.yml**

> Travis 要求项目的根目录下面，必须有一个.travis.yml文件。这是配置文件，指定了 Travis 的行为。该文件必须保存在 Github 仓库里面，一旦代码仓库有新的 Commit，Travis 就会去找这个文件，执行里面的命令。

首先，我们需要在项目根目录新建 .travis.yml 文件。

然后，采用 Travis 的快捷部署功能, 将博客静态文件部署到 [Github Pages](https://docs.travis-ci.com/user/deployment/pages/) ，完整配置文件如下：

```bash
language: node_js
node_js:
  - 8.12.0

install:
  - npm install

script:
  - hexo clean && hexo g

deploy:
  provider: pages
  skip_cleanup: true
  local-dir: public # 发布目录|静态文件目录
  github_token: $GITHUB_TOKEN # Set in travis-ci.org dashboard
  on:
    branch: master # 源分支
```
> **GITHUB_TOKEN** 为 Github 的 **Personal Access Token** ，配置在每个仓库的设置页里面，具体配置步骤下面将逐一介绍。

Travis 为几十种常见服务提供的快捷部署功能， 其他部署方式，请看 [官方文档](https://docs.travis-ci.com/user/deployment/)。

**创建 Personal Access Token**

进入 Github， 点击头像并选择 settings ，然后再点击 `Developer settings` ，最后，进入到 [Personal access tokens](https://github.com/settings/tokens) 。

![Personal Access Token](http://staticfile.lianwiki.cn/blog/Personal-Access-Token.png)

如上图所示，选择勾选第一个选项，点击保存，并复制 **Personal Access Token** 值。

具体创建步骤，请看 [Personal Access Token](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line) 。

**设置 Personal Access Token**

> 有些环境变量（比如用户名和密码）不能公开，这时可以通过 Travis 网站，写在每个仓库的设置页里面，Travis 会自动把它们加入环境变量。这样一来，脚本内部依然可以使用这些环境变量，但是只有管理员才能看到变量的值。具体操作请看 [官方文档](https://docs.travis-ci.com/user/environment-variables)。

紧接着上面的步骤，打开 [Travis 官网](https://travis-ci.com)，进入到 **仓库设置页面** ， 将上面复制的 **Personal Access Token**，添加到环境变量中，如下图所示。

![设置 Github Token](http://staticfile.lianwiki.cn/blog/Github-Token.png)

**提交 .travis.yml**

最后， 将 .travis.yml 提交到 Github 仓库， Travis 就会开始 **自动部署** 了。
