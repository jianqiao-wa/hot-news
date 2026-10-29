# 微信公众号选题热点雷达

基于官方 TrendRadar v6.11.0（提交 `68c8916`），以关键词筛选热榜，为公众号提供选题线索。源码保持官方版本。

## 1. 当前监控

六个热榜平台：微博、知乎、百度、抖音、B站、今日头条。另有科学网官方 RSS“人才高教”和“首页要闻”，补充不在热搜上的高教科研信息，保留近3天候选。研究生、硕博、保研推免、导学、奖助学金、学费住宿、毕业答辩、招聘就业等垂直词可直接入选；高校科研职业与培养信息也纳入候选。AI 筛选、分析、翻译均关闭。

### 日常怎么用

当前提供的是**宽覆盖候选清单**，由你自己筛选；没有开启 AI 自动分析或写稿，也不限制每组展示条数。

1. 打开 Bark → **历史消息 → TrendRadar** 分组，查看“增量分析”或晚间的“全天汇总”。“通知连通性测试”只用于测试收件。
2. 先看学术诚信、导师、研究生等主题组；标题带有原文链接。排名是该平台榜单位置，`N 次` 是抓取中出现的次数，不是阅读量或转发量。不同标题也可能是同一事件，选题时合并看。
3. 按自己的需要从完整候选中挑选，点开原文，再找高校通报、论文、招聘公告等一手材料核实。
4. 写稿前回答：发生了什么？最新进展是什么？与研究生有什么关系？例如“高校招聘门槛变化”可从第一学历、年龄要求、应届身份等角度切入，具体判断以公告原文为准。
5. 需要进一步分析时，把候选标题和原文链接发到当前聊天，可按“事实与待核实点、硕博读者关联、三个选题角度、文章提纲”继续处理。项目里的 AI 开关仍保持关闭。

### 本机“热点新闻分析”网页怎么点

你打开的 `output/html/2026-10-29/20-08.html#tab-0` 是20:08保存的本地快照，不会自动变成后续的云端报告。它不是 MCP 聊天窗口，没有提问框。查看本机新生成的报告请打开 `output/html/latest/daily.html`。

- **全部**：显示这份报告的所有分类；`#tab-0` 对应第一个分类，不代表全部。
- **分类标签**：切换研究生、高教科研、社会事件等分组。
- **搜索新闻标题**：只筛当前报告里的条目，不会联网搜索；没有命中的分类可能不出现。
- **新闻标题**：点击打开原文；点击左侧序号复制标题和链接。
- **右上角 导出 → Markdown**：导出这份报告的文字和链接，便于二次筛选。

GitHub Actions 产生的新报告可在运行详情的 Artifacts 下载解压后打开 `html/latest/daily.html`。云端生成的数据不会自动更新电脑上的旧文件。MCP 是另一种需要连接客户端的可选功能，本次不必安装它也能使用上述报告网页。

### 小红书接入状态

2026-10-29 已核对 NewsNow 当前[数据源列表](https://github.com/newsnext/newsnow/blob/main/shared/sources.json)：尚无小红书热榜接口，[新增需求 #366](https://github.com/newsnext/newsnow/issues/366)仍开放，因此当前维持六个平台。指定小红书博主可另行研究 RSSHub 订阅方案，但它不是全站热榜，需要可用的额外 RSSHub 服务，尚未配置或验证。

## 2. 抓取与推送时间

- 均为北京时间，每天 08:00–22:00 每半小时计划抓取一次，共 29 次；Actions 可能延迟或漏跑，不能保证准点。
- 08:00 ≤ 时间 < 20:00：只推送本日新出现且匹配关键词的标题，无匹配新增时不推送。
- 20:00 ≤ 时间 < 22:00：首次成功推送一次当日汇总，包含当日截至本次运行的已抓取数据；之后继续采集，不再补推。
- 22:00 最后一趟只采集；时段外手动运行同样会抓取，但不生成报告、不推送。
- 按官方排名、出现频次和热度权重排序，取消所有每组数量上限。通过 Bark 推送到 iPhone，长报告按官方逻辑自动拆成多条通知，分组为 `TrendRadar`；条目数量随来源更新变化，不设固定条数。

## 3. 发布到你的 GitHub

部署仓库为 [jianqiao-wa/hot-news](https://github.com/jianqiao-wa/hot-news)，默认分支为 `main`。程序和配置需推送到这个仓库的默认分支后，Actions 才能运行。不要把代码推向 `sansan0/TrendRadar`。

如果用 Git 上传，先把官方远端保留为 `upstream`，将自己的仓库配置为 `origin`，仅提交本次配置和说明；不要上传 `.venv`、运行数据或任何密钥。确保工作流位于仓库默认分支，并在 Actions 页面允许工作流运行。

## 4. 配置 Bark 推送

1. 在 iPhone 打开 Bark，点“去开启”，允许通知。若系统设置中仍未开启，进入 **设置 → 通知 → Bark → 允许通知**。

2. 回到 Bark“服务器”首页，点“发送测试”，确认手机收到。使用示例中的 `YOUR_KEY` 必须已变成真实设备 Key；设置页的 `Device Token` 不是要填入 GitHub 的 Key。

3. 复制推送地址，仅保留服务器地址与第一段真实设备 Key：`https://api.day.app/<真实设备Key>`。不要包含 `curl -X GET`、`/title/body` 或查询参数。若仍显示 `YOUR_KEY` 或 Token 未知，先确认通知权限、网络与 Bark 内置自测正常，再添加 Secret。

4. 打开 [直接新增 Secret](https://github.com/jianqiao-wa/hot-news/settings/secrets/actions/new)，填写：

- Name：`BARK_URL`
- Secret（值）：粘贴上一步的真实推送地址，然后点 **Add secret**。

只需添加 **Repository secret**，不需要 Environment secret，也不要填到 Variables。保存后页面只显示名字，无法重新显示原值，这是正常的。

工作流原生从 `BARK_URL` 读取，配置文件中保持空字符串。企业微信已停用，旧的 `WEWORK_WEBHOOK_URL` Secret 不会再被使用。不要把真实 Bark 地址或设备 Key 写进代码、日志或聊天。

Bark“设置 → 默认保存”可保留通知历史；本项目使用官方发送器，不读取 URL 中的 `group`、`isArchive` 等额外参数。[Bark 官方使用说明](https://github.com/Finb/Bark/blob/master/README.zh.md)。

## 5. 首次手动运行

添加 Secret 后，进入 [Actions → Get Hot News](https://github.com/jianqiao-wa/hot-news/actions/workflows/crawler.yml) → **Run workflow** → 分支 `main` → 勾选“仅发送 Bark 测试通知” → 执行。这个测试不受推送时段、新增数量和晚间去重影响，只发送一条官方连通性测试消息；仍需在 iPhone 确认收到。

想立即查看真实完整候选时，取消通知测试，勾选 **“手动发送完整候选汇总”**。它仅对这次执行忽略推送时段与晚间去重，不修改正常定时策略；可能产生多条 Bark 通知。

日常抓取测试：取消勾选该选项，在 08:00–22:00 内运行。首次运行会把当天首次见到的匹配标题视作新增；重复运行可能因没有新增或晚间已推送而保持安静。Secret 缺失或地址格式错误时，普通模式会警告、跳过推送并继续抓取；专用通知测试会报错，提示编辑已有 Secret。绿勾不代表手机已经收到通知。

本地检查（在项目目录）：

```powershell
uv sync --frozen --no-dev
uv run python -m trendradar --doctor
uv run python -m trendradar --show-schedule
uv run python -m trendradar
```

本机若 `uv` 尚未进入 PATH，可将 `uv` 换成 `python -m uv`。GitHub Secrets 不会自动注入本机，因此本地没有通知渠道是正常现象。

## 6. 查看错误和报告

**Actions → Get Hot News → 某次运行 → crawl → 对应步骤**。重点看 `Install dependencies`、`Restore daily crawler state`、`Run crawler` 和 `Save daily crawler state`。运行摘要会列出本次实际抓取条数和各平台成功/失败情况，全源失败会报错。在运行详情的 Artifacts 下载 `trendradar-report-…` 查看 HTML 和体检报告（保留 7 天，静默时段可能只有体检报告）。本地报告位于 `output/html/`，体检报告为 `output/meta/doctor_report.json`。

当日热榜和 RSS SQLite 分别存在 `output/runtime/news/`、`output/runtime/rss/`，Actions 分别恢复、保存同日缓存，维持增量去重及晚间 `once.push`。**不要清理当天缓存**；缓存丢失、保存失败或被淘汰会造成当日历史不完整，并可能重推。缓存只存新闻及调度记录，不存密钥。

## 7. 每周 Check In

每 7 天内至少一次：**Actions → Check In → Run workflow**。它删除工作流运行历史以重置 7 天倒计时，不会累加时长；重要日志和附件应先下载。若已经过期，先签到，再进入 **Get Hot News → Enable workflow** 恢复，并手动运行一次。官方签到逻辑保持不变。

## 8. 调整关键词

编辑 `config/frequency_words.txt`，保留空行分组和组别名。普通词为 OR，`+` 必须词全部满足，`/正则/` 忽略大小写，`@N` 限制该组展示数量。全局噪音规则放在 `[GLOBAL_FILTER]`，主题放在 `[WORD_GROUPS]`。当前源码把所有 `!词` 全局应用，避免用 `!明星` 等宽泛词误杀重大事件。优先级由组顺序控制。

筛选只看标题；不能强制多平台同时上榜、验证真伪、还原完整故事线或合并同一事件的不同标题。`rank_threshold` 只影响强调和权重，不是“只推前 N 名”的过滤开关。

## 9. 以后开启 AI

先决定需要筛选、分析或翻译中的哪项，再将 `AI_API_KEY` 加入 GitHub Secrets，配置 `ai.model` 等官方字段。按需调整 `filter.method`、`ai_analysis.enabled`、`ai_translation.enabled`，并同步修改工作流里固定为 `false` 的 `AI_*_ENABLED` 和 timeline 的 `filter_method` / `analyze`。分析展示还需开启 `display.regions.ai_analysis`。未明确开启前不调用 AI；启用后可能产生所选服务商的费用。

## 10. 升级 TrendRadar

本仓库按模板方式导入官方源码，并保留你原来的初始提交，与官方仓库没有共同的提交历史。升级前先提交备份这四个配置及本指南，再获取官方新版，按文件比较迁入源码和配置变化；不要直接 `git pull upstream master` 或覆盖自定义配置。保留 Secret 引用、当日状态缓存和自定义时段。升级后重新运行 `--doctor`，再手动执行一次 Actions 验收；以新版本注释、迁移说明和代码为准。
