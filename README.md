# xiao-xiao-cai 的个人主页

面向 AI 应用开发／智能体开发岗位的个人展示空间，用于补充简历中的项目细节、演示材料和证书图片。目前提供建设中的首页、示例图片和自定义 404 页面。

**网站地址：<https://xiao-xiao-cai.github.io/>**

**源码仓库：<https://github.com/xiao-xiao-cai/xiao-xiao-cai.github.io>**

## 技术方案

Astro + TypeScript + 原生 CSS，使用 npm 管理依赖。Astro 构建生成 HTML、CSS 和静态资源，由 GitHub Actions 发布到 GitHub Pages。交互可以使用浏览器端 JavaScript；需要密钥或数据库的服务端功能应部署在独立后端。

本地与 CI 使用 Node.js 24。TypeScript 使用与 `@astrojs/check` 兼容的 6.x 版本；依赖版本记录在 `package.json` 和 `package-lock.json` 中。

## 本地开发

先安装 Node.js 24，再在仓库根目录执行以下 PowerShell 命令（使用 RTK；未安装时可去掉 `rtk proxy` 前缀）：

```powershell
rtk proxy npm ci
rtk proxy npm run dev
```

访问终端显示的本地地址，默认是 <http://localhost:4321>。按 `Ctrl+C` 停止。

| 命令 | 用途 |
| --- | --- |
| `rtk proxy npm run dev` | 启动开发服务 |
| `rtk proxy npm run check` | 检查 Astro 与 TypeScript 类型 |
| `rtk proxy npm run build` | 生成 `dist/` 静态产物 |
| `rtk proxy npm run preview` | 本地预览已构建的产物 |

发布前依次执行类型检查、构建和预览，检查首页、图片、移动端布局以及 `/404.html`。依赖安装或更新后应一并提交锁文件。

## 目录结构

```text
.github/workflows/deploy.yml  # 构建及 Pages 部署
public/images/               # 原样发布的图片
src/layouts/Layout.astro     # 公共页面结构、元信息和导航
src/pages/index.astro        # 首页
src/pages/404.astro          # 自定义 404 页面
src/styles/global.css       # 公共样式和响应式布局
astro.config.mjs            # 站点地址与静态输出配置
```

`node_modules/`、`dist/`、`.astro/`、环境变量文件及本地工具配置由 Git 忽略。`.gitattributes` 统一文本文件为 LF 换行。

## 添加内容

- **个人介绍**：更新 `src/pages/index.astro` 中的介绍、链接和页面描述。
- **项目详情**：新增 `src/pages/projects/项目英文标识.astro`，复用公共布局，并从首页添加链接。Astro 会生成可直接访问和刷新的静态页面。项目内容可围绕问题、本人贡献、实现过程、结果和演示展开。
- **证书图片**：将适合公开的图片放入 `public/images/certificates/`，通过 `/images/certificates/文件名.webp` 引用。上传前检查个人信息和使用权限，压缩图片并设置说明文字、替代文字及宽高；首屏外图片可使用 `loading="lazy"`。
- **文字较多的页面**：可使用 Astro 支持的 Markdown 页面，按实际内容增长再组织目录。

## GitHub Pages 配置

1. 仓库保持公开，默认分支为 `main`。
2. 打开仓库 **Settings → Pages → Build and deployment → Source**，选择 **GitHub Actions**。
3. 推送到 `main` 会触发 `Deploy to GitHub Pages` 工作流，也可以在 **Actions → Deploy to GitHub Pages → Run workflow** 手动运行远端已有提交。
4. 构建任务使用 Node.js 24，按锁文件安装依赖，依次进行类型检查、静态构建和产物上传。部署任务依赖构建成功，并通过 `github-pages` 环境发布。
5. 工作流只授予构建所需的源码读取权限及部署所需的 `pages: write`、`id-token: write`。使用 GitHub 自动提供的令牌。
6. 在 **Actions** 查看任务日志与部署地址，或在 **Settings → Pages** 查看发布状态。

此仓库名称符合 `<用户名>.github.io` 规则，站点根路径为 `/`。`astro.config.mjs` 的 `site` 为 `https://xiao-xiao-cai.github.io`，资源路径应写成 `/images/...`。Actions 只上传构建后的 `dist/`。

本地测试文件在构建后分别对应 `/`、`/images/site-preview.svg` 和 `/404.html`；访问不存在的路径时，GitHub Pages 会返回自定义 404 页面。

## 版本控制与发布

首次初始化将完整基础版本同步到 `origin/main`。后续日常开发先保留本地提交：

```powershell
rtk git status
rtk git diff
rtk git add src
rtk git commit -m "docs: update personal introduction"
```

按实际修改选择暂存文件。`commit` 记录在本地；`push` 才会同步远端并触发上线。后续推送需经仓库所有者明确决定。本仓库使用 GitHub noreply 邮箱作为提交邮箱。

## 常见问题

| 现象 | 检查方法 |
| --- | --- |
| 网站返回 GitHub 默认 404 | 检查 Pages 发布源是否为 GitHub Actions，以及部署任务是否成功；首次发布可能需要等待几分钟 |
| 图片或 CSS 加载失败 | 检查路径和文件名大小写；确认文件已提交且包含在构建产物中；本站资源以 `/` 为根路径 |
| Actions 构建失败 | 打开失败步骤日志，核对 Node.js 24 和锁文件，在本地运行 `npm ci`、`npm run check`、`npm run build` |
| 部署提示 Pages 未启用或权限错误 | 检查 Pages 发布源、仓库 Actions 策略、工作流权限和 `github-pages` 环境限制，再运行已有提交 |
| 页面仍显示旧内容 | 确认远端提交与部署记录，等待发布完成，再刷新浏览器缓存；本地提交本身不会更新网站 |
| 本地和线上 404 行为有差异 | 本地可直接预览 `/404.html`；不存在路径的 HTTP 404 和自定义页面需在 GitHub Pages 上验证 |

## 官方文档

- [GitHub Pages 文档](https://docs.github.com/en/pages)
- [配置发布源](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
- [自定义 Actions 工作流](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)
- [自定义 404 页面](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-custom-404-page-for-your-github-pages-site)
- [Astro 部署到 GitHub Pages](https://docs.astro.build/en/guides/deploy/github/)
