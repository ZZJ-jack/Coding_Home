# Coding 团队展示网站

这是一个为「Coding」技术团队构建的静态网站项目，包含团队介绍、网址导航、文件下载中心以及基于 Markdown 的技术博客。网站采用纯 HTML/CSS/JS 实现，拥有动态矩阵雨背景，无需后端数据库即可运行。

## 功能模块

- **主页**：团队简介、打字机欢迎语、特色卡片。
- **网址导航**：从 `files/sites.txt` 动态加载网站列表，支持搜索，自动匹配图标。
- **文件中心**：从 `files/files.txt` 按分类展示文件，支持一键下载。
- **关于团队**：展示团队成员、角色和团队文化。
- **技术博客**：基于 `post/articles.json` 索引 Markdown 文章，模态框内阅读。

## 技术栈

- HTML5 / CSS3（自适应布局，暗色主题）
- JavaScript（ES6+）
- Canvas 动画（矩阵雨背景）
- [Font Awesome 6](https://fontawesome.com/) 图标库
- [marked.js](https://marked.js.org/) Markdown 解析
- [github-markdown-css](https://github.com/sindresorhus/github-markdown-css) 文章样式

## 文件结构

```
/
├── index.html              # 主页
├── sitego.html             # 网址导航
├── files.html              # 文件中心
├── about.html              # 关于团队
├── articles.html           # 技术博客
├── files/
│   ├── sites.txt           # 网址列表（格式：名称:网址）
│   └── files.txt           # 文件列表（格式：-分类名\n文件:路径）
├── post/
│   ├── articles.json       # 文章索引
│   └── *.md                # Markdown 文章文件
└── README.md               # 本文件
```

## 配置说明

### 1. 网址导航 (`files/sites.txt`)

每行一条记录，格式为 `网站名称:网址`（网址可带或不带协议头，程序会自动补全 `https://`）。

**示例：**
```
Google:google.com
GitHub:https://github.com
PVZ:https://pvz.gocoding.dpdns.org
```

### 2. 文件中心 (`files/files.txt`)

- 以 `-分类名` 开头定义分类（如 `-Tools`、`-Games`）。
- 分类下方每行一个文件：`文件名:文件下载URL`。

**示例：**
```
-Tools
VSCode Setup:https://example.com/vscode.exe
7-Zip:https://7-zip.org/a/7z2407-x64.exe

-Games
PVZ Online:https://example.com/pvz.zip
```

> URL 末尾不要有空格或多余字符，程序会自动过滤 `\r`。

### 3. 技术博客 (`post/articles.json`)

JSON 数组格式，每篇文章包含 `title`、`date`、`path` 三个字段。

**示例：**
```json
{
  "articles": [
    {
      "title": "图论最短路算法详解",
      "date": "2026-05-28",
      "path": "./post/2026-05-28-shortest-path.md"
    },
    {
      "title": "C++ 高精度运算",
      "date": "2026-05-20",
      "path": "./post/cpp-bigint.md"
    }
  ]
}
```

- `path` 相对于网站根目录，建议以 `./post/` 开头。
- 文章文件为 `.md` 格式，支持 YAML Front Matter（会被自动移除，不影响阅读）。
- 日期仅用于排序和展示，不参与文章内容解析。

## 部署方式

该项目完全静态，可部署在任何 Web 服务器或托管平台（如 GitHub Pages、Vercel、Netlify、Nginx 等）。

**本地预览：**
```bash
# 使用 Python 快速启动一个 HTTP 服务器
python -m http.server 8080
# 或使用 npx serve
npx serve .
```
然后访问 `http://localhost:8080`。

**注意事项：**
- 确保 `files/` 和 `post/` 目录及其内部文件存在且路径正确。
- 如果使用 GitHub Pages，建议将所有文件放在仓库根目录。
- 文章和文件列表的加载依赖 `fetch` API，请确保通过网络访问（不要直接双击 `file://` 打开），否则会出现跨域或资源加载失败。

## 定制化

- **修改团队信息**：编辑 `about.html` 中的成员卡片和介绍文字。
- **更换背景动画**：每个页面底部的 `createMatrixEffect` 函数可以调整字符集、颜色、速度等参数。
- **导航栏**：各页面 `header` 部分的 `nav-buttons` 可增删链接，注意保持相对路径一致。

## 开源协议

MIT License。欢迎自由使用、修改和分享。

---

**Developed by ZZJ & Coding Team**  
*2026, Coding*