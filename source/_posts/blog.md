title: 记一次 Hexo 博客搭建全过程
excerpt: 只一篇博客搭建教程足以。
photos:
  - 'http://staticfile.lianwiki.cn/blog/github-travis-hexo.jpg'
date: 2019-03-06 17:38:20
tags:
---

[Hexo](https://hexo.io/zh-cn/) 作为 [众多静态博客框架](http://topspeedsnail.com/static-website-generators_or_tools/) 之一，具有快速、简洁且高效的特点，可基于 [Node](https://nodejs.org/cn/) 环境快速搭建博客，并且拥有众多美观的 [主题](https://hexo.io/themes/) 可以选择。

今天，将详细记录一次博客搭建的全过程，主要包括 **博客的初始化**、**主题的更换**、**部署到 [Github Pages](https://pages.github.com/)**、**将源码推送到 Gihub 仓库**、**[Hexo Admin](https://jaredforsyth.com/hexo-admin/) 的集成**、** [TravisCI](https://travis-ci.com) 持续集成**、**博客在线编辑** 等。

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

