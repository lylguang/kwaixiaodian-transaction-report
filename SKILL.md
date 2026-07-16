---
name: kwaixiaodian-transaction-report
description: 快手小店（快手电商）全店交易数据报告生成。通过浏览器自动化登录快手小店卖家后台，导航至生意通·全店成交分析页面，逐月查询成交金额、退款金额、客单价、成交订单数等核心财务指标，并生成可视化HTML报告。适用于需要按季度/月度/自定义周期统计快手小店交易数据的场景。触发词：快手小店交易数据、快手电商成交分析、快手小店生意通、kwaixiaodian transaction report。
agent_created: true
---

# 快手小店全店交易数据报告

## Overview

通过 agent-browser 浏览器自动化工具登录快手小店卖家后台，进入生意通·全店成交分析页面，按自然月逐月提取成交金额、退款金额、客单价、成交订单数等核心财务指标，最终生成包含KPI卡片、月度趋势图、数据对比表的HTML可视化报告。

## 前置条件

- 已安装 agent-browser（`npm install -g agent-browser && agent-browser install`）
- 用户拥有快手小店商家账号，可通过手机快手小店商家APP扫码登录
- 网络可正常访问 `https://s.kwaixiaodian.com`

## 工作流程

### Step 1: 加载 agent-browser 技能

首先调用 Skill 工具加载 `agent-browser` 技能，获取浏览器自动化命令的使用指南。

### Step 2: 打开浏览器并登录

用 `--headed` 模式打开快手小店后台，让用户可见浏览器窗口：

```bash
agent-browser --headed open "https://s.kwaixiaodian.com/zone"
```

检查页面是否跳转到登录页（URL 含 `login.kwaixiaodian.com`）：
- **如果跳转到登录页**：提示用户"请打开手机快手小店商家APP，点击左上角侧边栏扫一扫，扫描浏览器中的二维码登录。登录成功后告诉我。"
- **如果已在后台首页**：确认页面显示店铺名称（如"812女装28号店"），继续下一步

```bash
agent-browser snapshot 2>&1 | head -20
```

### Step 3: 导航至生意通·全店成交分析

从快手小店后台首页导航到全店成交分析页面：

1. 获取页面交互元素，找到"生意通"入口并点击：
```bash
agent-browser snapshot -i 2>&1 | grep -iE "生意通"
agent-browser click @eXX  # 点击生意通
```

2. 在生意通页面找到"交易分析"卡片并点击"查看"：
```bash
agent-browser snapshot -i 2>&1 | grep -iE "(交易|成交|分析)"
agent-browser click @eXX  # 点击交易分析的查看链接
```

3. 确认进入全店成交分析页面，应看到"成交金额""退款金额""成交订单数"等指标：
```bash
agent-browser snapshot -i 2>&1 | grep -iE "(成交金额|成交订单|退款|佣金|客单价)"
```

**备选导航路径**：也可尝试直接打开 `https://s.kwaixiaodian.com/zone/data/transaction/analysis`，但该URL可能变更，优先使用页面内导航。

### Step 4: 按自然月逐月查询数据

对每个目标月份执行以下操作：

#### 4.1 打开日期选择器

找到页面上的"其他"按钮并点击展开日期选项：
```bash
agent-browser snapshot -i 2>&1 | grep -iE "其他"
agent-browser click @eXX  # 点击"其他"展开
```

#### 4.2 选择"自然月"

```bash
agent-browser snapshot -i 2>&1 | grep -iE "自然月"
agent-browser click @eXX  # 点击"自然月"
```

#### 4.3 切换年份（如需查询往年）

月份选择器默认显示当前年份。如果目标月份不在当前年份，点击年份按钮切换：
```bash
agent-browser snapshot -i 2>&1 | grep -iE "(年|2025|2026)"
agent-browser click @eXX  # 点击年份按钮
agent-browser snapshot -i 2>&1 | grep -iE "2025|2026"
agent-browser click @eXX  # 选择目标年份
```

#### 4.4 选择目标月份

```bash
agent-browser snapshot -i 2>&1 | grep -iE "月"
agent-browser click @eXX  # 点击目标月份（如"7月"）
```

#### 4.5 等待数据加载并提取

等待3秒让数据加载完成，然后用 JavaScript eval 提取页面文本内容：
```bash
agent-browser click @eXX && sleep 3 && agent-browser eval "
var mainContent = document.querySelector('main') || document.body;
var text = mainContent.innerText;
text.substring(0, 3000);
"
```

如内容较长，可分段提取：
```bash
agent-browser eval "
var mainContent = document.querySelector('main') || document.body;
var text = mainContent.innerText;
text.substring(2000, 4000);
"
```

### Step 5: 解析核心数据指标

从提取的页面文本中解析以下指标（参考 `references/data_fields.md` 获取完整字段说明）：

**核心数据**：
- 成交金额（格式：`¥\n472,278.3` 或 `¥472,278`）
- 退款金额（支付日）（格式：`¥\n271,787.18`）
- 成交订单数（格式：`13,848`）
- 成交人数（格式：`11,739`）
- 客单价（格式：`¥\n40.23`）
- 金额退款率（支付日）（格式：`57.55\n%`）
- 下单订单数
- 退款订单数（支付日）
- 商品曝光次数
- 商品点击次数
- 点击成交转化率

**售卖方式构成**：自营成交 / 达人合作成交 / 超级链接成交（含金额和占比）

**售卖载体构成**：直播间成交 / 短视频成交 / 商品卡成交 / 其他成交（含金额和占比）

**账号明细**：每个账号（自营/合作达人）的成交金额、退款金额、成交订单数、成交人数、客单价

### Step 6: 关闭浏览器

所有月份数据采集完毕后关闭浏览器：
```bash
agent-browser close
```

### Step 7: 生成 HTML 报告

使用 `assets/report_template.html` 作为基础模板，替换其中的数据占位符，生成完整的可视化报告。报告应包含：

1. **页头**：店铺名称、统计周期、报告生成日期
2. **Q3核心KPI卡片**：成交金额、退款金额、净成交额、成交订单数、成交人数、客单价
3. **月度趋势柱状图**：各月成交金额对比
4. **月度核心数据明细表**：所有指标按月对比，含环比变化
5. **售卖方式构成表**：自营/达人合作/超级链接的月度和季度合计
6. **售卖载体构成表**：直播间/短视频/商品卡/其他的月度和季度合计
7. **账号成交明细表**：按月列出各账号的成交数据
8. **关键发现与洞察**：自动分析数据趋势、异常值、对比变化
9. **数据说明**：统计口径、数据来源、计算方式

**关键注意事项**：
- 中国股市/电商惯例：金额用 ¥ 符号，涨用红色、跌用绿色
- 净成交额 = 成交金额 - 退款金额（支付日）
- 客单价 = 成交金额 / 成交人数（不是除以订单数）
- 退款率 = 退款金额（支付日）/ 成交金额
- 季度合计的退款率需要用合计退款金额/合计成交金额重新计算，不能简单取月度平均

## 常见问题处理

### 登录会话过期
页面跳转到 `login.kwaixiaodian.com` 时，需要请用户重新扫码登录。登录后浏览器会自动重定向回后台。

### 日期选择器操作失败
- 自定义日期模式较复杂（需要操作日历面板逐月导航），优先使用"自然月"模式
- 如果"自然月"选项不可见，先点击"其他"展开下拉菜单
- 年份切换：点击年份按钮 → 选择目标年份 → 选择目标月份

### 页面数据未刷新
- 点击月份后等待3秒再提取数据
- 如果数据显示仍为"-"或"0"，检查日期范围是否正确（通过 snapshot 确认显示的日期范围）
- 尝试点击页面空白处关闭日历弹窗后重新提取

### 提取的文本包含大量重复内容
- 页面可能有多个AI助手推荐卡片导致文本重复，核心数据通常在"核心数据"关键词之后
- 使用 `text.substring(start, end)` 分段提取不同区域

## Resources

### references/
- `data_fields.md` — 全店成交分析页面的完整数据字段说明和解析规则

### assets/
- `report_template.html` — HTML报告模板，包含KPI卡片、趋势图、数据表的完整样式和结构
