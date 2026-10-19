# 电商客服智能平台 · 前端交互原型

> 依据《开发版 PRD v1.0》生成。骨架沿用仓库既有 `proto-gen` 规范（macOS 窗口外壳 + 左侧 TOC + 右侧 PRD 旁注），主题为默认 violet，可整体换肤。

## 1. 交付物

| 文件 | 页面 | section |
|---|---|---|
| `index.html` | 原型索引（含 PRD 对照表） | — |
| `01-agent-workbench.html` | 坐席 Chat 工作台（P0，**可点击交互**） | 工作台-01 主页面 · AI 助手 / -02 知识 Tab · 检索命中 / -03 顾客·订单 Tab · 接口超时 / -04 插入草稿冲突 / -05 转人工确认 |
| `02-iq-handoff.html` | IQ 会话与转人工上下文（P0） | IQ-01 会话回放与放行审计 / IQ-02 转人工交接摘要 |
| `03-knowledge-center.html` | 知识中心（P0） | 知识中心-01 全局搜索与结果 / -02 知识详情 |
| `04-knowledge-admin.html` | 知识管理（P0） | 知识管理-01 知识列表 / -02 上传解析与字段映射 / -03 发布前校验 |
| `05-monitor.html` | 监控与优化（P0/P1） | 监控-01 首屏看板 / -02 优化任务中心 |
| `06-config.html` | 配置中心（治理） | 配置-01 意图与转接策略 / -02 AI 质量与灰度 |

每个 section 内**恰好一个** macOS 窗口外壳 + **恰好一个**「功能概览」PRD 旁注面板；`toc-sidebar` 全文件唯一。

## 2. 与 PRD 的对应关系

| 原型 | PRD 章节 | 关键约束在原型中的体现 |
|---|---|---|
| 01 工作台 | §4.1–§4.7 | 三栏布局与响应式折叠；AI 草稿插入不自动发送、不弹窗（空框填入、有内容时光标处插入）；发送防重复；异常态均给出可执行下一步 |
| 02 IQ 会话 | §5.2 / §5.4 / §5.5 / §8.1 | 每条对客答复展示合规来源与版本（Answer Gate）；每轮展示路由、槽位、风险标记；交接摘要字段固定且完整 |
| 03 知识中心 | §3.1 / §7.3 | 先权限过滤再检索与生成摘要；摘要只概括检索证据并带引用；失效 / 撤回内容不作可引用答案 |
| 04 知识管理 | §7.1 / §7.2 | 列表按 Notice / Knowledge / 商品信息 分类展示；内容状态机与索引同步状态分开呈现；解析后先预览与告警；发布配置含渠道 + 用户组 + 生效时间，发布前逐项校验并区分编辑 / 发布职责 |
| 03 / 04 分工 | §3.1 / §7.3 | 知识中心只负责「查阅与引用」（单栏、检索范围收成一行小字）；知识缺口汇总（搜索 / 无结果次数、影响会话量、建议动作、分派）集中在知识管理呈现 |
| 05 监控与优化 | §1.4 / §10 | 承接率 ≠ 解决率；过期命中 / 冲突命中使用警示色；自动诊断只出候选动作，不自动改配置 |
| 06 配置中心 | §11 / §12 | 「配置项 + 编辑与发布」两段式；AI 策略按 **IQ / 人工坐席** 分区，业务可调仅 Prompt 内容与安全降级策略，模型 / 索引 / 工作流版本只读；灰度、二次确认与操作审计 |

## 3. 哪些是真交互（其余为静态状态）

`01-agent-workbench.html` 的 section-01 可实际点击：

- 切换会话卡：更新会话头部并**按会话恢复未发送草稿**（对应验收标准「切换会话后草稿不丢失」）
- 右侧面板四模块切换（会话摘要 / 知识搜索 / AI 助手 / 顾客·订单）：同一时间只渲染一个模块；点「搜知识」跳知识搜索、点「AI 回复」跳 AI 助手、点会话头部的订单 / 商品 / 会员 Chip 跳顾客·订单；面板可折叠 / 展开
- 会话摘要的**时间范围切换**（过去 48 小时 / 过去 7 天）：实时替换结论式诉求、情绪与期望、关键事实、已答复、仍缺什么与顾客原话要点
- 「AI 回复 / 重新生成」：骨架屏 → 草稿卡，可取消
- 「插入输入框」：空输入框直接填入，已有内容时在光标处插入，不弹窗、不询问替换 / 追加
- 「润色」：输入框为空时给出提示，有内容时在输入区内联展开差异预览与事实一致性校验
- 「发送」：发送中禁用防重复 → 追加坐席消息 → 清空输入框
- 「转人工 / 升级」：在右侧面板内联展开转接表单（原因 / 队列 / 优先级 / 可编辑交接摘要），对话区不中断
- 转接确认后：对话区只追加一行系统事件；转接状态 / 队列 / 幂等键等运营字段不进入坐席侧摘要（在 IQ 交接摘要页按权限查看）

> **无模态弹窗**：工作台页面 0 弹窗、0 遮罩。润色预览、转人工表单内联在输入区或右侧面板；反馈使用非阻断轻提示。知识管理页的发布前校验改用子页呈现（同样不弹窗）。

## 4. 新增组件类（沉淀候选，未写入 shared.css）

按 `proto-gen` 规范，是否沉淀到 `assets/shared.css` 由人确认后再落笔。本轮新增：

**会话台套件（`01` 引入，建议整体沉淀）**：`wb` / `wb--collapsed` / `wb-queue` / `wb-queue__head` / `wb-queue__list` / `wb-queue__foot` / `wb-search` / `wb-tabs` / `qc`（+ `__row` `__name` `__id` `__preview` `__meta` `__unread`） / `wb-chat` / `wb-chat__head` / `wb-chat__title` / `chat-block` / `chat-sub`（+ `meta-sep`） / `chat-chips` / `ent-chip` / `wb-msgs` / `msg`（`--customer` / `--agent` / `--iq` + `__avatar` `__meta` `__bubble`，气泡限宽 520px） / `cite-chip` / `sysmsg` / `flow-card` / `flow-step`（`done` / `now`） / `wb-composer` / `wb-tool` / `wb-input` / `wb-hint` / `wb-panel`（`__head` / `__body`） / `wb-toast` / `inline-panel`（润色预览） / `diff-old` / `diff-new`

**面板与卡片套件（`01` 引入，`02`–`06` 重复出现 ≥3 次 → 母版候选）**：`pcard`（`__head` / `__title` / `__body` / `__actions`） / `prow` / `sum-lead`（+ `__text`） / `sum-chips` / `sum-block__title` / `sum-quote`（+ `__t`） / `link-mini` / `filter-chip` / `skel`

**卡片权重与信息层级（`04` / `06` 复用 → 建议沉淀）**：`card--focus`（当前页主区域：主色描边 + 浅色卡头） / `card--muted`（次级信息：浅底） / `stat-strip`（指标压成一行细条） / `strip`（`<details>` 折叠细条，收纳缺口明细、操作审计、变更记录） / `alert-row--sm` / `cfg-row--sm`

**内联交互套件（`01` 新增，替代弹窗）**：`inline-bar`（输入区上方的一行确认条） / `inline-panel`（+ `__head`） / `inline-actions`；`ai-sum`（+ `__head` / `__text` / `__cites` / `__note` / `__actions`）与 `cite-num`（知识搜索模块的带引用 AI 总结）；`scope-note`（`03` 检索范围与缺口去向的一行小字说明，把非核心信息压到最低视觉层级 → 建议沉淀）；`04` 使用 `channel-subpage`（+ `__head`） / `subpage-close-x` 作为子页容器（类名沿用 `proto-gen` 的 subpage 约定，样式目前只在本页定义，建议一并沉淀）

> **转接信息的可见性**：坐席侧既不在对话区渲染转接卡片，也不在会话摘要里露出转接状态、目标队列、排队情况、上下文完整度与幂等键；这些运营字段归属 IQ 交接摘要页（按权限）。坐席只需通过对话区一行系统事件感知转接事实（PRD §4.5 / §4.6 / §6.3 已同步更新）。

**其他页面局部**：`kres`（`__title` / `__excerpt` / `__foot`） / `ord-card` / `prod`（`__img`） / `mark.hl`（`01`、`03`）；`iq-wrap` / `decide` / `kv` / `audit-row`（`02`）；`kc-wrap` / `search-hero` / `ai-answer` / `doc-body` / `ver-row`（`03`）；`ka-toolbar` / `ka-table` / `drop-zone` / `fieldmap-row` / `alert-row`（`04`）；`mon-block` / `drill-bar` / `drill-chip`（`05`）；`cfg-row` / `cfg-input` / `ver-bar`（`06`）

> 建议优先把「会话台套件」与「pcard / prow / link-mini」两组沉淀进 `shared.css`（已跨 6 个文件重复），其余保持页面内私有。

## 5. 评审自查

**P1 组件复用**：所有按钮 / 徽标 / 输入 / 开关 / 弹窗 / 侧栏 / 顶栏均使用 `shared.css` 既有类（`.btn-*`、`.badge-*`、`.input`、`.form-*`、`.toggle-switch`、`.form-dialog`、`.sidebar-item`、`.win-chrome-*`、`.kpi-*`、`.dist-*`、`.bar-*`、`.usage-detail-table`）。页面自有样式只放本页布局，未复制通用组件样式。

**P2 与既有原型一致性**：基准取同仓库最近一次产出 `assets/prototype/order-sync-authorization/`（同骨架、同主题、同样带三对 `@proto-gen` 标记与 `data-toast` / `data-goto` 演示约定）。有意偏离一处：本套原型把侧边栏导航项写成 `<a class="sidebar-item">` 以支持跨页面跳转，基准为 `<div>`。

**P3 结构契约与注入**：每个 `.proto-with-prd` 恰一外壳 + 恰一 PRD 面板；TOC 与 section 一一对应；三对标记块均已由 `inject-assets.mjs` 回填，无空块；文件自包含可直接双击打开。

**P4 PRD 面板**：标题统一「功能概览」；条目按视觉从上到下排列、以加粗元素名开头、一条一个可感知元素；引用类名而非具体色值；重复出现的通用外壳在首个 section 描述后不再复述。

## 6. 交付研发时请剥离

`data-comp` / `data-target` 属性、`prd-highlight.js` 注入内容、`shared.css` 末尾「PRD ↔ 原型 双向高亮联动」段（4 条规则）以及各页 `data-toast` / `data-goto` / `data-confirm` 演示脚本，均属**评审与演示脚手架**，不属于业务逻辑。

```bash
# 输出应为空
grep -nE "data-(comp|target)|prd-highlight|is-highlight|is-bullet-active" your-prod-code/
```

## 7. 重新生成样式

```bash
node .agent/skills/proto-gen/assets/inject-assets.mjs assets/prototype/service-platform/
```

命令幂等，可反复执行；页面自有样式写在标记块之外，不会被覆盖。
