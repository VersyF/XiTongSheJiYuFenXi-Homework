# 实践项目2：个人实验报告

## 1. 基本信息

- GitHub 登录名：
- 个人仓库地址：
- 最终网站地址：

## 2. 实践过程与关键截图

### 2.1 仓库创建

说明操作、结果和对应截图。![](D:\2026-2027\系统设计与分析\Test\XiTongSheJiYuFenXi-Homework\report\images\1.png)

### 2.2 项目结构与 MkDocs 配置

![](D:\2026-2027\系统设计与分析\Test\XiTongSheJiYuFenXi-Homework\report\images\2.1.png)

![](D:\2026-2027\系统设计与分析\Test\XiTongSheJiYuFenXi-Homework\report\images\2.2.png)

### 2.3 本地构建或预览

![](D:\2026-2027\系统设计与分析\Test\XiTongSheJiYuFenXi-Homework\report\images\3.png)



### 2.4 Pages 设置

![](D:\2026-2027\系统设计与分析\Test\XiTongSheJiYuFenXi-Homework\report\images\4.png)

### 2.5 Actions 首次构建和部署

![](D:\2026-2027\系统设计与分析\Test\XiTongSheJiYuFenXi-Homework\report\images\5.png)

### 2.6 网站访问与四页导航检查

![](D:\2026-2027\系统设计与分析\Test\XiTongSheJiYuFenXi-Homework\report\images\6.png)

### 2.7 第二次推送与自动更新验证

- 对应提交链接：
- 对应 Actions 运行链接：[我的技术文档](https://versyf.github.io/XiTongSheJiYuFenXi-Homework/)

![](D:\2026-2027\系统设计与分析\Test\XiTongSheJiYuFenXi-Homework\report\images\7.1.png)

![](D:\2026-2027\系统设计与分析\Test\XiTongSheJiYuFenXi-Homework\report\images\7.2.png)

## 3. 问题与解决

- 核对 `Actions` 中 `build` 的 `Install dependencies`、`Build documentation`、`Upload site artifact` 步骤均成功。

- 核对 `deploy` 的 `Deploy to GitHub Pages` 步骤成功，`github-pages` 环境有部署记录和网站地址。

- 核对 `Settings → Pages` 的 Source 为 GitHub Actions。

- 核对 `.gitignore` 已忽略 `site/`、`.venv/`，`git status` 中没有误提交构建产物和虚拟环境。

- 核对 `mkdocs.yml` 的 `site_url`、`repo_url` 已替换为本人账号和仓库名，`nav` 路径相对于 `docs/`。

## 4. 原理理解

1. docs/ 和 site/ 各保存什么？为什么不用手工修改 site/？
- `docs/` 保存**源文档**，也就是我手写的 Markdown 文件（`index.md`、`about.md`、`guide.md`、`api.md`），它是网站内容的“源头”。

- `site/` 保存 `mkdocs build` **自动生成的静态网站构建产物**，包括 HTML、CSS、JS 和资源文件，是真正被部署到 GitHub Pages 的内容。

- 不用手工修改 `site/`，因为它是构建结果，每次构建都会根据 `docs/` 和 `mkdocs.yml` 重新生成；手工改会被下一次构建覆盖，而且容易和源文档不一致。正确做法是只改 `docs/` 和配置，让构建流程生成 `site/`。
1. 本次工作流在什么情况下自动运行？
- 当代码 **push 到 `main` 分支**，并且改动涉及 `docs/**`、`mkdocs.yml`、`requirements.txt` 或 `.github/workflows/deploy.yml` 时自动运行。

- 也支持在 Actions 页面通过 `workflow_dispatch` **手动触发**。

- 只改 `report/README.md` 或根目录 `README.md` 不在触发路径里，不会自动运行。
1. build 与 deploy 两个任务有什么区别？
- `build` 负责**生成网站**：拉取源码、配置 Python、安装依赖、执行 `mkdocs build --strict` 构建出 `site/`，并把 `site/` 上传为 artifact。

- `deploy` 负责**发布网站**：通过 `needs: build` 等构建成功后，取得 artifact，凭 `pages: write` 和 `id-token: write` 权限，在 `github-pages` 环境中用 `actions/deploy-pages` 把网站发布到 GitHub Pages。

- 简单说，`build` 是“做出来”，`deploy` 是“发出去”；构建失败就不会部署。
1. 只修改 report/README.md 会触发部署吗？为什么？
- **不会**。

- 因为工作流的 `push.paths` 只包含 `docs/**`、`mkdocs.yml`、`requirements.txt` 和 `.github/workflows/deploy.yml`，`report/README.md` 不在这些路径中，所以 push 不会触发该工作流。

- 这是预期行为：报告不影响网站内容，没必要重新构建和部署。
1. 本次方案为何无需创建 gh-pages 分支？
- 本次使用 GitHub 官方 Pages Actions 工作流：`build` 把 `site/` 上传为 artifact，`deploy` 用 `actions/deploy-pages` 直接发布到 GitHub Pages。

- 发布来源在 `Settings → Pages` 设为 **GitHub Actions**，部署由工作流和 `github-pages` 环境管理，不需要把构建产物推到 `gh-pages` 分支再由分支发布。

- 所以不需要创建 `gh-pages` 分支，也不需要开启创建/批准 PR 的权限或填写访问令牌。
