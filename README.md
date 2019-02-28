# blog

大约博客

### 安装

-  拉取仓库

```bash
git clone https://github.com/DayueOrg/blog.git
```
- 安装依赖

```bash
npm install
```
### 启动服务器

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

### 其他

-  生成静态文件

```bash
hexo g
```

- 清除缓存文件 (db.json) 和已生成的静态文件 (public)

```bash
hexo clean
```
