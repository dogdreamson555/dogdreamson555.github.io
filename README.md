# dogdreamson555 的博客

使用 Hugo 与 Theme Stack 构建的个人博客。前两阶段“工程可构建”和“写作可重复”已通过示例验证，作者已确认阅读效果没有发现问题；真实文章兼容性待验证。GitHub Pages 部署和 giscus 评论将在后续阶段完成。

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

## 新增文章

尚未决定公开的材料先在 `.private/` 或仓库外整理。以下操作生成的源文件会进入公开仓库，`draft: true` 仅控制是否生成网页。

在仓库根目录运行（将 `my-second-post` 换成唯一的英文标识）：

```sh
hugo new content post/my-second-post/index.md
```

命令使用 `archetypes/post.md`，生成 `content/post/my-second-post/index.md`，日期含时区，`slug` 自动取文章目录名，新文章默认为 `draft: true`。这是 Hugo 的[文章模板机制](https://gohugo.io/content-management/archetypes/)，无需额外脚本。

| 字段 | 填写方式 |
| --- | --- |
| `title` | 改成中文标题即可 |
| `date` | 核对真实发布日期及 `+08:00` 时区；未来日期默认不输出 |
| `slug` | 保持唯一；发布后不要随标题修改，以免改变链接及后续评论映射 |
| `draft` | 写作时为 `true`，准备公开发布时改为 `false` |
| `description` | 可选摘要；留空时由正文生成摘要 |
| `categories` / `tags` | 可选，例如 `["技术"]`、`["Hugo", "笔记"]` |
| `image` | 可选封面，例如 `"cover.jpg"`；图片放在同一目录，没封面就留空 |

文章永久链接为 `/p/<slug>/`。新增时确认目录未使用，并检查现有标识，避免两个目录中的文章填写相同 slug：

```powershell
Get-ChildItem content/post -Recurse -Filter index.md | Select-String '^slug:'
```

## 正文和配图

正文从元信息结束的第二行 `---` 之后开始，章节从 `##` 起写。代码块注明语言，表格使用标准 Markdown。将图片与 `index.md` 放在一起，用相对路径引用：

```text
content/post/my-second-post/
  index.md
  example.png
```

```markdown
![对图片内容的简短说明](example.png "可选图注")

[跳到示例章节](#示例章节)

[查看另一篇文章]({{< relref "post/build-preview" >}})
```

章节链接对应实际标题；发布前点击确认。`relref` 会在构建时检查目标文章是否存在。不要把本机绝对路径、编辑器附件路径或 `.private` 路径写进正文。Obsidian 的 `[[双链]]`、`![[嵌入]]` 等专用语法先手工转成标准链接和图片语法；尚未使用真实笔记验证兼容性。

可参考 `build-preview` 中的中文正文、长代码、表格和 `writing-flow.svg` 配图。SVG 是本项目编写的流程图，没有账号、定位元数据或外部资源；自己的截图仍需检查隐私与图片元数据。

## 检查与准备发布

1. 执行 `hugo server -D --bind 127.0.0.1`，打开首页及新文章，检查中文、目录、长代码滚动、表格、配图和站内链接；桌面与手机宽度各看一次。
2. 核对标题、摘要、slug、日期与公开内容，将要发布的文章设为 `draft: false`。
3. 按下节命令向全新目录生产构建，确认目标文章与图片存在，草稿未出现在 HTML、首页或订阅中。
4. 查看 `git status`、本次 diff 及附件，确认源文件齐全且不含私密材料。部署尚未配置，当前构建成功不表示已上线。

仓库中的 `second-post` 是按模板实际创建并修改中文标题的示例，保留 `draft: true`；使用 `-D` 时可访问 `/p/second-post/`，普通生产构建不会输出。两篇示例在正式发布前决定是否保留。

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
- `archetypes/post.md`：自动生成标题、日期、唯一目录标识及草稿状态的文章模板（手动修改 slug 后需自行检查唯一性）。
- `content/post/build-preview/index.md`：非正式的工程预览示例，发布前应确认是否保留。
- `content/post/build-preview/writing-flow.svg`：随文章管理的写作流程配图。
- `content/post/second-post/index.md`：由模板实际创建的第二篇示例草稿。
- `themes/hugo-theme-stack/`：官方主题子模块，保留其 GPL-3.0 许可证和页面署名。
- `baseURL` 暂按用户站点设为 `https://dogdreamson555.github.io/`；真实 Pages 设置与在线地址尚未验证。

2026-09-08：版本和配置依据 [Stack v4.0.3](https://github.com/CaiJimmy/hugo-theme-stack/releases/tag/v4.0.3) 的主题元数据与默认配置。已使用上述固定版本向全新目录完成生产和草稿构建；本地首页、两篇示例及配图均返回 HTTP 200，代码高亮、表格、章节锚点和跨文章链接已检查。第二篇草稿未进入生产输出（包括订阅），改中文标题后链接保持不变。作者已确认桌面、手机宽度下的阅读效果没有发现问题。真实笔记、截图及编辑器专用语法尚待真实素材验证。
