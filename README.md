# 快手小店全店交易数据报告 Skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/Platform-WorkBuddy-blue)](https://www.codebuddy.cn)
[![Language](https://img.shields.io/badge/Language-Chinese-red)](https://en.wikipedia.org/wiki/Chinese_language)

> 通过浏览器自动化登录快手小店卖家后台，导航至生意通·全店成交分析页面，逐月查询成交金额、退款金额、客单价、成交订单数等核心财务指标，并生成可视化 HTML 报告。

## 功能特性

- **自动化数据采集**：通过 `agent-browser` 浏览器自动化工具，登录快手小店卖家后台，自动导航至生意通·全店成交分析页面
- **逐月数据查询**：使用「自然月」日期选择器，按月提取完整财务数据，支持任意历史月份
- **核心财务指标**：成交金额、退款金额、客单价、成交订单数、成交人数、退款率、下单订单数、商品曝光/点击次数等 15+ 个指标
- **渠道结构分析**：售卖方式构成（自营/达人合作/超级链接）、售卖载体构成（直播间/短视频/商品卡/其他）
- **账号明细**：按月列出每个自营/达人账号的成交数据
- **可视化 HTML 报告**：KPI 卡片、月度趋势柱状图、数据对比表、自动洞察分析
- **季度对比**：支持多季度数据对比，展示环比变化趋势

## 前置条件

- [WorkBuddy](https://www.codebuddy.cn) 桌面客户端（用于运行 Skill）
- `agent-browser` 浏览器自动化工具（WorkBuddy 内置）
- 快手小店商家账号（可通过手机快手小店商家 APP 扫码登录）
- 网络可正常访问 `https://s.kwaixiaodian.com`

## 安装

### 方式一：通过 WorkBuddy 安装

将本仓库克隆到 WorkBuddy 的 skills 目录：

```bash
# 用户级 Skill（全局可用）
git clone https://github.com/YOUR_USERNAME/kwaixiaodian-transaction-report.git ~/.workbuddy/skills/kwaixiaodian-transaction-report
```

### 方式二：手动安装

1. 下载或克隆本仓库
2. 将文件复制到 `~/.workbuddy/skills/kwaixiaodian-transaction-report/` 目录
3. 重启 WorkBuddy 客户端

## 使用方法

### 触发方式

在 WorkBuddy 对话中输入以下关键词即可触发：

- "快手小店交易数据"
- "快手电商成交分析"
- "快手小店生意通"
- "查询快手小店 Q3/Q4 交易数据"

### 示例对话

```
用户：帮我查询快手小店的交易数据
WorkBuddy：（自动加载 Skill，打开浏览器，引导扫码登录）

用户：给我查询 2025 年第四季度的数据
WorkBuddy：（逐月查询 10/11/12 月数据，生成 HTML 报告）
```

### 生成报告示例

报告包含以下模块：

| 模块 | 说明 |
|------|------|
| 核心指标总览 | 成交金额、退款金额、净成交额、成交订单数、成交人数、客单价 |
| 月度趋势柱状图 | 各月成交金额可视化对比 |
| 月度核心数据明细 | 全部指标按月对比，含环比变化 |
| 售卖方式构成 | 自营/达人合作/超级链接的月度和季度合计 |
| 售卖载体构成 | 直播间/短视频/商品卡/其他的月度和季度合计 |
| 账号成交明细 | 按月列出各账号的成交数据 |
| 季度对比 | 与其他季度的环比对比（可选） |
| 关键发现与洞察 | 自动分析数据趋势、异常值 |
| 数据说明 | 统计口径、数据来源、计算方式 |

## 项目结构

```
kwaixiaodian-transaction-report/
├── SKILL.md                        # Skill 主文件 — 完整工作流程指南
├── references/
│   └── data_fields.md              # 数据字段说明和解析规则
├── assets/
│   └── report_template.html        # HTML 报告模板（含占位符）
├── README.md                       # 项目文档
├── LICENSE                         # MIT 开源协议
└── .gitignore
```

## 工作流程

```
┌─────────────┐    ┌──────────────┐    ┌─────────────────────┐
│  加载 Skill  │───>│  打开浏览器   │───>│  扫码登录快手小店    │
└─────────────┘    │  (--headed)  │    │  卖家后台           │
                   └──────────────┘    └─────────────────────┘
                                                │
                                                ▼
┌─────────────┐    ┌──────────────┐    ┌─────────────────────┐
│  生成 HTML   │<───│  关闭浏览器   │<───│  逐月查询交易数据    │
│  可视化报告  │    │              │    │  (自然月选择器)      │
└─────────────┘    └──────────────┘    └─────────────────────┘
```

### 详细步骤

1. **加载 agent-browser 技能** — 获取浏览器自动化工具
2. **打开浏览器并登录** — `--headed` 模式打开快手小店，处理扫码登录
3. **导航至生意通·全店成交分析** — 首页 → 生意通 → 交易分析 → 全店成交分析
4. **按自然月逐月查询** — 日期选择器操作：其他 → 自然月 → 切换年份 → 选择月份
5. **解析核心数据指标** — 成交金额、退款金额、客单价等 15+ 个字段
6. **关闭浏览器**
7. **生成 HTML 报告** — 使用模板替换占位符生成可视化报告

## 关键注意事项

- **金额格式**：中国电商惯例，金额使用 ¥ 符号
- **净成交额** = 成交金额 - 退款金额（支付日）
- **客单价** = 成交金额 / 成交人数（不是除以订单数）
- **退款率** = 退款金额（支付日）/ 成交金额 × 100%
- **季度退款率**需用合计退款金额/合计成交金额重新计算，不能简单取月度平均
- **登录会话**：快手小店登录会话可能过期，需重新扫码登录

## 技术要点

- **React 受控组件**：日期选择器使用 `Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype, 'value').set` 配合 `dispatchEvent` 设置值
- **页面文本提取**：通过 `agent-browser eval` 执行 JavaScript，提取 `main` 元素的 `innerText`
- **分页数据采样**：对于大量订单数据，采用采样 + 页面总数的方式获取
- **元素引用**：通过 `agent-browser snapshot -i` 获取可交互元素列表，使用 `@eXX` 引用点击

## 环境要求

| 依赖 | 说明 |
|------|------|
| WorkBuddy | 桌面客户端，运行 Skill 和 agent-browser |
| agent-browser | 浏览器自动化工具（WorkBuddy 内置） |
| 快手小店商家 APP | 用于扫码登录 |

## 常见问题

<details>
<summary>登录会话过期怎么办？</summary>

页面跳转到 `login.kwaixiaodian.com` 时，需要请用户重新扫码登录。打开手机快手小店商家 APP，点击左上角侧边栏的扫一扫，扫描浏览器中的二维码。
</details>

<details>
<summary>日期选择器操作失败？</summary>

自定义日期模式较复杂（需要操作日历面板逐月导航），优先使用「自然月」模式。如果「自然月」选项不可见，先点击「其他」展开下拉菜单。
</details>

<details>
<summary>页面数据未刷新？</summary>

点击月份后等待 3 秒再提取数据。如果数据显示仍为"-"或"0"，检查日期范围是否正确（通过 snapshot 确认显示的日期范围），尝试点击页面空白处关闭日历弹窗后重新提取。
</details>

<details>
<summary>提取的文本包含大量重复内容？</summary>

页面可能有多个 AI 助手推荐卡片导致文本重复，核心数据通常在"核心数据"关键词之后。使用 `text.substring(start, end)` 分段提取不同区域。
</details>

## 开源协议

本项目基于 [MIT License](LICENSE) 开源，欢迎自由使用、修改和分发。

## 贡献

欢迎提交 Issue 和 Pull Request！

- 发现 Bug 或有功能建议？请提交 [Issue](../../issues)
- 想贡献代码？请提交 [Pull Request](../../pulls)

## 相关链接

- [WorkBuddy 官网](https://www.codebuddy.cn)
- [WorkBuddy 文档](https://www.codebuddy.cn/docs/workbuddy/Overview)
- [快手小店卖家后台](https://s.kwaixiaodian.com)
