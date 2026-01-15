# 如何用 MkDocs 搭建网站

⸻

阶段 0：理解 MkDocs 和静态网站

背景知识：
	•	MkDocs 是一个 静态站点生成器（Static Site Generator，简称 SSG）。
	•	它的原理是：你写 Markdown 文件 → MkDocs 根据主题模板生成 HTML/CSS/JS 文件 → 可以直接部署到网站（无需服务器渲染）。
	•	Markdown 是一种轻量标记语言，你写的文章看起来像普通文本，但可以加标题、列表、链接、图片等。

所以你的思路是：
	1.	写内容 → Markdown
	2.	配置网站 → mkdocs.yml
	3.	生成网站 → mkdocs build
	4.	部署 → GitHub Pages 或其他静态服务器

⸻

阶段 1：准备环境

1. 安装 Python

MkDocs 是用 Python 写的，所以你需要 Python 环境。
	•	推荐版本：Python 3.9+
	•	检查是否已安装：

python --version
# 或者
python3 --version

如果显示版本号 ≥ 3.9 就可以。
	•	Windows 用户可以去 Python 官网￼ 安装，并勾选 “Add Python to PATH”。

⸻

2. 安装 MkDocs

命令：

pip install mkdocs

背景知识：pip 是 Python 的包管理器，用它可以安装 Python 写的软件包。MkDocs 就是一个 Python 包。

安装完成后可以检查版本：

mkdocs --version


⸻

3. 安装主题（可选但推荐）
	•	MkDocs 默认主题比较简洁，但很多人用 Material for MkDocs，现代感强，功能丰富（搜索、代码高亮、导航栏等）。

安装：

pip install mkdocs-material

背景知识：主题就是 MkDocs 用来把 Markdown 转换成 HTML/CSS/JS 的模板，不同主题页面风格不一样。

⸻

阶段 2：创建网站

1. 初始化项目

mkdocs new mysite

	•	这会创建一个名为 mysite 的文件夹，里面有：
	•	mkdocs.yml → 网站配置文件
	•	docs/ → 放 Markdown 文件的目录（网站内容都在这里）

目录结构示例：

mysite/
├── docs/
│   └── index.md
└── mkdocs.yml

背景知识：
	•	index.md 是网站首页。
	•	mkdocs.yml 控制网站标题、导航栏、主题、插件等。

⸻

2. 预览网站

进入项目目录：

cd mysite

启动本地服务器：

mkdocs serve

	•	打开浏览器访问 http://127.0.0.1:8000/
	•	你会看到一个默认的 MkDocs 网站
	•	好处：你可以实时预览修改内容，无需每次生成 HTML

背景知识：serve 命令会启动一个临时的本地服务器，把生成的 HTML 渲染出来，这样你可以在浏览器里看到效果。

⸻

3. 修改网站内容
	•	编辑 docs/index.md：

# 欢迎来到我的网站

这是我的第一个 MkDocs 网站。

## 第二个标题
内容可以加列表：
- 项目 1
- 项目 2

	•	添加更多页面：
	•	创建 docs/about.md

# 关于我

这是我的介绍页面。

	•	配置导航栏，在 mkdocs.yml 中：

site_name: 我的MkDocs网站
theme:
  name: material
nav:
  - 首页: index.md
  - 关于: about.md

背景知识：
	•	nav 控制导航菜单顺序
	•	Markdown 文件路径对应网站 URL，index.md 默认是 / 首页

⸻

阶段 3：生成静态网站
	•	当你修改完内容和配置后，可以生成静态文件：

mkdocs build

	•	生成目录：site/
	•	里面包含最终的 HTML、CSS、JS 文件
	•	这些文件可以直接上传到服务器，用户访问就能看到网站

背景知识：
	•	静态网站不需要数据库和后台，直接用浏览器就能打开 HTML 文件

	•	你也可以直接本地预览：

mkdocs serve


⸻

阶段 4：部署到 GitHub Pages

1. 创建 GitHub 仓库
	•	登录 GitHub￼
	•	新建仓库，例如 mydocs
	•	仓库名可以和本地文件夹一样，也可以不同

⸻

2. 初始化 Git 并上传代码

git init
git add .
git commit -m "初始化 MkDocs 网站"
git branch -M main
git remote add origin https://github.com/用户名/mydocs.git
git push -u origin main

背景知识：
	•	Git 是版本管理工具
	•	GitHub 是远程托管仓库
	•	push 是把本地代码上传到 GitHub

⸻

3. 使用 MkDocs GitHub 部署插件
	•	安装插件：

pip install mkdocs-git-revision-date-localized-plugin

	•	直接部署到 GitHub Pages：

mkdocs gh-deploy

说明：
	•	gh-deploy 会自动：
	1.	生成静态文件到 site/
	2.	创建一个 gh-pages 分支（GitHub Pages 默认分支）
	3.	上传到远程仓库
	•	之后你就可以访问：

https://用户名.github.io/mydocs/


⸻

4. 小技巧
	•	如果修改内容，直接：

mkdocs gh-deploy

就会自动更新网站，无需重复创建仓库。
	•	如果使用 Material 主题，可以在 mkdocs.yml 加一些高级配置：

theme:
  name: material
  features:
    - navigation.tabs
    - navigation.top
    - search.highlight

这些会增加标签页导航、顶部导航和搜索高亮功能。

⸻

✅ 阶段 5：网站维护思路
	1.	所有内容都在 docs/ → Markdown 文件
	2.	配置在 mkdocs.yml → 网站信息、导航栏、主题、插件
	3.	本地预览用：

mkdocs serve

	4.	部署更新用：

mkdocs gh-deploy

这样你就有了 完整的静态网站流程：写内容 → 本地预览 → 配置 → 部署到 GitHub Pages

⸻

如果你愿意，我可以帮你直接写一个 示例项目结构 + mkdocs.yml 配置文件 + Material 主题示例，你拷贝就能跑起来。

你希望我帮你直接写吗？