# diagram-design · DeepSeek Harness 专用 skill

**一句话需求，换回一个自包含的 `.html` 图表文件——内联 SVG，能双击打开、能提交进 Git、能直接放上幻灯片。**

`MIT` · 45 种图表类型 · 导入 `.drawio` / Mermaid / `.excalidraw` · 导出 SVG + PNG · Python 3.10+ · 无构建步骤

中文 | [English](README.en.md)

编辑设计风格是有具体定义的：只用一种强调色、1px 细线、没有阴影、所有坐标落在 4px 网格上。这个 skill 把这套规则
连同「什么内容该用哪种图」的路由表一起交给 agent，最终产出单文件——没有外链图片，除 Google Fonts 外不发任何网络请求。

## 能产出什么

- **单文件交付。** 内联 SVG，可选 Google Fonts，除动画控制器外无 JS，无外部图片。
- **45 种类型，各三种皮肤。** 架构图、架构增量、IT 现状、流程图、时序图、状态机、ER/数据模型、泳道图、四象限、
  雷达图、时间线、甘特图、桑基图、Wardley 图、看板、用户旅程、UML 类图、数据库 schema……每种都有 minimal light、
  minimal dark、full editorial 三个变体。
- **已有的图能重画。** 给 `.drawio`、Mermaid 代码块或 `.excalidraw` 文件，先抽出结构（节点/边/容器），再按同一套
  风格重排；不改内容、不乱加节点。
- **品牌 onboarding。** 给一次站点地址（或直接粘色值），之后的图沿用你的纸色、墨色、强调色和字体。profile 存在
  `~/.diagram-design/profiles/`，不在安装目录里，升级不会覆盖。
- **带质量门。** `scripts/self_check.py` 会拦下损坏的无障碍契约、远程资源、超出画布的内容和残缺的动画标记。

## 安装

仓库根目录**就是** skill bundle（`SKILL.md` 在最外层），所以「安装」= 把这个目录放到 `<skill 根>/diagram-design`。

```bash
# 全局安装：所有会话、所有项目都能用（推荐）
git clone https://github.com/jishi7/dsh-diagram-design.git ~/.dsh/skills/diagram-design
```

```powershell
# Windows 同一条命令
git clone https://github.com/jishi7/dsh-diagram-design.git "$HOME\.dsh\skills\diagram-design"
```

已经有一份 checkout？用链接代替二次克隆（Windows junction 不需要管理员权限）：

```powershell
New-Item -ItemType Junction -Path "$HOME\.dsh\skills\diagram-design" -Target "<你的 checkout 路径>"
```

```bash
ln -s "<你的 checkout 路径>" ~/.dsh/skills/diagram-design      # macOS / Linux
```

只想在某个项目里启用：把目标换成 `<项目>\.dsh\skills\diagram-design`（rank 100，优先于全局根）。
不需要重启：skill provider 会监视这些根目录，在下一个模型步骤发布替换目录。

DSH 按以下优先级（数字越小越优先）扫描 skill 根目录：

| Rank | 根目录 |
|---|---|
| 100 | `<项目根>/.dsh/skills` |
| 200 | `<项目根>/.agents/skills` |
| 300 | `customSkillDirs`（配置项） |
| 400 | `~/.dsh/skills` |
| 500 | `~/.agents/skills` |

每个根只扫**一层**：`<根>/<名字>/SKILL.md` 或 `<根>/<名字>.md`，不递归。同名 skill 由 rank 小的胜出。

## 使用

直接描述你要什么：

> 给我画一张这个应用的架构图：前端、后端、数据库、Redis 缓存。

> 我需要一张象限图，按「影响力 × 投入」排 Q2 的项目。

> 把这个 Mermaid 块重画一下，放在我的 deck 里。

也可以 `/diagram-design` 显式调用。在一个项目里首次生成时，它会先问你要不要做品牌 onboarding（配色 + 字体），
还是沿用默认皮肤。

## 依赖与自检

| 功能 | 依赖 |
|---|---|
| 生成 HTML/SVG、自检 | Python 3.10+（Windows 用 `python`，**不要**用 Microsoft Store 的 `python3` 桩） |
| 导出 PNG（`scripts/export_png.py`） | Playwright + Chromium：<br>`python -m pip install playwright && python -m playwright install chromium` |
| 导入 draw.io / Mermaid / Excalidraw | 只需要 Python |

```bash
python scripts/self_check.py <file.html>        # 打印 OK，或者说明哪里不合格
python scripts/export_svg.py <file.html>        # 在文件旁生成只含图表的 SVG
```

## 仓库结构

```
SKILL.md              路由表、设计系统、检查清单、DSH 环境说明
references/           63 个文件：45 个 type-*.md + 18 个专题（style-guide、语义模式、导入导出、onboarding、profiles）
scripts/              6 个 Python 助手：self_check、export_svg/png、drawio/mermaid/excalidraw 三个 extract
assets/               217 个文件：5 个模板 + 210 个示例 + icons.html + index.html 画廊
.github/              3 个文件：issue 表单 + PR 检查清单（没有 CI）
```

`SKILL.md` 只负责路由：一次请求只加载对应的一个 `type-*.md`，需要时再加 `primitives-core.md` / `layout-budget.md`。
`references/` 不会被预加载——没用之前，这个 skill 的常驻成本只有一个名字和一条描述。

## 与上游的差异

这是 [cathrynlavery/diagram-design](https://github.com/cathrynlavery/diagram-design)（MIT）的 DSH 专用精简版。
删掉的都是只服务于其他宿主或作者自己 CI 的东西：

- Claude Code / Codex / Factory Droid / Qoder 的插件清单与 marketplace 文档
- 斜杠命令与 Pi 提示词模板（这里改成自然语言触发，或直接调脚本）
- `docs/`（ADR、截图、hero GIF）、`.github/` 的 workflow、上游的 CI lint/verify 脚本
- 所有假设 `skills/diagram-design/` 是安装根的路径——本仓库的根目录就是 bundle

**刻意保留**的是 `assets/` 里 210 个示例和 5 个模板：它们是模型抄写标记的「标准答案」，不是文档。

## 更新与分支

```bash
git -C ~/.dsh/skills/diagram-design pull     # clone 或 junction 都适用，目录会自动刷新
```

分支模型：`dsh` 是默认分支，也是唯一被编辑的分支；`main` 是它的镜像，每次发布同步，两个名字 clone 下来的树一致。

上游会独立演进；因为本仓库调整过目录结构，合并 `origin/main` 需要手工重放这些移动。更省事的做法是重跑一次转换，
而不是去解 600 个文件的 rename 冲突。

## 常见问题

**装好了但 DSH 看不到？**
先检查 `SKILL.md` 的 frontmatter 能不能被 YAML 解析。`description` 里出现 `": "` 时必须加引号——未加引号会静默
丢失整个 skill：磁盘上一切正常，模型侧只会收到「未知 skill」。

**`python3` 报错？**
Windows 上 `python3` 往往是 Microsoft Store 的占位别名，不是解释器。用 `python`。

**能不能出 PNG？**
需要 Playwright + Chromium，见「依赖与自检」。没装时自检和 SVG 导出照常工作，缺 Playwright 只算 `warn`。

**切换分支后 skill 失效？**
用 junction 安装时，skill 指向的是工作树。`git checkout main` 会把 `SKILL.md` 换回旧结构的位置，DSH 立刻找不到它。
要长期稳定，用 `git worktree` 或独立 clone 安装。

## 许可

MIT，见 [LICENSE](LICENSE)。图标许可（Tabler MIT、Simple Icons CC0、Devicon MIT、log-z MIT）记录在
[THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md) 和 `references/primitive-icons.md`。原始设计与文案
© Cathryn Lavery；DSH 相关改动（结构、宿主移除、命令改写、本自述文件）以同一许可发布。
