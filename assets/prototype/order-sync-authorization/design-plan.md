# Design Plan — 私域-公域订单同步授权与绑定 v1.0

## 1. Subject

- 产品：品牌私域小程序（微信小程序）订单同步授权与绑定模块
- 受众：在公域直播间/平台下单、跳转私域小程序的消费者（会员）
- 本文件/首批页面要完成的单一任务：在不打断购买主流程的前提下，完成「订单同步授权 + 公域/私域绑定」的决策、状态展示与管理
- 假设与待确认：
  - 小程序环境，画布 375×812，微信小程序信息密度
  - 品牌主色待定，默认 refined-consumer 橙 `#FF6B35`
  - PRD 5.3 未写完（常驻模块字段缺失），按 4.1/5.2 推断补齐
  - 登录方式默认「微信一键登录 + 手机号/验证码兜底」，待确认

## 2. Tone

- surface: c_end
- 气质：refined-consumer
- 参考气质：微信/支付宝小程序信息架构密度，克制消费 App（DESIGN.md C 端允许列表）

## 3. Token

### Color（复制自 design-c-end.md，主色为默认值待品牌确认）

- primary: `#FF6B35`（品牌定后可替换，须过 Anti-Slop）
- page_bg: `#F7F8FA`
- surface: `#FFFFFF`
- border: `#EBEDF0`
- text_primary: `#1A1A1A`
- text_secondary: `#666666`
- text_tertiary: `#999999`
- accent_positive: `#00B578`（已授权/已同步）
- warning: `#FF8F1F`（待确认/绑定不一致）
- danger: `#FA5151`（撤回）
- 弹窗遮罩: `rgba(0,0,0,0.45)`

### Typography

- body: PingFang SC（中文正文；数据列/账号 ID 用 SF Mono）
- hero: 24/600/32 — 仅登录页主标题与弹窗标题级使用
- title: 18/600/26
- body: 15/400/24
- caption: 12/400/18

### Spacing / Radius

- spacing_allowed: [4, 8, 12, 16, 24, 32, 48]
- radius: card 12 / image 8 / button_capsule 22
- shadow: `0 2px 12px rgba(0,0,0,0.06)`（仅卡片；弹窗遮罩不含阴影）

## 4. Layout（每页一节）

### product-detail — 商品详情

- 页型: c_detail
- ASCII:
```text
┌──────────────────────┐
│ ‹ 返回    商品标题    ··· │  topbar 44
├──────────────────────┤
│  商品主图（占 320×240）│  media_header
├──────────────────────┤
│ 商品名称/卖点         │  summary_strip
│ ¥149  ｜ 已售 2.3k    │
├──────────────────────┤
│ 规格                 │  info_sections
│ 配送：48h 发货        │
│ 服务：7 天无理由      │
│ （不展示同步弹窗）     │
├──────────────────────┤
│ [加入购物车]  [立即购买]│  bottom_cta 52（含 safe area）
└──────────────────────┘
```
- regions 列表：topbar / media_header / summary_strip / info_sections / bottom_cta

### member-login — 会员登录

- 页型: c_form
- ASCII:
```text
┌──────────────────────┐
│ ‹ 返回      登录      │  topbar 44
├──────────────────────┤
│       欢迎回来        │  hero 标题（仅本页）
├──────────────────────┤
│ 手机号 [____________] │  form_section
│ 验证码 [______] [获取]│
│ [微信一键登录]        │  （secondary）
│ ☐ 同意《用户协议》     │
├──────────────────────┤
│      [ 登录并继续 ]    │  bottom_cta 52
└──────────────────────┘
```
- regions 列表：topbar / form_section / bottom_cta

### popup-bind-auth — 授权与绑定弹窗（场景A）

- 页型: c_form overlay（无 shell，遮罩 + 居中卡片）
- ASCII:
```text
┌──────────────────────┐
│ ░░ 页面遮罩 rgba(0,0,0,.45) ░░│
│  ┌──────────────────┐ │
│  │ 同步订单需要您的授权 │ │  title
│  │ 为了同步订单，我们需 │ │  body
│  │ 要：1. 绑定公域账号  │ │
│  │ 2. 授权同步订单信息  │ │
│  │ ☐ 我已阅读并同意    │ │
│  │   《订单同步授权协议》│ │
│  │ [  暂不  ] [同意并绑定]│ │  bottom_cta（1 primary）
│  └──────────────────┘ │
└──────────────────────┘
```
- regions 列表：dialog_backdrop / form_section / bottom_cta

### popup-rebind-check — 重新绑定确认弹窗（场景C）

- 页型: c_form overlay（无 shell）
- ASCII:
```text
┌──────────────────────┐
│ ░░ 页面遮罩 rgba(0,0,0,.45) ░░│
│  ┌──────────────────┐ │
│  │ 检测到当前公域账号  │ │  title
│  │ 与已绑定账号不一致  │ │
│  │ 当前登录：公域账号B │ │  对照行
│  │ 已绑定：公域账号A   │ │
│  │ 更新后订单将同步至B  │ │  caption
│  │ [保持原绑定][确认重新绑定]│ │  bottom_cta（1 primary）
│  └──────────────────┘ │
└──────────────────────┘
```
- regions 列表：dialog_backdrop / form_section / bottom_cta

### order-confirm — 订单确认

- 页型: c_form
- ASCII:
```text
┌──────────────────────┐
│ ‹ 返回      确认订单   │  topbar 44
├──────────────────────┤
│ 公域账号B ↔ 会员138****8888│  sync_status_bar
│ [●已授权同步] ＞ 管理    │  （Signature，3 态）
├──────────────────────┤
│ 商品卡片 ×2           │  form_section
│ ─────────────────    │
│ 商品小计 / 运费 / 合计  │
│ 备注 [______________] │
├──────────────────────┤
│      [ 提交订单 ]      │  bottom_cta 52
└──────────────────────┘
```
- regions 列表：topbar / sync_status_bar / form_section / bottom_cta

### auth-bind-manage — 授权与绑定管理

- 页型: c_profile
- ASCII:
```text
┌──────────────────────┐
│ ‹ 返回   授权与绑定    │  topbar 44
├──────────────────────┤
│ (头像) 会员 138****8888│  profile_header
├──────────────────────┤
│ 当前绑定：公域账号A    │  summary_strip（绑定卡片）
│ [● 已授权同步]        │
├──────────────────────┤
│ 订单同步授权  [开]     │  menu_list
│ 授权协议（v2.0）      │
│ 重新绑定              │
├──────────────────────┤
│      撤回绑定与授权    │  danger_zone（danger text）
└──────────────────────┘
```
- regions 列表：topbar / profile_header / summary_strip / menu_list / danger_zone

## 5. Signature

- 元素：「同步链路条」——一条双端对照的绑定状态行：`公域账号 B ↔ 会员 138****8888` + 状态徽标（已授权同步/未授权同步/待确认），带管理入口
- 落地规则：只出现在订单确认页 `sync_status_bar` 与管理页绑定卡片；弹窗不重复出现（弹窗用大标题+说明承载决策）。每屏 1 条，无装饰性图标动画，状态同时用文案+颜色（满足 a11y）

## 6. Differentiation

- 相对 AI 默认电商下单页（商品卡堆叠 + 营销横幅 + 空泛提示）：本方案把「授权/绑定」做成一条克制的信任状态层——主流程零弹窗打扰，决策只在登录后的单一弹窗出现，状态常驻 1 处且有管理入口，视觉密度对齐微信小程序而非营销落地页

---

## Anti-Slop Review

- 日期: 2026-08-31
- surface: c_end
- 命中项：无
- 修订：无
- 结论: PASS

### 勾选明细

**A. frontend-design 通用**
- [x] A1 主正文字体为 PingFang SC，无 Inter/Roboto/Arial/system-ui/Space Grotesk
- [x] A2 主色 `#FF6B35`，无 `#6366F1`、无紫蓝渐变
- [x] A3 未命中 AI 三套默认皮（非奶油纸衬线、非近黑+酸绿、非报纸密排）
- [x] A4 Layout/Signature 非「换 brief 仍一样」——Signature 服务于双账号对照与同步状态，Layout 由 PRD 旅程推导
- [x] A5 Signature 非纯装饰——同步链路条承载账号/会员/状态的功能信息
- [x] A6 Differentiation 一句话说清与 AI 默认的差异

**C. C 端追加**
- [x] C1 无 B 端侧栏/顶栏套移动端（子页仅返回顶栏）
- [x] C2 单屏主任务清晰（浏览/登录/授权决策/提交订单/管理）
- [x] C3 主 CTA ≤ 1（详情页底栏仅「立即购买」1 个 primary；弹窗各 1 个 primary；提交订单 1 个 primary）
- [x] C4 无 marketing 三列 feature + 全屏 hero
- [x] C5 画布 375×812（小程序），非 1440 拉满
