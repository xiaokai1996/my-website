# 简单用MkDocs搞个网站

## step 1. ⚙️ 配置MkDocs环境
```bash
# 1. 创建虚拟环境
python3 -m venv venv

# 2. 激活虚拟环境
source venv/bin/activate   # macOS/Linux
# venv\Scripts\activate    # Windows

# 3. 安装 MkDocs + Material + 扩展
pip install mkdocs mkdocs-material pymdown-extensions

# 4. 运行
mkdocs serve
```
## step 2. 📝 创建项目结构和配置
```bash
# 1. 初始化项目，自动生成一个空的目录结构+mkdocs.yml配置文件
mkdocs new mysite
# mysite/
# ├── docs/
# │   └── index.md
# └── mkdocs.yml

# 2. 预览空网站
cd mysite
mkdocs serve

# 3. 配置md和导航栏
```

背景知识：

- index.md 是网站首页。
- mkdocs.yml 控制网站标题、导航栏、主题、插件等。

```yaml
site_name: 刘凯的个人网站
theme:
  name: material
  features:
    - navigation.tabs
    - navigation.top
    - search.highlight
markdown_extensions:
  - nl2br
nav:
  - 首页: index.md
  - 关于: about.md
  - 博客:
    - 2026 年 AI 泡沫: blogs/ai-bubble-2026.md
  - 笔记:
    - 如何用 MkDocs 构建网站: notes/how-to-build-websites-with-mkdocs.md
```

## step 3. 💻 修改md预览网站
```bash
mkdocs serve	# 本机8000端口预览
mkdocs build	# 生成静态网页，这一步其实没啥用！mkdocs gh-deploy会自动包含这一步
```
## step 4. 🖇️ 部署到GitHub Pages
```bash
# 1. 先把本地的git目录提交到远端
git init
git add .
git commit -m "初始化 MkDocs 网站"
git branch -M main
# git@github.com:xiaokai1996/my-website.git
git remote add origin https://github.com/xiaokai1996/my-website.git
git push -u origin main

# 2. 安装mkdocs github部署插件
pip install mkdocs-git-revision-date-localized-plugin

# 3. 然后把md渲染成静态文件自动触发github的流水线
mkdocs gh-deploy
```

为啥mkdocs gh-deploy可以一键部署呢？因为他会自动帮你做3件事情：

1. 把md渲染成静态文件，放到site文件夹下
2. 创建一个gh-pages分支
3. 把gh-pages分支推送到远程仓库

## 常见的工作流

1. 随手写一个md文件，改一下配置：`mkdocs.yml`
2. 信心满满在本机 [预览](http://127.0.0.1:8000/)：`mkdocs server`
3. 好像差点意思，先存个档明天再搞：`git add . && git commit -m "update" && git push`
4. 非常棒了，一键发布 ：`mkdocs gh-deploy`
5. 欢迎访问 [我的网站](https://xiaokai1996.github.io/my-website/)

## FAQ

### 编译的静态文件是否要提交？

不需要提交，如果不小心提交了，可以使用下面这种方法删除，然后新建一个.gitignore文件把/site文件夹加进去
```bash
git rm -r --cached site
git commit -m "删除 site"
git push
```
### 如何新增目录？
每个md都会生成一个网页
nav决定了网站的目录结构，在mkdocs.yml中新增对应的目录架构就行
```sql
docs/
├── guide/
│   ├── install.md
│   └── usage.md
├── tutorials/
│   ├── tutorial1.md
│   └── tutorial2.md
nav:
  - 首页: index.md
  - 指南:
      - 安装: guide/install.md
      - 使用: guide/usage.md
  - 教程:
      - 教程1: tutorials/tutorial1.md
      - 教程2: tutorials/tutorial2.md
```

### 🙋如何把实现单个换行符换行？
github中默认是双换行符=真正的换行，单个换行符=字符串连接。
但可以通过插件实现
``` yaml
site_name: 我的MkDocs网站
theme:
  name: material
markdown_extensions:
  - nl2br  # 让单回车换行生效
nav:
  - 首页: index.md
```

### 如何上传图片并设置格式、标题字体美化？
使用这种相对写法，可以直接显示图片，开启pymdownx的扩展插件，就可以实现
```md
![hello](../assets/images/image-0001.png){ width=200px }
![hello](../assets/images/image-0001.png){ width=50% }
![hello](../assets/images/image-0001.png){ .my-img }
![hello](../assets/images/image-0001.png){ .center-image width=200px }
```

还可以在doc目录下配置`docs/stylesheets/extra.css`，配置一些常用的格式，注意格式可以叠加！
```css
.my-img {
  width: 200px;
  height: auto;
}
.center-image {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
```

最终配置如下，重点是`pymdownx.extra`和`stylesheets/extra.css`
``` yaml
site_name: 刘凯的个人网站
theme:
  name: material
  features:
    - navigation.tabs
    - navigation.top
    - search.highlight
markdown_extensions:
  - nl2br                # 支持单换行符转换为真正的换行符
  - admonition          # 支持提示框（注意、警告等）
  - codehilite          # 代码高亮
  - toc                 # 生成目录
  - pymdownx.arithmatex # 数学公式
  - pymdownx.betterem   # 改善斜体/加粗语法
  - pymdownx.caret      # 支持上标 ^text^
  - pymdownx.emoji      # 支持 :smile: 表情
  - pymdownx.inlinehilite
  - pymdownx.magiclink  # 自动识别 URL 并生成链接
  - pymdownx.mark       # 高亮文字
  - pymdownx.smartsymbols
  - pymdownx.tasklist   # 支持任务列表
  - pymdownx.tilde      # 支持下标 ~text~
  - pymdownx.extra       # 额外的 Markdown 扩展
extra_css:
  - stylesheets/extra.css
nav:
  - 首页: index.md
  - 关于: about.md
  - 博客:
    - 2026 年 AI 泡沫: blogs/ai-bubble-2026.md
  - 笔记:
    - 如何用 MkDocs 构建网站: notes/how-to-build-websites-with-mkdocs.md
```

其中`stylesheets/extra.css`主要是用来配置标题字体、图片格式等。

```css
/* 全局标题加粗（推荐） */
.md-typeset h1 {
  font-weight: 700;
}

.md-typeset h2 {
  font-weight: 600;
}

.md-typeset h3 {
  font-weight: 600;
}

.md-typeset h4,
.md-typeset h5,
.md-typeset h6 {
  font-weight: 500;
}

/* 中文友好字体 */
.md-typeset {
  font-family:
    -apple-system,
    BlinkMacSystemFont,
    "PingFang SC",
    "Hiragino Sans GB",
    "Microsoft YaHei",
    "Noto Sans CJK SC",
    "Source Han Sans SC",
    sans-serif;
}

.my-img {
  width: 200px;
  height: auto;
}
.center-image {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
```