# blog

大约博客

## 环境

node: 10.14.2

## 使用手册

### 安装

-  拉取仓库

```bash
git clone https://github.com/DayueOrg/blog.git
```
- 进入项目目录

```bash
cd blog
```

- 安装依赖

```bash
npm install
```
- 安装 hexo

Windows

```bash
npm install -g hexo
```
Mac 和 Linux
```bash
sudo npm install -g hexo
```

### 启动服务器

进入项目目录,执行如下命令：

```bash
hexo s
```
- 访问本地服务

​       http://localhost:4000

- 可视化编辑博客

​       http://localhost:4000/blog/admin/

### 部署博客

- 手动部署

```bash
hexo g & hexo d
```

   - 自动部署

     执行 `git push`  会触发 travis-ci 进行自动部署。

## hexo 常用命令

-  生成静态文件

```bash
hexo g
```

- 清除缓存文件 (db.json) 和已生成的静态文件 (public)

```bash
hexo clean
```

## 问题

### node 版本不匹配


- 升级 node

Mac

```bash
sudo npm cache clean -f
sudo npm install -g n
npm view node versions
sudo n 10.14.2
```
