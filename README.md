# dogdreamson555 的博客

使用 Hugo 与 Theme Stack 构建的个人博客。目前完成第一阶段：本地工程可构建。写作模板与正式文章将在后续阶段完成。GitHub Pages 部署和 giscus 评论将在后续阶段完成。

## 固定依赖

| 依赖 | 版本 |
| --- | --- |
| Hugo Extended | `0.165.0`（同时记录于 `.hugo-version`） |
| Theme Stack | `v4.0.3`，提交 `3e123a30b79b5d52a3a8e88a9dd678fcfd28e418` |
| Git | 用于检出主题子模块 |

主题使用 Git 子模块管理，无需 Go 或 Node.js。主题版本由仓库的 gitlink 固定；不要使用 `git submodule update --remote` 自动升级。`.hugo-version` 是版本记录，不会自动切换本机 Hugo；未来 Actions 应安装同一版本。

安装 [Hugo Extended 0.165.0](https://github.com/gohugoio/hugo/releases/tag/v0.165.0) 后，运行 `hugo version`，确认包含 `v0.165.0` 和 `extended`。

首次克隆：

```sh
git clone --recurse-submodules https://github.com/dogdreamson555/dogdreamson555.github.io.git
cd dogdreamson555.github.io
```

已有仓库补齐主题：

```sh
git submodule update --init --recursive
```

## 本地预览

在仓库根目录执行：

```sh
hugo server --bind 127.0.0.1
```

打开 <http://localhost:1313/>，示例文章位于 <http://localhost:1313/p/build-preview/>。需要预览草稿时使用 `hugo server -D`，按 Ctrl+C 停止服务。

## 生产构建

普通构建命令是 `hugo --environment production --minify`，默认输出到被忽略的 `public/`。发布检查必须使用全新目录，避免混入旧的草稿或已删除文章。PowerShell 示例（每次生成唯一目录）：

```powershell
$buildDir = Join-Path 'public' ([guid]::NewGuid().ToString('N'))
hugo --environment production --minify --destination $buildDir
if ($LASTEXITCODE -ne 0) { throw 'Hugo build failed' }
Write-Output "构建输出：$buildDir"
```

生产配置排除草稿、未来文章和过期文章。`draft: true` 不会隐藏公开仓库中的源文件；尚未决定公开的内容应留在被忽略的 `.private/` 或仓库外。

## 文件与当前状态

- `config/_default/`：中文站点配置、主题参数与 Markdown 渲染配置。
- `content/post/build-preview/index.md`：非正式的工程预览示例，发布前应确认是否保留。
- `themes/hugo-theme-stack/`：官方主题子模块，保留其 GPL-3.0 许可证和页面署名。
- `baseURL` 暂按用户站点设为 `https://dogdreamson555.github.io/`；真实 Pages 设置与在线地址尚未验证。

2026-09-08：已使用上述固定版本向全新目录完成生产构建；本地首页、示例文章及其 CSS/JS 均返回 HTTP 200。桌面和手机上的实际阅读效果待作者确认。
