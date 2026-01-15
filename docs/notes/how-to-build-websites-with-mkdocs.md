# 简单用MkDocs搞个网站


## Step 1 ⚙️ 配置MkDocs环境

最好是创建一个虚拟的环境，确保环境纯净，安装mkdocs依赖

确保python3和mkdocs在同一个环境中

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

## Step 2 📝 创建项目结构和配置

这一步是内容制作，真正的内容是docs目录下的所有md文件

再加上一个配置文件，把md渲染成静态网页就完成了网站的雏形

```bash
# 1. 初始化项目，自动生成一个空的目录结构+mkdocs.yml配置文件
mkdocs new mysite
# mysite/
# ├── docs/
# │   └── index.md
# └── mkdocs.yml

# 2. 预览空网站
cd mysite
mkdocs serve  # 需要在根目录启动

# 3. 编辑md，在mkdocs.yml中编辑导航栏样式
```

背景知识：
- `index.md` 是网站首页。
- `mkdocs.yml` 是整个网站的配置中心，控制网站标题、导航栏、主题、插件等。

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

## Step 3 💻 本地预览网页

准备好md文件和网页配置文件之后，就可以使用mkdocs命令在本地启动网页了

```bash
mkdocs serve	# 启动一个服务，在本地8000端口可以看到渲染出来的网页
mkdocs build	# 生成静态网页，不需要手动执行，因为mkdocs gh-deploy会自动包含这一步
```

## Step 4 🖇️ 部署到GitHub Pages

这一步是能从外网访问我们网站的关键，使用mkdoc github部署插件部署


```bash
# 1. 先把本地的git目录提交到github远端
git init
git add .
git commit -m "初始化 MkDocs 网站"
git branch -M main
git remote add origin https://github.com/xiaokai1996/my-website.git
git push -u origin main

# 2. 安装mkdocs github部署插件
pip install mkdocs-git-revision-date-localized-plugin

# 3. 把md渲染成静态文件，触发github的流水线，部署网页
mkdocs gh-deploy
```

为啥`mkdocs gh-deploy`可以一键部署呢？因为他会自动帮你做3件事情：

1. 把md渲染成静态文件，放到本地`site`文件夹下
2. 自动创建一个`gh-pages`分支，把`gh-pages`分支推送到远程仓库
3. 自动触发github上的`action`部署流水线，github启动虚拟机运行

## FAQ 🎨

### 🙋 日常工作流是什么样的?

1. 新增/修改md文件，修改配置
2. 在本机预览调试 http://127.0.0.1:8000/
3. 感觉非常棒了，一键发布
4. 访问 [我的github网站](https://xiaokai1996.github.io/my-website/) 预览

### 🙋 编译的静态文件是否要提交？

不需要提交，如果不小心提交了，可以使用下面这种方法删除，然后新建一个`。gitignore`文件把`/site`，`/venv`等文件夹加进去
```bash
git rm -r --cached site
git commit -m "删除 site"
git push
```

### 🙋 如何新增目录？

每个md都会生成一个网页

`mkdocs.yml`中`nav`决定了网站的目录结构，新增对应的目录架构就行

文件目录
```sql
docs/
├── guide/
│   ├── install.md
│   └── usage.md
├── tutorials/
│   ├── tutorial1.md
│   └── tutorial2.md
```

和网站目录一一对应
```yaml
nav:
  - 首页: index.md
  - 指南:
      - 安装: guide/install.md
      - 使用: guide/usage.md
  - 教程:
      - 教程1: tutorials/tutorial1.md
      - 教程2: tutorials/tutorial2.md
```

### 🙋 如何把实现单个换行符换行？

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

### 🙋 如何上传图片并设置格式？

使用这种相对写法，可以直接显示图片，注意使用相对地址

```md
![hello](../assets/images/image-0001.png)
```

如果开启`pymdownx`的扩展插件，还可以实现图片居中、缩放效果，需要在`mkdocs.yml`中的`markdown_extensions`添加一个`pymdownx.extra`支持

```md
![hello](../assets/images/image-0001.png){ width=200px }
![hello](../assets/images/image-0001.png){ width=50% }
```

还可以在doc目录下配置`docs/stylesheets/extra.css`，配置一些常用的格式，不同的格式可以叠加组合！

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

此时的图片可以这样设置格式：
```md
![hello](../assets/images/image-0001.png){ .my-img }
![hello](../assets/images/image-0001.png){ .center-image width=200px }
```
### 🙋 如何美化标题字体？

可以在`stylesheets/extra.css`文件中设置不同层级标题的字号、字体

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
```

注意引入css文件需要再`mkdocs.yml`中显示的引入`extra_css`

### 最终的`mkdocs.yml`和`extra.css`配置参考

最终配置如下，`mkdocs.yml`是一个全局控制器，重点是`markdown_extensions`里面的`pymdownx.extra`插件支持，和额外引入的样式文件`stylesheets/extra.css`

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

`stylesheets/extra.css`主要是用来配置标题字体、自定义图片格式等。

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