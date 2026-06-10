# vul-labs（漏洞盒子）UI 设计规范

**Status**: Draft v0.1（第一版，面向 G1 评审）
**Author**: UI 设计师
**Last Updated**: 2026-06-09
**上游输入**: [PRD](../product/PRD.md) · [用户故事](../product/user-stories.md) · [UX 架构](./UX-architecture.md) · [工作流](../WORKFLOW.md)
**下游交接**: 前端开发者（Design Token → CSS 变量 / 组件实现）

> **本文档目标**：把 [UX 架构](./UX-architecture.md) 的信息架构、页面框架与状态设计，落地为**可直接实现的视觉系统**——设计语言、Design Token（CSS 变量）、组件库、关键页面高保真描述、视觉编码规范、响应式与主题方案。
> **追溯原则**：所有组件/页面/状态都回链到 UX 章节（§x）与用户故事（US-xxx），与 PRD、UX 双向可追溯（见 §8）。
> **落地原则**：所有 Token 以**深色为基准**、以 **CSS 自定义属性**给出可直接复制的值；语义变量命名与 UX §5.3 约定一致（`--bg-*` / `--text-*` / `--state-*` / `--instance-*`），前端**禁止硬编码色值**。
> **无障碍原则**：所有正文/重要文本配色满足 **WCAG 2.1 AA**（普通文本 ≥ 4.5:1，大字/图形 ≥ 3:1）；状态**绝不只靠颜色**，统一采用 **图标 + 文字 + 颜色** 三重编码（色盲友好，见 §5）。

---

## 0. 设计决策摘要（与 UX 决策对齐）

| # | UI 决策 | 理由 | 关联 |
|---|---------|------|------|
| U1 | **深色优先（dark-first）**，深色为设计基准，浅色为完整对照主题，支持「深/浅/跟随系统」三态 | 安全工具用户习惯深色（PRD §7）；UX D1 | UX D1 / §5.3 |
| U2 | **专业克制的"沙箱终端"视觉语言**：深蓝灰中性底 + **单一冷调品牌强调色（青色 cyan）** + 等宽字体承载技术内容 | 技术/安全感但不喧闹；强调色稀缺使用以突出关键动作 | PRD §7 |
| U3 | **语义色与"实例状态色""难度色"分轨管理**，全部叠加图标+文字 | 三套色系都会用到红/黄/绿，必须靠图标+文字消歧并满足色盲友好 | UX §6 / §4 |
| U4 | **品牌强调色 = 青色**，刻意**避开**语义绿（成功）/蓝（信息），防止"主操作"与"成功/信息"混淆 | 颜色语义不串台 | U3 |
| U5 | **实例状态机是视觉一等公民**：统一的「状态徽章 + 状态点 + 全局状态条」三处呈现同一状态语言 | UX D3：状态贯穿详情页与全局横幅 | UX §3.1/§4.2 |
| U6 | **等宽字体专用于**：flag 输入、访问地址、倒计时、端口/代码片段 | 强化"可复制的技术值"语义，降低 O/0、l/1 误读 | US-021/030 |
| U7 | **异常态/加载态与正常态同优先级出设计**（骨架屏、空态、错误卡、限流态） | UX D7：异常态先行 | UX §4 |

---

## 1. 设计语言 / 风格定位

### 1.1 关键词
**深色优先 · 沙箱终端感 · 专业克制 · 高对比可读 · 状态清晰**

vul-labs 的界面要让用户感觉自己进入一个"**可信、隔离、可控的攻防工作台**"，而非花哨的消费级产品：

- **基调**：深蓝灰中性背景（非纯黑，降低长时间练习的眩光与边缘割裂），层级用"明度抬升 + 细边框"表达，而非重阴影。
- **强调克制**：青色品牌色只用于**主操作、焦点、关键链接**，全局出现频率低 → 重要动作（启动环境 / 提交 flag）一眼可辨。
- **技术质感**：等宽字体承载 flag、访问地址、倒计时、端口；卡片/面板带极细 1px 边框，呼应"终端/控制台"观感。
- **安全语义优先**：合规横幅、实例状态、超时倒计时等"风险相关信息"在视觉权重上优先，绝不被弱化（PRD §8.3）。
- **稳重而非冰冷**：圆角中等（6–12px）、动效短促克制（150–300ms），通关时给一次"恰到好处"的庆祝反馈（US-030/031），其余场景不打扰。

### 1.2 反例（明确不做）
- ❌ 满屏霓虹/黑客电影式绿字闪烁（与"专业克制"冲突，且绿被占用为语义色）。
- ❌ 重投影、毛玻璃堆叠的拟物风（增加渲染成本，违背 PRD §7 性能）。
- ❌ 仅用颜色区分状态（违背无障碍）。
- ❌ 强调色泛滥导致主操作淹没。

---

## 2. 设计令牌（Design Tokens）

> 全部 Token 以 CSS 自定义属性给出。**深色为 `:root` 默认**；浅色在 `[data-theme="light"]` 覆盖（§6）。语义层（`--bg-*`/`--text-*`/`--state-*`/`--instance-*`/`--difficulty-*`）引用原始色阶层（`--c-*`），前端只消费**语义层**。
> 对比度标注基于深色基准底色 `#0B0F17`（正文）/ 对应表面色，均经核验满足 AA。

### 2.1 原始色阶（Primitive Palette）

```css
:root {
  /* —— 中性 / 石板蓝灰（界面骨架） —— */
  --c-slate-950: #0B0F17;  /* 应用最底层背景 */
  --c-slate-900: #111827;  /* 卡片/面板表面 */
  --c-slate-850: #161E2E;  /* 输入/内嵌区 */
  --c-slate-800: #1E293B;  /* 悬浮层/模态 */
  --c-slate-700: #2A3647;  /* 边框-强 / 分隔 */
  --c-slate-600: #3A475A;  /* 边框/禁用描边 */
  --c-slate-500: #64748B;  /* 次要图标/占位 */
  --c-slate-400: #8B98AC;  /* 三级文本 */
  --c-slate-300: #AFBACB;  /* 次要文本 */
  --c-slate-200: #CBD3DF;  /* 文本(浅底) */
  --c-slate-100: #E6EDF3;  /* 主文本(深底) */
  --c-white:     #FFFFFF;

  /* —— 品牌强调色 / 青（cyan，仅用于主操作/焦点/关键链接） —— */
  --c-cyan-300:  #67E8F9;
  --c-cyan-400:  #22D3EE;  /* 深底上的品牌前景文本/图标 */
  --c-cyan-500:  #06B6D4;  /* 主按钮填充 */
  --c-cyan-600:  #0E91AE;  /* 主按钮 hover */
  --c-cyan-700:  #0C7186;  /* 主按钮 active */

  /* —— 语义：成功 / 绿 —— */
  --c-green-300: #86EFAC;
  --c-green-400: #4ADE80;  /* 深底前景 */
  --c-green-500: #22C55E;
  --c-green-600: #16A34A;  /* 填充(配深色文本) */

  /* —— 语义：警告 / 琥珀 —— */
  --c-amber-300: #FCD34D;
  --c-amber-400: #FBBF24;  /* 深底前景 */
  --c-amber-500: #F59E0B;
  --c-amber-600: #D97706;

  /* —— 语义：危险 / 红 —— */
  --c-red-300:   #FCA5A5;
  --c-red-400:   #F87171;  /* 深底前景 */
  --c-red-500:   #EF4444;
  --c-red-600:   #DC2626;  /* 填充(配白字) */

  /* —— 语义：信息 / 蓝（与品牌青刻意区分） —— */
  --c-blue-300:  #93C5FD;
  --c-blue-400:  #60A5FA;  /* 深底前景 */
  --c-blue-500:  #3B82F6;
  --c-blue-600:  #2563EB;

  /* —— 漏洞类型辅助色（仅做类别识别，弱饱和小面积使用） —— */
  --c-violet-400: #A78BFA; /* XSS */
  --c-sky-400:    #38BDF8; /* 文件上传 */
  --c-teal-400:   #2DD4BF; /* SSRF (P1) */
  --c-rose-400:   #FB7185; /* 命令注入 (P1) */

  /* —— 通关高光 / 金（仅通关勋章点缀） —— */
  --c-gold-400:  #FBBF24;
}
```

### 2.2 语义色 —— 深色基准（`:root`）

```css
:root {
  /* 背景层级（明度抬升表达层级） */
  --bg-base:      var(--c-slate-950); /* 页面底 */
  --bg-surface:   var(--c-slate-900); /* 卡片/面板 */
  --bg-inset:     var(--c-slate-850); /* 输入框/代码块/内嵌 */
  --bg-elevated:  var(--c-slate-800); /* 模态/弹层/下拉 */
  --bg-hover:     #1B2536;            /* 行/卡片 hover 底 */
  --bg-active:    #222E42;            /* 选中/激活底 */
  --bg-overlay:   rgba(3, 6, 12, 0.72); /* 模态遮罩 */

  /* 边框 / 分隔 */
  --border-subtle:  var(--c-slate-700); /* 默认细边框 */
  --border-strong:  var(--c-slate-600); /* 强调/输入边框 */
  --border-focus:   var(--c-cyan-400);  /* 焦点环 */

  /* 文本（深底） */
  --text-primary:   var(--c-slate-100); /* 14.2:1 ✓ AAA */
  --text-secondary: var(--c-slate-300); /*  9.0:1 ✓ AA  */
  --text-tertiary:  var(--c-slate-400); /*  5.6:1 ✓ AA  */
  --text-disabled:  var(--c-slate-500); /*  仅非关键/占位 */
  --text-inverse:   var(--c-slate-950); /* 用于浅色填充上的文字 */
  --text-link:      var(--c-cyan-400);  /*  8.4:1 ✓ AA  */
  --text-link-hover:var(--c-cyan-300);

  /* 品牌（主操作） */
  --brand:          var(--c-cyan-500);
  --brand-hover:    var(--c-cyan-600);
  --brand-active:   var(--c-cyan-700);
  --brand-fg:       var(--c-cyan-400); /* 深底上的品牌文字/图标 */
  --on-brand:       #04222B;           /* 青色填充上的文字(深) 对比 ≥ 7:1 ✓ */
  --brand-subtle-bg:rgba(34, 211, 238, 0.12); /* 品牌弱底(标签/选中) */

  /* —— 语义状态（前景=深底文本/图标；solid=填充；subtle=弱底） —— */
  --state-success-fg:    var(--c-green-400);   /* 文本/图标 8.6:1 ✓ */
  --state-success-solid: var(--c-green-600);   /* 填充(配 --on-solid-dark) */
  --state-success-bg:    rgba(34, 197, 94, 0.14);
  --state-success-border:rgba(34, 197, 94, 0.40);

  --state-warning-fg:    var(--c-amber-400);   /* 11.1:1 ✓ */
  --state-warning-solid: var(--c-amber-500);
  --state-warning-bg:    rgba(245, 158, 11, 0.14);
  --state-warning-border:rgba(245, 158, 11, 0.42);

  --state-danger-fg:     var(--c-red-400);     /* 6.1:1 ✓ */
  --state-danger-solid:  var(--c-red-600);     /* 配白字 4.5:1 ✓ */
  --state-danger-bg:     rgba(239, 68, 68, 0.14);
  --state-danger-border: rgba(239, 68, 68, 0.42);

  --state-info-fg:       var(--c-blue-400);    /* 7.0:1 ✓ */
  --state-info-solid:    var(--c-blue-600);
  --state-info-bg:       rgba(59, 130, 246, 0.14);
  --state-info-border:   rgba(59, 130, 246, 0.42);

  --on-solid-dark:  #06210F;  /* 绿/琥珀等浅填充上的深色文字 */
  --on-solid-light: #FFFFFF;  /* 红/蓝等深填充上的白色文字 */

  /* —— 实例状态机专用色（US-020/021/022，UX §4.2） —— */
  --instance-unstarted:  var(--c-slate-400);  /* 未启动 · 灰 */
  --instance-provisioning:var(--c-blue-400);  /* 启动中 · 蓝(脉冲) */
  --instance-ready:      var(--c-green-400);  /* 已就绪 · 绿 */
  --instance-failed:     var(--c-red-400);    /* 启动失败 · 红 */
  --instance-expiring:   var(--c-amber-400);  /* 临近超时 · 琥珀 */
  --instance-destroyed:  var(--c-slate-500);  /* 已销毁 · 暗灰 */
  --instance-passed:     var(--c-green-400);  /* 已通关 · 绿(配奖杯+金点缀) */

  /* —— 难度色（UX §3.4，叠加信号条图标） —— */
  --difficulty-easy:   var(--c-green-400);  /* 入门 */
  --difficulty-medium: var(--c-amber-400);  /* 中等 */
  --difficulty-hard:   var(--c-red-400);    /* 困难 */
}
```

### 2.3 字体（Typography）

```css
:root {
  /* 字体族：中文优先回退 + 无衬线；技术值用等宽（U6） */
  --font-sans: "Inter", "PingFang SC", "Microsoft YaHei",
               "Noto Sans SC", system-ui, -apple-system, sans-serif;
  --font-mono: "JetBrains Mono", "Fira Code", "Cascadia Code",
               ui-monospace, "SFMono-Regular", Consolas, monospace;

  /* 字号阶梯（1rem = 16px，4px 节律） */
  --fs-xs:   0.75rem;   /* 12px · 徽章/辅助/表注 */
  --fs-sm:   0.8125rem; /* 13px · 次要正文/标签 */
  --fs-base: 0.875rem;  /* 14px · 正文默认(界面密度优先) */
  --fs-md:   1rem;      /* 16px · 强调正文/输入值 */
  --fs-lg:   1.125rem;  /* 18px · 卡片标题/区块标题 */
  --fs-xl:   1.25rem;   /* 20px · 页面次级标题 */
  --fs-2xl:  1.5rem;    /* 24px · 页面主标题 */
  --fs-3xl:  1.875rem;  /* 30px · 大数字(总分/统计) */
  --fs-4xl:  2.25rem;   /* 36px · 营销/空态主视觉 */

  /* 字重 */
  --fw-regular:  400;
  --fw-medium:   500;
  --fw-semibold: 600;
  --fw-bold:     700;

  /* 行高 */
  --lh-tight:   1.25; /* 标题/大数字 */
  --lh-snug:    1.4;  /* UI 文本 */
  --lh-normal:  1.6;  /* 正文/任务说明长文 */

  /* 字间距 */
  --ls-tight: -0.01em; /* 大标题 */
  --ls-wide:   0.02em; /* 全大写小标签/徽章 */
}
```

**排版用法约定**

| 场景 | 字号 | 字重 | 行高 | 字体 |
|------|------|------|------|------|
| 页面主标题 (h1) | `--fs-2xl` | semibold | tight | sans |
| 区块标题 (h2/h3) | `--fs-lg`/`--fs-md` | semibold | snug | sans |
| 正文 / 任务说明 | `--fs-base`～`--fs-md` | regular | normal | sans |
| 辅助 / 表注 / 徽章 | `--fs-xs`～`--fs-sm` | medium | snug | sans |
| **flag 输入 / 访问地址 / 端口** | `--fs-md` | medium | snug | **mono** |
| **倒计时 / 大数字统计** | `--fs-xl`～`--fs-3xl` | semibold | tight | **mono** |

### 2.4 间距（Spacing · 4px 基准节律）

```css
:root {
  --space-0:  0;
  --space-1:  0.25rem; /*  4px */
  --space-2:  0.5rem;  /*  8px */
  --space-3:  0.75rem; /* 12px */
  --space-4:  1rem;    /* 16px */
  --space-5:  1.25rem; /* 20px */
  --space-6:  1.5rem;  /* 24px */
  --space-8:  2rem;    /* 32px */
  --space-10: 2.5rem;  /* 40px */
  --space-12: 3rem;    /* 48px */
  --space-16: 4rem;    /* 64px */

  /* 容器 / 栅格 */
  --container-max: 1280px;
  --page-gutter:   var(--space-6); /* 桌面页面左右留白 */
  --grid-gap:      var(--space-4);
  --card-min:      280px;          /* 卡片网格 minmax 下限(UX §3.4) */
}
```

### 2.5 圆角（Radius）

```css
:root {
  --radius-sm:   4px;   /* 徽章/小标签/输入内小元素 */
  --radius-md:   6px;   /* 按钮/输入框 */
  --radius-lg:   8px;   /* 卡片/面板 */
  --radius-xl:   12px;  /* 大卡片/模态 */
  --radius-pill: 9999px;/* 状态点/胶囊标签/头像 */
}
```

### 2.6 阴影与层级感（Shadow / Elevation）

> 深色界面**以"明度抬升 + 边框"为主、阴影为辅**；强调色焦点用"发光环"。

```css
:root {
  --shadow-xs:  0 1px 2px rgba(0, 0, 0, 0.30);
  --shadow-sm:  0 2px 6px rgba(0, 0, 0, 0.35);
  --shadow-md:  0 6px 16px rgba(0, 0, 0, 0.45);
  --shadow-lg:  0 16px 40px rgba(0, 0, 0, 0.55); /* 模态 */

  /* 焦点环（键盘可达，UX §6） */
  --ring-focus: 0 0 0 3px rgba(34, 211, 238, 0.45);
  /* 危险确认焦点环 */
  --ring-danger: 0 0 0 3px rgba(239, 68, 68, 0.40);
  /* 启动中脉冲光（实例 provisioning） */
  --glow-provisioning: 0 0 0 0 rgba(96, 165, 250, 0.55);
}
```

### 2.7 层级（z-index）

```css
:root {
  --z-base:        0;
  --z-sticky:      100;  /* 详情页右栏 sticky 操作面板 */
  --z-compliance:  200;  /* 合规横幅(常驻顶部) */
  --z-topnav:      300;  /* 顶栏 */
  --z-instancebar: 290;  /* 全局实例状态条(顶栏下方) */
  --z-dropdown:    400;  /* 用户菜单/下拉/筛选抽屉 */
  --z-overlay:     900;  /* 模态遮罩 */
  --z-modal:       1000; /* 模态内容 */
  --z-toast:       1100; /* toast 通知(最高) */
}
```

### 2.8 动效（Motion）

```css
:root {
  --dur-instant: 80ms;
  --dur-fast:    150ms; /* hover/焦点/按钮反馈 */
  --dur-normal:  240ms; /* 卡片/面板/抽屉 */
  --dur-slow:    360ms; /* 模态进出/页面过渡 */
  --dur-celebrate: 700ms; /* 通关计分动画(US-030/031) */

  --ease-standard: cubic-bezier(0.2, 0, 0, 1);     /* 进出通用 */
  --ease-emphasized: cubic-bezier(0.2, 0, 0, 1.2); /* 计分/强调 */
  --ease-exit: cubic-bezier(0.4, 0, 1, 1);
}

/* 尊重用户减少动效偏好（UX §6） */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.001ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.001ms !important;
  }
}
```

**关键动效清单**

| 动效 | 时长 / 曲线 | 用途 |
|------|------------|------|
| 按钮 hover / 焦点 | `--dur-fast` / standard | 全站交互反馈 |
| 卡片 hover 抬升 | `--dur-normal` / standard | 靶场卡片 |
| 模态 / 抽屉进出 | `--dur-slow` / standard | 确认框、筛选抽屉 |
| 实例「启动中」脉冲 | 1.5s 循环 / ease-in-out | 状态点呼吸光（reduced-motion 下降级为静态点 + 文案） |
| 倒计时数字翻动 | `--dur-fast` | 实例剩余时间（每秒，临近超时变色） |
| **通关计分** | `--dur-celebrate` / emphasized | flag 正确：成功卡淡入 + `+100` 上浮 + 勋章点亮（一次性、不循环） |
| flag 错误抖动 | `--dur-fast` ×2 | 输入框横向抖动（reduced-motion 下仅变色+图标） |

---

## 3. 组件库清单

> 约定：每个组件标注**状态（默认 / 悬停 hover / 聚焦 focus / 禁用 disabled / 加载 loading）**与无障碍要点。颜色一律引用 §2 语义 Token。

### 3.1 按钮 Button

**变体**：`primary`（主操作，青色填充）· `secondary`（次操作，描边）· `ghost`（弱操作，无边框）· `danger`（销毁/重置）· `link`（文字链接式）
**尺寸**：`sm`(h28) · `md`(h36，默认) · `lg`(h44，登录/启动等关键 CTA，满足 ≥44px 触控)

| 状态 | primary | secondary | danger |
|------|---------|-----------|--------|
| 默认 | bg `--brand` / text `--on-brand` | bg 透明 / 边框 `--border-strong` / text `--text-primary` | bg `--state-danger-solid` / text `--on-solid-light` |
| 悬停 | bg `--brand-hover` + `--shadow-sm` | bg `--bg-hover` / 边框 `--brand-fg` | bg `--c-red-500` |
| 聚焦 | + `--ring-focus` | + `--ring-focus` | + `--ring-danger` |
| 禁用 | opacity .5 / `cursor:not-allowed` / 去交互 | 同上 | 同上 |
| 加载 | 左置 spinner + 文案保留(如"启动中…") / 禁重复点击 | — | — |

```css
.btn {
  display: inline-flex; align-items: center; justify-content: center;
  gap: var(--space-2);
  height: 36px; padding: 0 var(--space-4);
  font: var(--fw-medium) var(--fs-base)/1 var(--font-sans);
  border-radius: var(--radius-md); border: 1px solid transparent;
  cursor: pointer; user-select: none;
  transition: background var(--dur-fast) var(--ease-standard),
              box-shadow var(--dur-fast) var(--ease-standard),
              border-color var(--dur-fast) var(--ease-standard);
}
.btn:focus-visible { outline: none; box-shadow: var(--ring-focus); }
.btn[disabled], .btn[aria-disabled="true"] { opacity: .5; cursor: not-allowed; pointer-events: none; }

.btn--primary   { background: var(--brand); color: var(--on-brand); }
.btn--primary:hover   { background: var(--brand-hover); box-shadow: var(--shadow-sm); }
.btn--primary:active  { background: var(--brand-active); }

.btn--secondary { background: transparent; color: var(--text-primary); border-color: var(--border-strong); }
.btn--secondary:hover { background: var(--bg-hover); border-color: var(--brand-fg); }

.btn--ghost     { background: transparent; color: var(--text-secondary); }
.btn--ghost:hover { background: var(--bg-hover); color: var(--text-primary); }

.btn--danger    { background: var(--state-danger-solid); color: var(--on-solid-light); }
.btn--danger:hover { background: var(--c-red-500); }
.btn--danger:focus-visible { box-shadow: var(--ring-danger); }

.btn--lg { height: 44px; padding: 0 var(--space-6); font-size: var(--fs-md); }
.btn--block { width: 100%; }
```

### 3.2 输入框 Input / 表单控件

包含：文本输入、密码输入（含 👁 显隐切换）、搜索框、复选框 Checkbox、下拉 Select。
**状态**：默认 / 聚焦（青色边框 + 焦点环）/ 禁用 / 错误（红边框 + 下方错误文案 + `aria-invalid`）/ 只读。

```css
.field-label { display:block; font-size: var(--fs-sm); font-weight: var(--fw-medium);
  color: var(--text-secondary); margin-bottom: var(--space-2); }

.input {
  width: 100%; height: 40px; padding: 0 var(--space-3);
  background: var(--bg-inset); color: var(--text-primary);
  border: 1px solid var(--border-strong); border-radius: var(--radius-md);
  font: var(--fs-base)/1.4 var(--font-sans);
  transition: border-color var(--dur-fast), box-shadow var(--dur-fast);
}
.input::placeholder { color: var(--text-disabled); }
.input:focus { outline: none; border-color: var(--border-focus); box-shadow: var(--ring-focus); }
.input:disabled { opacity:.5; cursor:not-allowed; }
.input[aria-invalid="true"] { border-color: var(--state-danger-fg); }

.input--mono { font-family: var(--font-mono); letter-spacing: .01em; } /* flag/地址(U6) */

.field-error { margin-top: var(--space-2); font-size: var(--fs-sm);
  color: var(--state-danger-fg); display:flex; gap: var(--space-1); align-items:center; }
.field-hint  { margin-top: var(--space-2); font-size: var(--fs-xs); color: var(--text-tertiary); }
```

- **密码强度条**（US-001）：4 段 `▮▮▯▯`，弱→强 = `--state-danger-fg → --state-warning-fg → --state-success-fg`；同时显示文字"弱/中/强"+ 规则提示（"至少 8 位"），不只靠颜色。
- **复选框**（合规勾选 US-001 / 筛选 US-011）：选中态填充 `--brand`、勾 `--on-brand`；焦点环；标签整体可点击；触控 ≥ 44px。

### 3.3 标签 / 徽章 Badge

> 三类专用徽章，**全部图标 + 文字 + 颜色三重编码**（编码规范详见 §5）。

**a) 难度徽章 `Badge--difficulty`**（US-010/012）

| 难度 | 图标(信号条) | 颜色 | 文字 |
|------|------------|------|------|
| 入门 | `▁` 1 格 | `--difficulty-easy` | 入门 |
| 中等 | `▁▄` 2 格 | `--difficulty-medium` | 中等 |
| 困难 | `▁▄█` 3 格 | `--difficulty-hard` | 困难 |

**b) 漏洞类型徽章 `Badge--type`**（US-010/011）：中性胶囊底（`--bg-inset` + `--border-subtle`），仅图标着小面积类型色 + 文字，避免与难度/状态色竞争（U3）。

| 类型 | 图标 | 图标色 |
|------|------|--------|
| SQL 注入 | 数据库 `db` | `--brand-fg`(青) |
| 反射型 XSS | `</>` | `--c-violet-400` |
| 文件上传 | 上传 `↥` | `--c-sky-400` |
| SSRF (P1) | 地球 `globe` | `--c-teal-400` |
| 命令注入 (P1) | 终端 `>_` | `--c-rose-400` |

**c) 实例 / 通关状态徽章 `Badge--status`**（驱动 §4.2 / §5.3）：状态点 + 图标 + 文字，配 `*-bg` 弱底 + `*-border` 描边。

```css
.badge {
  display:inline-flex; align-items:center; gap: var(--space-1);
  height:22px; padding: 0 var(--space-2);
  font: var(--fw-medium) var(--fs-xs)/1 var(--font-sans);
  border-radius: var(--radius-sm); border:1px solid transparent; white-space:nowrap;
}
.badge .badge__icon { width:14px; height:14px; flex:none; }

/* 难度 */
.badge--easy   { color: var(--difficulty-easy);   background: var(--state-success-bg); border-color: var(--state-success-border); }
.badge--medium { color: var(--difficulty-medium); background: var(--state-warning-bg); border-color: var(--state-warning-border); }
.badge--hard   { color: var(--difficulty-hard);   background: var(--state-danger-bg);  border-color: var(--state-danger-border); }

/* 类型(中性胶囊) */
.badge--type   { color: var(--text-secondary); background: var(--bg-inset);
  border-color: var(--border-subtle); border-radius: var(--radius-pill); }

/* 状态弱底徽章见 §4 */
```

### 3.4 卡片 Card / 靶场卡片 LabCard

**靶场卡片**（US-010，UX §3.4）整卡可点击进详情；包含：名称、类型徽章、难度徽章、**我的通关状态**（✅已通关 / ○未开始）、分值。

| 状态 | 表现 |
|------|------|
| 默认 | bg `--bg-surface` + 1px `--border-subtle` + `--radius-lg` |
| 悬停 | 边框 → `--brand-fg`，`translateY(-2px)` + `--shadow-md`，名称变 `--text-link` |
| 聚焦 | `--ring-focus`（整卡为 `<a>`/`role=link`，键盘可达） |
| 已通关 | 右上角 ✅ + 顶部 2px `--state-success-fg` 高光条 |
| 加载 | 用骨架卡（§3.8）占位 |

```css
.card { background: var(--bg-surface); border: 1px solid var(--border-subtle);
  border-radius: var(--radius-lg); padding: var(--space-4);
  transition: transform var(--dur-normal) var(--ease-standard),
              box-shadow var(--dur-normal), border-color var(--dur-normal); }
.lab-card { display:flex; flex-direction:column; gap: var(--space-3); cursor:pointer; position:relative; }
.lab-card:hover { transform: translateY(-2px); box-shadow: var(--shadow-md); border-color: var(--brand-fg); }
.lab-card:focus-visible { outline:none; box-shadow: var(--ring-focus); }
.lab-card--passed::before { content:""; position:absolute; inset:0 0 auto 0; height:2px;
  background: var(--state-success-fg); border-radius: var(--radius-lg) var(--radius-lg) 0 0; }
```

### 3.5 表格 Table

用于个人中心已通关列表（US-040）/ 后台占位（P2）。深底斑马纹用 hover 而非重底色。

| 状态 | 表现 |
|------|------|
| 表头 | bg `--bg-inset`，text `--text-tertiary`，`--ls-wide`，sticky 可选 |
| 行默认 | 底分隔线 `--border-subtle` |
| 行悬停 | bg `--bg-hover` |
| 行聚焦/选中 | bg `--bg-active` + 左 2px `--brand` |
| 空 | 表体内居中空态（§3.8） |
| 加载 | 行级骨架条 |

### 3.6 模态 Modal / 确认对话框

用于：销毁/重置实例二次确认（US-023，UX §6 防误操作）。
- 遮罩 `--bg-overlay`（`--z-overlay`）；内容 `--bg-elevated` + `--shadow-lg` + `--radius-xl`（`--z-modal`）。
- 结构：标题 + 说明 + 操作区（右下：取消 `secondary` + 确认 `danger`）。
- **危险确认**：主按钮用 `--danger`，文案明确（"销毁实例"而非"确定"）。
- 无障碍：`role="dialog"` `aria-modal="true"`；打开聚焦首个可操作元素、焦点陷阱；`Esc` 关闭并归还焦点到触发元素（UX §6）。

### 3.7 Toast 通知

右上角堆叠（`--z-toast`），`--bg-elevated` + 左 3px 语义色条 + 图标 + 文案 + 关闭。
**类型**：success / warning / danger / info（图标 + 颜色 + 文字三重）。
- 用途示例：登录已过期重定向提示（US-003）、复制访问地址成功、实例已销毁。
- 默认 4s 自动消失（错误类需手动关闭或更长）；`aria-live="polite"`（info/success）/ `assertive`（错误）。
- reduced-motion 下取消滑入，仅淡入。

### 3.8 加载骨架 Skeleton / 空态 / 错误态

> UX D7：异常态先行。三者共用一套占位组件语言。

- **骨架屏 Skeleton**（US-010 列表 / 详情加载）：`--bg-inset` 底 + 微光扫过（reduced-motion 下静态）；列表用「骨架靶场卡」，详情用「左栏段落条 + 右栏面板块」骨架。
```css
.skeleton { background: var(--bg-inset); border-radius: var(--radius-sm);
  position:relative; overflow:hidden; }
.skeleton::after { content:""; position:absolute; inset:0;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,.06), transparent);
  transform: translateX(-100%); animation: skeleton-shimmer 1.4s infinite; }
@keyframes skeleton-shimmer { to { transform: translateX(100%); } }
```
- **空态 EmptyState**：居中图标插画 + 主文案 + 副文案 +（可选 CTA）。
  - 列表无数据："暂无靶场"（US-010）。
  - 筛选无结果："无匹配靶场" + `secondary` 按钮「清除筛选」（US-011）。
  - 进度空态：鼓励文案 + `primary` 按钮「去练习 →」（US-040）。
- **错误态 ErrorState**：错误图标(red) + "加载失败，请重试" + `secondary` 按钮「重试」（接口 5xx/网络失败）。

### 3.9 实例控制器 InstanceController（产品核心组件，US-020/021/022）

详情页右栏粘性面板，按 §4.2 状态机切换。包含：状态徽章、主操作按钮、访问地址（mono + 复制 + 新标签打开）、倒计时（mono）、销毁/重置（P1）。详细状态见 §4.2、页面用法见 §4.6。

### 3.10 全局实例状态条 GlobalInstanceBar（UX §3.1，D3）

跨页持久条，**仅当有活跃实例时出现**，位于顶栏下方（`--z-instancebar`）。
- 结构：`🟢 状态点 + 靶场名 + 状态文字 · ⏱倒计时(mono) · (访问) (销毁)`。
- 颜色随实例状态（`--instance-*`）变化；临近超时整条左侧出现 `--state-warning-border` 高亮。
- 移动端简化为一行（状态点 + 倒计时 + 访问图标），UX §5.2。
- `aria-live="polite"` 周期播报剩余时间（避免过频，UX §6）。

### 3.11 Flag 提交器 FlagSubmit（US-030）

详情页右栏、始终可见（UX D5）。等宽输入框（占位 `flag{...}`）+ 提交按钮 + 反馈区。四态见 §4.3。

### 3.12 全局外壳组件

- **合规横幅 ComplianceBanner**（PRD §8.3 / UX D6）：顶部常驻警示条，`--state-warning-bg` 弱底 + ⚠ 图标 + 文案"仅限合法授权环境内学习使用 · 靶场为隔离沙箱" + 可收起（×，记忆偏好，首访默认展开）。`--z-compliance`。
- **顶栏 TopNav**（UX §3.1）：Logo + 一级导航（靶场 / 我的进度，当前页 `aria-current` 高亮：文字 `--text-primary` + 下方 2px `--brand`）+ 主题切换 + 用户菜单（含登出 US-003）。
- **页脚 Footer**：开源协议 / 免责声明 / 文档链接，`--text-tertiary`。

---

## 4. 关键页面高保真描述

> 以下为**桌面（≥1024px）深色基准**视觉描述；区块标注组件与 Token。响应式见 §6。`( )`=控件/状态。

### 4.1 全局外壳 App Shell

```
┌────────────────────────────────────────────────────────────────┐
│ ⚠ 仅限合法授权环境内学习使用 · 靶场为隔离沙箱            (×)    │ ComplianceBanner
│   bg:--state-warning-bg  icon:--state-warning-fg  text:--text-secondary │
├────────────────────────────────────────────────────────────────┤
│ ◧ vul-labs   [靶场] [我的进度]            (🌓主题) (👤 user ▾)   │ TopNav (--z-topnav)
│   Logo:青字  当前页:下方2px --brand                              │
├────────────────────────────────────────────────────────────────┤
│ 🟢 SQL注入-入门 · 运行中 · ⏱ 42:15(mono) ·  (访问) (销毁)        │ GlobalInstanceBar
│   状态色:--instance-ready  仅有活跃实例时出现                     │ (仅条件出现)
├────────────────────────────────────────────────────────────────┤
│                                                                  │
│  [ 主内容区 · bg:--bg-base · 最大宽 1280px 居中 · 左右 24px ]      │
│                                                                  │
├────────────────────────────────────────────────────────────────┤
│ 开源协议 · 免责声明 · 文档     (text:--text-tertiary)            │ Footer
└────────────────────────────────────────────────────────────────┘
```

### 4.2 登录页 `/login`（US-002，UX §3.2）

居中卡片，最大宽 **400px**，`--bg-surface` + `--border-subtle` + `--radius-xl`，置于 `--bg-base` 之上。

```
            ◧ vul-labs                  ← Logo 青色 + 标题 --fs-2xl/semibold
       登录以进入你的练习靶场            ← 副标题 --text-secondary/--fs-sm

  ┌────────────────────────────────┐
  │ 用户名 / 邮箱                    │   field-label
  │ [______________________]        │   .input
  │ 密码                             │
  │ [________________] (👁)         │   .input + 显隐切换(ghost icon-btn)
  │                                  │
  │ (  登 录  )  .btn--primary--lg--block │  关键 CTA
  │                                  │
  │ ⚠ 账号或密码错误                 │   错误态: --state-danger-fg + 图标(防枚举文案)
  │ ⏱ 尝试过多，请 30s 后再试         │   限流态: --state-warning-fg + 倒计时(mono)
  │                                  │
  │ 还没有账号? 去注册 →             │   --text-link
  └────────────────────────────────┘
```
- 错误**内联**（不弹窗），区分「凭证错误(danger)」与「限流(warning)」；错误出现后焦点移到首个错误（UX §6）。
- 加载：提交时按钮 loading（"登录中…"），禁重复提交。

### 4.3 注册页 `/register`（US-001，UX §3.3）

同登录卡片骨架，增加：密码强度条 + 确认密码 + **合规勾选**（UX D6）。

```
        注册 vul-labs 账号
  [用户名]  [邮箱]  [密码 (👁)]
   密码强度: ▮▮▯▯ 中 · 至少8位      ← 强度条(danger→warning→success)+文字
  [确认密码]
  [✓] 我已阅读并同意：仅在合法授权环境内使用   ← 必勾(checkbox)，未勾→注册禁用
  (  注 册  ) .btn--primary--lg--block       ← 未勾/校验未过=disabled
  ⚠ 用户名已被注册 / 密码不满足规则           ← 就近字段内联错误
  已有账号? 去登录 →
```

### 4.4 靶场列表 `/labs`（US-010 / US-011，UX §3.4）

左侧筛选侧栏（240px 常驻）+ 右侧结果区，卡片网格 `grid auto-fit minmax(280px, 1fr)` / gap 16px。

```
┌─ 靶场 (--fs-2xl) ───────────────────────────  (🔎搜索 P1占位/disabled) ─┐
├──────────────┬────────────────────────────────────────────────────────┤
│ [筛选侧栏]    │ 共 12 个靶场   [SQLi ⓧ] [入门 ⓧ]  (清除全部)            │
│ 漏洞类型      │ ┌───────────────┐ ┌───────────────┐ ┌──────────────┐ │
│ ☑ SQL注入    │ │ ▌(已通关高光)  │ │               │ │              │ │
│ ☐ XSS        │ │ SQL注入-入门 ✅ │ │ 反射XSS-入门 ○ │ │ 文件上传-中等 ○│ │
│ ☐ 文件上传    │ │ [db SQLi][▁入门]│ │ [</>XSS][▁入门]│ │ [↥上传][▁▄中等]│ │
│ 难度         │ │ 分值 100       │ │ 分值 100      │ │ 分值 200     │ │
│ ☐ 入门       │ └───────────────┘ └───────────────┘ └──────────────┘ │
│ ☐ 中等       │ LabCard: hover抬升+青边  整卡可点/键盘可达             │
│ ☐ 困难       │                                                        │
│ (清除筛选)    │ (筛选空: "无匹配靶场" + 清除筛选 / 加载: 6张骨架卡)     │
└──────────────┴────────────────────────────────────────────────────────┘
```
- 已选筛选 chips 可单独移除（`Badge--type` 样式 + ⓧ）。
- 卡片要素严格对齐 US-010：名称 / 类型徽章 / 难度徽章 / 通关状态 / 分值。

### 4.5 靶场详情 `/labs/:id`（第一迭代闭环主战场，UX §3.5）

左右双栏：左=任务说明（可滚动），右=操作面板（**`position:sticky`，`--z-sticky`**）。

```
‹ 返回靶场列表  (--text-link)
SQL 注入 - 入门   [db SQLi] [▁入门] 分值100   [✅已通关·06/09 12:30]  ← 头部:通关徽章 US-012/031
┌──────────────────────────────┬─────────────────────────────────┐
│ [左栏·任务说明 bg:--bg-base]   │ [右栏·sticky 操作面板]            │
│ ## 场景描述                    │ ┌ InstanceController(card) ─────┐ │
│ 正文 --text-secondary/normal   │ │ ◉ 状态徽章(随状态机切换)       │ │ ← §4.6
│ ## 攻击目标                    │ │ (主操作按钮 / 访问地址 / 倒计时)│ │
│ ## 提示 ▸(可折叠,默认收起)     │ └───────────────────────────────┘ │
│   ▸ 提示1  ▸ 提示2  (accordion)│ ┌ FlagSubmit(card) ─────────────┐ │
│ ## 漏洞类型说明 / 学习要点      │ │ [ flag{______} ](input--mono) │ │ ← §4.7
│                                │ │ (提交 flag) .btn--primary      │ │
│ (维护不可用: 启动入口disabled  │ │ 反馈区(成功/失败/限流/重复)     │ │
│  + 原因提示, US-012)           │ └───────────────────────────────┘ │
└──────────────────────────────┴─────────────────────────────────┘
```
- 左栏**绝不出现正确 flag**（PRD §8 / US-012）。提示用手风琴默认收起。
- 头部通关徽章 = `Badge--status` 成功态 + 奖杯图标 + 通关时间（已通关时）。

### 4.6 实例控制器 6 态高保真（US-020/021/022，UX §4.2）

> 同一面板按状态机切换；状态 = 徽章点 + 图标 + 文字（三重编码）。

```
① 未启动        ◌ 未启动(--instance-unstarted)
                "点击启动一个专属隔离实例"
                ( ▶ 启动环境 ) .btn--primary--lg--block

② 启动中        ◍ 启动中(--instance-provisioning, 脉冲光)
                ▰▰▰▱▱ 不确定进度条
                "正在拉起实例…通常 <30s（首次拉镜像可能更久）"
                ( 启动中… ) disabled+spinner   ← 防重复点(US-020)

③ 已就绪        ● 已就绪(--instance-ready)
                访问地址: http://…:32790   (input--mono, readonly)
                (🔗 新标签打开) (📋 复制)    ← 新标签 D4 / 复制 toast
                ⏱ 剩余 58:12 (mono, --text-primary)
                (♻ 重置 P1)(🗑 销毁 P1)      ← danger ghost, 二次确认

④ 启动失败      ✕ 启动失败(--instance-failed)
                "启动失败：镜像缺失/资源不足，请重试或稍后再试"(--state-danger-fg)
                ( ↻ 重试 ) .btn--secondary

⑤ 临近超时      ⏱ 即将到期(--instance-expiring, 倒计时高亮)
                ⏱ 剩余 04:52 (mono, --state-warning-fg, 整面板暖色描边)
                "实例 5 分钟后将被回收，注意保存进度"
                (♻ 续期 P1)

⑥ 已销毁/超时   ⊘ 已回收(--instance-destroyed)
                旧地址置灰删除线
                "实例已回收，访问地址已失效。可重新启动"(US-022)
                ( ▶ 重新启动 ) .btn--primary
```
- **复用提示**（US-020）：进入检测到已有运行实例 → info 提示条"你已有一个运行中的实例" +（访问 / 重新启动）。
- 状态变化通过 `aria-live="polite"` 播报；倒计时周期播报、临近超时升级提醒（UX §6）。

### 4.7 Flag 提交 4 态（US-030，UX §4.3）

```
默认   [ flag{__________________} ](input--mono)  (提交 flag)
提交中 按钮 loading，输入禁用（防重复提交）
成功   ✅ 成功卡(--state-success-bg) "🎉 通关！+100 分"
       → 计分动画(--dur-celebrate): +100 上浮 + 头部勋章点亮 + 详情头"已通关"
失败   ⚠ 内联红字(--state-danger-fg) "flag 错误，再试试（注意大小写/格式）"
       + 输入框抖动(reduced-motion 下仅变色) · 不计分
重复   ◐ 中性提示 "已通关，重复提交不再加分"（幂等 US-030）
限流   ⏱ 橙色提示+倒计时 "提交过于频繁，请 N 秒后再试"（防爆破 US-030）
```
- **安全约束**：任何状态/响应都不含正确 flag（PRD §8 / US-030）。成功反馈 `aria-live="assertive"`。

### 4.8 个人中心 `/me`（US-040 / US-031，Sprint 2，UX §3.6）

```
我的进度 (--fs-2xl)
┌──────────┐ ┌──────────┐ ┌──────────┐   概览卡(card)，大数字 mono/--fs-3xl
│ 总得分    │ │ 已通关    │ │ 通关率    │
│  300     │ │  3 / 9   │ │  33%     │   --brand-fg / --state-success-fg / --text-primary
└──────────┘ └──────────┘ └──────────┘
─────────────────────────────────────────────────────────
[已通关靶场] (table)              [最近活动] (时间线)
✅ SQL注入-入门 100 06/09 12:30    · 通关 SQL注入 06/09 12:30
✅ 反射XSS-入门 100 06/08 20:10    · 启动 文件上传 06/08 …
(空态: "还没通关任何靶场～" + (去练习 →) .btn--primary)  ← US-040 空态
```
- 仅展示本人数据（US-040 越权约束，平台面安全）。

---

## 5. 视觉编码规范（图标 + 文字 + 颜色三重 · 色盲友好）

> UX §6 硬要求：状态**不可仅靠颜色**。本节给出三套并行编码体系，所有图标在深/浅两主题下均可辨识。

### 5.1 难度编码（US-010/012）

| 难度 | 颜色 Token | **形状/图标** | 文字 | 色盲冗余 |
|------|-----------|--------------|------|---------|
| 入门 | `--difficulty-easy` (绿) | 信号条 `▁`(1 格) | 入门 | 格数(1)+文字 |
| 中等 | `--difficulty-medium`(琥珀) | 信号条 `▁▄`(2 格) | 中等 | 格数(2)+文字 |
| 困难 | `--difficulty-hard` (红) | 信号条 `▁▄█`(3 格) | 困难 | 格数(3)+文字 |

> 信号条「格数」本身即形状编码，即使去色也可区分；分值高低（100/200…）作辅助暗示。

### 5.2 漏洞类型编码（US-010/011）

类型徽章用**中性胶囊**承载，靠**专属图标 + 文字**识别，类型色仅作小面积图标着色（不参与红黄绿语义）：

| 类型 | 图标 | 图标色 | 文字 |
|------|------|--------|------|
| SQL 注入 | 数据库 | `--brand-fg` | SQLi |
| 反射型 XSS | `</>` | `--c-violet-400` | XSS |
| 文件上传 | 上传箭头 | `--c-sky-400` | Upload |
| SSRF (P1) | 地球 | `--c-teal-400` | SSRF |
| 命令注入 (P1) | 终端 `>_` | `--c-rose-400` | RCE |

### 5.3 实例 / 通关状态编码（US-020/021/022/030，UX §4.2）

| 状态 | 颜色 Token | 图标 | 文字 | 形态特征 |
|------|-----------|------|------|---------|
| 未启动 | `--instance-unstarted` | ◌ 空心圆 | 未启动 | 静态空心 |
| 启动中 | `--instance-provisioning` | ◍ 半填充 | 启动中 | **脉冲呼吸** |
| 已就绪 | `--instance-ready` | ● 实心圆/✓ | 已就绪 | 实心稳定 |
| 启动失败 | `--instance-failed` | ✕ 叉 | 启动失败 | 叉形 |
| 临近超时 | `--instance-expiring` | ⏱ 时钟 | 即将到期 | 时钟+倒计时高亮 |
| 已销毁 | `--instance-destroyed` | ⊘ 禁止 | 已回收 | 暗淡禁止符 |
| 已通关 | `--instance-passed` | 🏆 奖杯 | 已通关 | 奖杯+金色点缀 |

> 颜色（绿/蓝/红/琥珀/灰）+ 形状（空心/半填/实心/叉/时钟/禁止/奖杯）+ 文字三者并行；去色后靠形状与文字仍可区分。「已就绪(●绿)」与「已通关(🏆绿)」靠**奖杯图标**消歧。

---

## 6. 响应式与主题

### 6.1 响应式断点（桌面优先，UX §5.2 / D2）

```css
:root { --bp-sm: 640px; --bp-md: 768px; --bp-lg: 1024px; --bp-xl: 1280px; }
```

| 断点 | 列表 | 详情 | 筛选 | 全局状态条 |
|------|------|------|------|-----------|
| ≥1280 | 卡片 4 列 | 双栏舒展，右栏 sticky | 常驻侧栏 | 完整 |
| ≥1024（主） | 3 列 | 左右双栏，右栏 sticky | 常驻侧栏 | 完整 |
| ≥768 | 2 列 | 双栏收窄 / 右栏移至下方 | 顶部「筛选」抽屉 | 完整 |
| <768 | 单列 | **单栏堆叠**：说明在上；实例控制器 + flag 提交**吸底/置顶** | 抽屉 | 简化一行 |

> 移动端为"降级而非阉割"：保证看进度 / 查实例状态 / 提交 flag 可用，闭环关键操作（控制器+flag）置顶/吸底触手可及（UX §5.2）。所有触控目标 ≥ 44×44px（UX §6）。

```css
.lab-grid { display:grid; gap: var(--grid-gap);
  grid-template-columns: repeat(auto-fit, minmax(var(--card-min), 1fr)); }
.detail-layout { display:grid; gap: var(--space-6);
  grid-template-columns: minmax(0,1fr) 360px; } /* ≥1024 双栏 */
@media (max-width: 1023px){ .detail-layout { grid-template-columns: 1fr; } /* 单栏堆叠 */ }
.detail-aside { position: sticky; top: calc(var(--space-4) + 56px); }
@media (max-width: 1023px){ .detail-aside { position: static; } }
```

### 6.2 主题：深 / 浅 / 跟随系统（UX §5.3 / D1）

- 三态切换，偏好持久化 `localStorage('vul-labs-theme')`；首屏前内联脚本写入 `data-theme` **防闪烁（FOUC）**。
- 深色为 `:root` 默认与设计基准；浅色为**完整对照主题**（非附属），同样满足 AA。
- 「跟随系统」时读取 `prefers-color-scheme` 并响应其变化。

**浅色主题覆盖（语义层，节选关键值，均经 AA 核验）**

```css
[data-theme="light"] {
  --bg-base:     #F6F8FB;
  --bg-surface:  #FFFFFF;
  --bg-inset:    #EEF2F7;
  --bg-elevated: #FFFFFF;
  --bg-hover:    #EEF2F7;
  --bg-active:   #E3EAF3;
  --bg-overlay:  rgba(15, 23, 42, 0.45);

  --border-subtle: #DBE2EC;
  --border-strong: #C2CCDA;
  --border-focus:  var(--c-cyan-600);

  --text-primary:   #0B1220; /* 16:1 ✓ */
  --text-secondary: #3D4A5C; /*  9:1 ✓ */
  --text-tertiary:  #5B6675; /*  6:1 ✓ */
  --text-disabled:  #94A0B0;
  --text-inverse:   #FFFFFF;
  --text-link:      #0C7186; /* cyan-700, 配白底 5.4:1 ✓ */
  --text-link-hover:#0E91AE;

  --brand:     var(--c-cyan-600); /* 浅底主按钮加深以配白字 */
  --brand-hover: var(--c-cyan-700);
  --brand-fg:  #0C7186;
  --on-brand:  #FFFFFF;
  --brand-subtle-bg: rgba(6, 182, 212, 0.10);

  /* 语义前景在浅底改用 600 档以保 AA */
  --state-success-fg: var(--c-green-600);
  --state-warning-fg: var(--c-amber-600);
  --state-danger-fg:  var(--c-red-600);
  --state-info-fg:    var(--c-blue-600);
  --state-success-bg: rgba(22,163,74,0.10);
  --state-warning-bg: rgba(217,119,6,0.10);
  --state-danger-bg:  rgba(220,38,38,0.10);
  --state-info-bg:    rgba(37,99,235,0.10);

  --instance-unstarted:  #5B6675;
  --instance-provisioning:var(--c-blue-600);
  --instance-ready:      var(--c-green-600);
  --instance-failed:     var(--c-red-600);
  --instance-expiring:   var(--c-amber-600);
  --instance-destroyed:  #94A0B0;
  --instance-passed:     var(--c-green-600);

  --difficulty-easy:   var(--c-green-600);
  --difficulty-medium: var(--c-amber-600);
  --difficulty-hard:   var(--c-red-600);

  --shadow-md: 0 6px 16px rgba(15,23,42,0.10);
  --shadow-lg: 0 16px 40px rgba(15,23,42,0.16);
}
```

> 实现要点：组件只引用语义变量，切主题=切 `data-theme`，**零组件改动**。深/浅两套语义前景色分别用 400/600 档以双向满足 AA（U3）。

---

## 7. 无障碍与质量基线（对齐 UX §6）

| 维度 | 落地 |
|------|------|
| 对比度 | 正文 ≥ 4.5:1、大字/图形 ≥ 3:1；深/浅双主题均核验（见 §2.2 / §6.2 标注） |
| 状态不只靠颜色 | 难度/类型/实例/flag 全部图标+文字+颜色三重（§5） |
| 键盘可达 | 所有交互（导航/筛选/启动/复制/提交/销毁）可 Tab + Enter/Space；`:focus-visible` 焦点环 `--ring-focus` |
| 焦点管理 | 路由切换焦点移至 h1；模态/抽屉焦点陷阱、关闭归还；错误后聚焦首个错误字段 |
| ARIA 播报 | 实例状态/倒计时 `aria-live="polite"`；flag 结果/会话过期 `assertive`；表单 label 关联、`aria-invalid` |
| 触控目标 | 按钮/卡片/复选框点击区 ≥ 44×44px |
| 防误操作 | 销毁/重置二次确认模态（danger）（US-023） |
| 减少动效 | `prefers-reduced-motion` 降级脉冲/抖动/庆祝动画为静态+文案 |
| 合规可见 | 合规横幅/注册声明用 warning 弱底常驻，不弱化隐藏（PRD §8.3） |

---

## 8. 追溯矩阵（UI 产出 ↔ UX ↔ US）

| US ID | 名称 | UI 组件 / 页面（本文 §） | UX 来源 |
|-------|------|--------------------------|---------|
| US-001 | 用户注册 | §4.3 注册页 · §3.2 密码强度条/合规勾选 | UX §3.3 |
| US-002 | 用户登录 | §4.2 登录页（凭证错误/限流双态） | UX §3.2 |
| US-003 | 登出与会话 | §3.12 顶栏用户菜单 · §3.7 会话过期 toast | UX §3.1/§2.3 |
| US-010 | 浏览靶场列表 | §4.4 列表 · §3.4 靶场卡片 · §3.8 加载/空态 | UX §3.4/§4.1 |
| US-011 | 筛选靶场 | §4.4 筛选侧栏/chips · §3.8 筛选空态 | UX §3.4 |
| US-012 | 靶场详情 | §4.5 详情 · 维护不可用态 · 提示折叠 | UX §3.5 |
| US-020 | 启动实例 | §3.9/§4.6 实例控制器（未启动/启动中/失败/复用） | UX §3.5/§4.2 |
| US-021 | 访问/攻击实例 | §4.6 已就绪态（地址 mono+复制+新标签 D4） | UX §3.5 |
| US-022 | 超时自动销毁 | §4.6 临近超时/已销毁态 · §3.10 全局状态条倒计时 | UX §4.2 |
| US-023 | 手动销毁/重置 | §4.6 销毁/重置按钮 · §3.6 二次确认模态 | UX §4.2/§6 |
| US-030 | 提交 flag 计分 | §3.11/§4.7 Flag 提交 4 态 + 计分动画 | UX §4.3 |
| US-031 | 计分规则展示 | §4.4 卡片分值 · §4.5 头部 · §4.8 总分 | UX §3.4/§3.6 |
| US-040 | 个人进度 | §4.8 个人中心（概览卡/表格/空态/越权约束） | UX §3.6 |
| US-041 | 排行榜 (P2) | 导航预留位（不显示） | UX §1.3 |
| US-050/051 | 后台 (P2) | 未展开（占位） | UX §1.1 |

---

## 9. 给前端的交接说明（Handoff）

1. **Token 即代码**：§2 全部 CSS 变量可直接落为 `tokens.css`（`:root` 深色 + `[data-theme="light"]` 覆盖）。组件**仅消费语义层**（`--bg-*`/`--text-*`/`--state-*`/`--instance-*`/`--difficulty-*`），禁止硬编码与直接引用 `--c-*` 原始色。
2. **优先级**（对齐 UX §8.1）：详情页（实例控制器 6 态 + flag 4 态）> 列表 > 登录/注册 > 个人中心。
3. **状态驱动**：实例控制器与全局状态条由后端 `status` 字段驱动（`unstarted/provisioning/ready/failed/expiring/destroyed`，见 UX §8.2），UI 按 §4.6 / §5.3 映射徽章+图标+文案。
4. **安全红线**：任何组件/状态/mock **不得**渲染正确 flag；flag 校验结果仅 `correct/incorrect/rate-limited/already-passed`（UX §8.2）。
5. **无障碍验收**：交付前过一遍 §7 清单（对比度、键盘、焦点、ARIA、reduced-motion）。
6. **图标体系**：建议统一图标库（如 Lucide），按 §5 映射；信号条/状态点可用纯 CSS 绘制以减资源。

---

## 附录：本版未展开（标注后续）
- **后台管理区**（US-050/051，P2）：仅追溯占位，待 Web 闭环成熟后单出 UI。
- **搜索、提示分级、实例续期**（P1）：列表搜索框、续期按钮已预留视觉位（disabled/占位），交互细化待对应迭代。
- **高保真视觉稿（Figma/像素图）**：本文为文字版高保真规范，像素稿在 G1 评审后据此产出。
