# TEST-00004｜UI 全局美化重设计 — 测试文档

- **文档编号**: TEST-00004
- **创建日期**: 2026-03-01
- **版本**: V1.0
- **状态**: 草案
- **对齐 PRD**: `docs/PRD/PRD-00004-UI全局美化重设计.md`
- **对齐 FD**: `docs/FD/FD-00004-UI全局美化重设计.md`
- **对齐 TDD**: `docs/TDD/TDD-00004-UI全局美化重设计.md`
- **视觉参考**: `ui-preview.html`（本地预览原型）

---

## 目录

1. [测试概述](#1-测试概述)
2. [测试策略](#2-测试策略)
3. [测试环境配置](#3-测试环境配置)
4. [P0 — 核心 UI 结构测试](#4-p0--核心-ui-结构测试)
5. [P0 — CSS 设计系统测试](#5-p0--css-设计系统测试)
6. [P0 — 主题切换测试](#6-p0--主题切换测试)
7. [P0 — 侧边栏导航测试](#7-p0--侧边栏导航测试)
8. [P0 — 账号卡片组件测试](#8-p0--账号卡片组件测试)
9. [P0 — Token 状态徽章测试](#9-p0--token-状态徽章测试)
10. [P0 — 邮件区域测试](#10-p0--邮件区域测试)
11. [P1 — 验证码高亮测试](#11-p1--验证码高亮测试)
12. [P1 — 仪表盘页面测试](#12-p1--仪表盘页面测试)
13. [P1 — 临时邮箱页面测试](#13-p1--临时邮箱页面测试)
14. [P1 — 全量刷新进度条测试](#14-p1--全量刷新进度条测试)
15. [P0 — 登录页测试](#15-p0--登录页测试)
16. [P2 — 移动端响应式测试](#16-p2--移动端响应式测试)
17. [P0 — 后端功能回归测试](#17-p0--后端功能回归测试)
18. [P0 — JS 兼容性回归测试](#18-p0--js-兼容性回归测试)
19. [测试数据准备](#19-测试数据准备)
20. [验收标准与门禁](#20-验收标准与门禁)

---

## 1. 测试概述

### 1.1 测试目标

本测试文档用于验证 UI 全局美化重设计的正确性与完整性，确保：

1. **视觉设计目标达成**
   - 现代国风设计语言落地（砖红/翠绿/琥珀金/宣纸米白配色）
   - 与 `ui-preview.html` 原型视觉效果高度一致
   - 亮色/暗色双主题均正确渲染

2. **JS 功能零回归**
   - 所有原有 JS 函数（账号选择、邮件加载、分组管理等）行为不变
   - 所有 API 调用路径、参数、响应处理不变
   - 所有模态框、Toast、交互反馈正常

3. **新 UI 组件正确性**
   - Token 状态徽章颜色/文字与逻辑一致
   - 账号头像渐变色稳定（同邮箱地址颜色相同）
   - 验证码识别+高亮算法正确
   - 侧边栏导航状态切换正确

4. **后端零改动确认**
   - 所有后端测试通过率 100%（与改动前相同）

### 1.2 测试范围

**包含：**
- 前端视觉检查（所有组件）
- 前端交互测试（所有可交互元素）
- JS 算法单元测试（stringToColor、getTokenStatus、extractVerifCode）
- 后端 API 功能回归（现有测试套件）
- DOM ID 兼容性验证
- 跨浏览器基础验证（Chrome、Edge）

**不包含：**
- 后端代码变更验证（本次不改后端）
- 性能压测
- 无障碍（a11y）深度测试
- IE 兼容性

### 1.3 测试分类

| 分类 | 说明 | 测试方式 |
|------|------|---------|
| 视觉测试 | CSS 颜色、布局、组件外观 | 人工对比 + 截图 |
| 交互测试 | 按钮点击、Tab 切换、侧边栏 | 人工操作 |
| 功能测试 | JS 算法逻辑、数据渲染 | 自动化（Node）+ 人工 |
| 回归测试 | 现有 API + JS 功能不变 | 自动化（Python unittest）|
| 兼容性测试 | 双主题、双浏览器 | 人工 |

---

## 2. 测试策略

### 2.1 测试金字塔

```
         /\
        /  \        视觉验收（与 ui-preview.html 对比）
       /    \       - 配色、间距、图标
      /------\
     /        \     交互测试（手动执行）
    /          \    - 侧边栏、主题切换、账号选择
   /------------\
  /              \  功能回归（自动化执行）
 /________________\ - Python unittest（后端 API）
                    - JS 算法逻辑验证
```

### 2.2 测试优先级

**P0（必须全部通过才能上线）：**
- 后端 API 功能完整（回归测试通过）
- 核心 UI 结构（sidebar、topbar、三栏布局）
- 所有原有 JS 函数正常（DOM ID 兼容性）
- 登录/退出/加载账号/加载邮件

**P1（发布前应通过）：**
- Token 状态徽章逻辑
- 验证码高亮
- 仪表盘页面
- 全量刷新进度条

**P2（可在后续迭代中修复）：**
- 移动端响应式
- 次要视觉细节

### 2.3 逐阶段门禁

每个实施 Phase 完成后，必须通过对应门禁才能进入下一 Phase：

| Phase | 门禁测试 |
|-------|---------|
| Phase 1（CSS） | TC-CSS-* 通过，浏览器无 CSS 错误 |
| Phase 2（HTML 骨架） | TC-LAYOUT-* + TC-JS-COMPAT-* 通过 |
| Phase 3（JS 渲染） | TC-ACCOUNT-* + TC-EMAIL-* + TC-TOKEN-* 通过 |
| Phase 4（附加页面） | TC-DASH-* + TC-PROGRESS-* 通过 |
| Phase 5（登录+响应式） | TC-LOGIN-* 通过，回归测试 100% |

---

## 3. 测试环境配置

### 3.1 浏览器环境

| 浏览器 | 版本要求 | 用途 |
|--------|---------|------|
| Google Chrome | 最新稳定版 | 主要测试浏览器 |
| Microsoft Edge | 最新稳定版 | 兼容性验证 |

**浏览器开发者工具设置：**
- Console：观察 JS 错误（必须为零报错）
- Network：验证 API 请求正常
- Elements：验证 DOM 结构和 CSS 变量

### 3.2 测试数据要求

在执行测试前，需准备以下测试数据（通过页面 UI 手动创建或 SQLite 直接插入）：

| 数据类型 | 数量 | 说明 |
|---------|------|------|
| 分组 | 3个 | 至少一个系统分组（临时邮箱）、两个自定义分组 |
| 账号 | 6个 | 包括有效 token × 2、即将过期 × 2、已过期 × 1、未知 × 1 |
| 临时邮箱 | 2个 | 验证临时邮箱卡片渲染 |
| 含验证码邮件 | 3封 | 6位数字 × 1、4位数字 × 1、字母数字混合 × 1 |
| 无内容邮件 | 1封 | 测试空正文边界情况 |

### 3.3 Token 过期时间构造

测试 Token 状态徽章时，需要数据库中有如下数据：

```sql
-- 有效 Token（有效期 > 30天后）
UPDATE accounts SET token_valid=1, token_expires_at='2027-01-01T00:00:00' WHERE id=1;

-- 即将过期（有效期在 1-30 天内）
UPDATE accounts SET token_valid=1, token_expires_at=datetime('now','+10 days') WHERE id=2;

-- 已过期
UPDATE accounts SET token_valid=0, token_expires_at='2025-01-01T00:00:00' WHERE id=3;

-- token_valid=true 但无 expires_at
UPDATE accounts SET token_valid=1, token_expires_at=NULL WHERE id=4;

-- 未知（token_valid 字段为 NULL）
UPDATE accounts SET token_valid=NULL, token_expires_at=NULL WHERE id=5;
```

---

## 4. P0 — 核心 UI 结构测试

### TC-LAYOUT-001：页面整体骨架渲染

**测试目标：** 验证新三栏+侧边栏布局正确渲染

**前置条件：** 已登录，浏览器窗口宽度 >= 1280px

**测试步骤：**
1. 打开应用主页（`/`）
2. 目测页面整体结构
3. 使用开发者工具检查元素层级

**预期结果：**
- [ ] `#app-sidebar` 可见，位于页面最左侧，宽度约 60px（折叠态）
- [ ] `.main-wrapper` 填充剩余宽度
- [ ] `.topbar` 位于内容区顶部，高度约 52px
- [ ] `.workspace` 下方三列可见（分组列 / 账号列 / 邮件区）
- [ ] 无横向滚动条出现
- [ ] 无内容溢出或重叠

**对比参考：** `ui-preview.html` → `#page-accounts` 的三列布局

---

### TC-LAYOUT-002：三栏宽度验证

**测试目标：** 验证三栏固定宽度符合设计规格

**前置条件：** 已登录，浏览器宽度 >= 1200px

**测试步骤：**
1. 打开开发者工具 → Elements
2. 选中 `.col-groups` 元素，查看 computed 宽度
3. 选中 `.col-accounts` 元素，查看 computed 宽度
4. 选中 `.col-email` 元素，查看 computed 宽度

**预期结果：**
- [ ] `.col-groups` 宽度 = 220px（±2px 容差）
- [ ] `.col-accounts` 宽度 = 280px（±2px 容差）
- [ ] `.col-email` 宽度 = 剩余空间（自适应）
- [ ] 三列总宽度 + 侧边栏宽度 ≈ 视窗宽度

---

### TC-LAYOUT-003：旧布局元素不存在

**测试目标：** 确认旧的四栏拖拽布局元素已被移除

**测试步骤：**
1. 打开开发者工具 → Console
2. 执行以下检查脚本：
   ```javascript
   console.log('resizer:', document.querySelectorAll('.resizer').length);  // 应为 0
   console.log('group-panel:', document.querySelectorAll('.group-panel').length);  // 应为 0
   console.log('account-panel:', document.querySelectorAll('.account-panel').length);  // 应为 0
   console.log('main-container:', document.querySelectorAll('.main-container').length);  // 应为 0
   ```

**预期结果：**
- [ ] 以上所有输出均为 `0`
- [ ] Console 无 JS 报错（`layout-manager.js` / `layout-bootstrap.js` 相关错误为 0）

---

### TC-LAYOUT-004：DOM ID 完整性验证

**测试目标：** 验证所有被 JS 引用的 DOM ID 仍然存在

**测试步骤：**
1. 打开开发者工具 → Console
2. 执行以下完整性检查脚本：
   ```javascript
   const requiredIds = [
     'currentAccount', 'currentAccountEmail',
     'groupList', 'accountList',
     'emailList', 'emailDetail', 'emailDetailToolbar',
     'emailCount', 'methodTag', 'folderTabs',
     'accountPanelTitle', 'csrfToken'
   ];
   const missing = requiredIds.filter(id => !document.getElementById(id));
   console.log('Missing IDs:', missing.length === 0 ? '✅ 全部存在' : '❌ 缺失：' + missing.join(', '));
   ```

**预期结果：**
- [ ] 控制台输出 `Missing IDs: ✅ 全部存在`
- [ ] `missing` 数组长度为 0

---

## 5. P0 — CSS 设计系统测试

### TC-CSS-001：亮色模式 CSS 变量

**测试目标：** 验证 `:root` CSS 变量值符合设计规格

**测试步骤：**
1. 打开开发者工具 → Console
2. 执行以下检查：
   ```javascript
   const style = getComputedStyle(document.documentElement);
   const checks = {
     '--clr-primary':   style.getPropertyValue('--clr-primary').trim(),
     '--bg':            style.getPropertyValue('--bg').trim(),
     '--bg-sidebar':    style.getPropertyValue('--bg-sidebar').trim(),
     '--clr-jade':      style.getPropertyValue('--clr-jade').trim(),
     '--clr-danger':    style.getPropertyValue('--clr-danger').trim(),
   };
   console.table(checks);
   ```

**预期结果：**

| CSS 变量 | 期望值 | 含义 |
|---------|-------|------|
| `--clr-primary` | `#B85C38` | 砖红/主色 |
| `--bg` | `#F5EDE0` | 宣纸米白背景 |
| `--bg-sidebar` | `#2C1A0E` | 深墨棕侧边栏 |
| `--clr-jade` | `#3A7D44` | 翠绿/有效状态 |
| `--clr-danger` | `#C0392B` | 危险红/过期 |

---

### TC-CSS-002：暗色模式 CSS 变量

**测试目标：** 验证暗色模式下 CSS 变量正确覆盖

**前置条件：** 主题已切换为暗色（`<html data-theme="dark">`）

**测试步骤：**
1. 点击主题切换按钮，切换为暗色
2. 在 Console 执行：
   ```javascript
   const style = getComputedStyle(document.documentElement);
   console.log('bg:', style.getPropertyValue('--bg').trim());
   console.log('bg-card:', style.getPropertyValue('--bg-card').trim());
   console.log('text:', style.getPropertyValue('--text').trim());
   ```

**预期结果：**
- [ ] `--bg` = `#1A1008`（深色背景）
- [ ] `--bg-card` = `#231507`
- [ ] `--text` = `#F0E0CC`（浅色文字）

---

### TC-CSS-003：字体系统

**测试目标：** 验证页面字体符合设计规格

**测试步骤：**
1. 开发者工具 → Elements → 选中 `<body>`
2. Computed 面板查看 `font-family`

**预期结果：**
- [ ] `font-family` 包含 `PingFang SC` 或 `Noto Sans SC` 或 `Microsoft YaHei`
- [ ] 等宽字体区域（如 token 值）使用 `Consolas` 或 `JetBrains Mono`

---

### TC-CSS-004：组件圆角规格

**测试目标：** 验证卡片、按钮等组件圆角符合设计规格

**测试步骤：**
1. 目测账号卡片圆角
2. 目测按钮圆角
3. 目测 Token 徽章圆角（应为 pill 形）

**预期结果：**
- [ ] 卡片圆角约 10px（视觉上有明显圆角）
- [ ] Token 状态徽章为 pill 形（圆角 9999px）
- [ ] 主按钮圆角约 8-10px

---

## 6. P0 — 主题切换测试

### TC-THEME-001：亮色→暗色切换

**测试目标：** 验证点击主题切换按钮后正确切换到暗色

**前置条件：** 当前为亮色模式

**测试步骤：**
1. 定位侧边栏底部的主题切换按钮（🌙 图标）
2. 点击按钮
3. 观察页面变化

**预期结果：**
- [ ] `<html>` 元素的 `data-theme` 属性变为 `dark`
- [ ] 页面背景色立即变为深色（`#1A1008` 附近）
- [ ] 侧边栏、顶栏背景均变暗
- [ ] 文字颜色变为浅色
- [ ] 过渡平滑（有 CSS transition 动画，约 0.25s）
- [ ] 切换按钮图标变为 ☀️

---

### TC-THEME-002：暗色→亮色切换

**测试目标：** 验证可以从暗色切回亮色

**前置条件：** 当前为暗色模式

**测试步骤：**
1. 点击主题切换按钮（☀️ 图标）
2. 观察页面变化

**预期结果：**
- [ ] `<html data-theme>` 变为 `light`
- [ ] 页面背景恢复为宣纸米白 `#F5EDE0`
- [ ] 切换按钮图标变为 🌙

---

### TC-THEME-003：主题状态持久化

**测试目标：** 验证页面刷新后主题仍保持

**测试步骤：**
1. 切换到暗色模式
2. 刷新页面（F5）
3. 观察刷新后页面主题

**预期结果：**
- [ ] 页面刷新后仍为暗色模式
- [ ] 无明显的亮色→暗色闪烁（FOUC），因为主题在 head 中同步初始化

---

### TC-THEME-004：主题切换无 FOUC

**测试目标：** 验证暗色模式下刷新页面不出现亮色闪烁

**测试步骤：**
1. 切换到暗色模式
2. 硬刷新页面（Ctrl+Shift+R）
3. 观察是否有短暂白屏/亮色闪烁

**预期结果：**
- [ ] 无可见的颜色闪烁（`<head>` 内有同步主题初始化脚本）

---

## 7. P0 — 侧边栏导航测试

### TC-SIDEBAR-001：导航项点击切换

**测试目标：** 验证点击侧边栏导航项后正确切换页面

**测试步骤：**
1. 点击侧边栏 "仪表盘" 导航项
2. 观察右侧内容区变化和导航项激活状态
3. 点击 "账号管理"（邮件管理）导航项
4. 再次观察变化

**预期结果：**
- [ ] 被点击的导航项有高亮/激活样式（砖红背景或左边框）
- [ ] 对应页面区域显示
- [ ] 上一个导航项的激活状态消失
- [ ] 顶栏标题更新为对应页面标题

---

### TC-SIDEBAR-002：导航激活样式

**测试目标：** 验证当前活跃导航项的视觉样式正确

**测试步骤：**
1. 确认当前在主邮件管理页
2. 开发者工具查看活跃导航项的 CSS 样式

**预期结果：**
- [ ] 活跃导航项背景色为主色相关（`rgba(184,92,56,.22)` 或类似）
- [ ] 活跃导航项文字颜色变亮
- [ ] 活跃导航项有左侧竖线指示器（`::before` 伪元素）

---

### TC-SIDEBAR-003：侧边栏底部按钮

**测试目标：** 验证侧边栏底部用户区域正常显示

**预期结果：**
- [ ] 侧边栏底部可见"退出登录"按钮（或用户头像区）
- [ ] 主题切换按钮在底部区域
- [ ] 点击退出登录有跳转到登录页

---

## 8. P0 — 账号卡片组件测试

### TC-ACCOUNT-001：账号卡片基本渲染

**测试目标：** 验证账号卡片在账号列表中正确渲染

**前置条件：** 已选中一个包含账号的分组

**预期结果：**
- [ ] 每个账号显示为独立卡片（`.account-item`）
- [ ] 卡片包含：渐变色圆形头像 + 邮箱地址 + Token 状态徽章
- [ ] 邮箱地址文字显示完整（或单行省略）
- [ ] 卡片有 hover 效果（轻微上移/阴影）

---

### TC-ACCOUNT-002：账号头像颜色稳定性

**测试目标：** 验证 `stringToColor()` 对同一邮箱生成稳定颜色

**测试步骤：**
1. 记录账号列表中 `test@example.com` 账号的头像颜色
2. 刷新页面
3. 再次观察同一账号头像颜色

**预期结果：**
- [ ] 两次观察到的头像渐变颜色完全相同
- [ ] 不同邮箱的头像颜色有明显区别（不完全相同）

**Console 验证：**
```javascript
// 手动验证颜色稳定性
function stringToColor(str) {
    let hash = 0;
    for (let i = 0; i < str.length; i++) {
        hash = (hash << 5) - hash + str.charCodeAt(i);
        hash |= 0;
    }
    const hue = Math.abs(hash) % 360;
    return hue;
}
// 同一邮箱多次调用应返回相同值
console.log(stringToColor('test@outlook.com'));  // 应每次相同
console.log(stringToColor('test@outlook.com'));
```

---

### TC-ACCOUNT-003：账号卡片选中状态

**测试目标：** 验证点击账号卡片后正确高亮且更新顶栏

**测试步骤：**
1. 点击某个账号卡片
2. 观察该卡片样式变化
3. 观察顶栏中 `#currentAccountEmail` 的更新

**预期结果：**
- [ ] 被点击的账号卡片有选中样式（`.active` 类，边框或背景高亮）
- [ ] 顶栏 `#currentAccountEmail` 更新为该邮箱地址
- [ ] `#currentAccount` 区域可见
- [ ] `#folderTabs`（收件箱/已删除）显示出来

---

### TC-ACCOUNT-004：账号头像首字母

**测试目标：** 验证头像显示邮箱地址的首字母（大写）

**预期结果：**
- [ ] 邮箱 `alice@example.com` 的头像显示 `A`
- [ ] 邮箱 `bob@test.com` 的头像显示 `B`
- [ ] 首字母为大写

---

## 9. P0 — Token 状态徽章测试

### TC-TOKEN-001：有效 Token 徽章

**测试目标：** 验证 `token_valid=true` 且距过期 > 30 天的账号显示绿色有效徽章

**前置条件：** 数据库中有 `token_valid=1, token_expires_at='2027-01-01'` 的账号

**预期结果：**
- [ ] Token 徽章文字为 `✓ 有效`
- [ ] Token 徽章背景为绿色系（`--clr-jade`，约 `#3A7D44`）
- [ ] Token 徽章为 pill 形状

---

### TC-TOKEN-002：即将过期 Token 徽章

**测试目标：** 验证距过期 ≤ 30 天的账号显示橙色警告徽章

**前置条件：** 数据库中有 `token_valid=1, token_expires_at=<今天+10天>` 的账号

**预期结果：**
- [ ] Token 徽章文字为 `⚠ 即将过期`
- [ ] Token 徽章颜色为橙色系（`--clr-warn`，约 `#E67E22`）

---

### TC-TOKEN-003：已过期 Token 徽章

**测试目标：** 验证 `token_valid=false` 的账号显示红色过期徽章

**前置条件：** 数据库中有 `token_valid=0` 的账号

**预期结果：**
- [ ] Token 徽章文字为 `✗ 已过期`
- [ ] Token 徽章颜色为红色系（`--clr-danger`，约 `#C0392B`）

---

### TC-TOKEN-004：未知 Token 徽章

**测试目标：** 验证 `token_valid=NULL` 的账号显示灰色未知徽章

**前置条件：** 数据库中有 `token_valid=NULL` 的账号

**预期结果：**
- [ ] Token 徽章文字为 `? 未知`
- [ ] Token 徽章颜色为灰色系（`--text-muted`）

---

### TC-TOKEN-005：Token 状态算法 Console 验证

**测试目标：** 直接验证 `getTokenStatus()` 函数的算法正确性

**测试步骤：**
在 Console 中执行：
```javascript
// 测试各种情况
const cases = [
  { name: '有效(2027)', account: { token_valid: true, token_expires_at: '2027-01-01T00:00:00' } },
  { name: '即将过期', account: { token_valid: true, token_expires_at: new Date(Date.now() + 10*86400000).toISOString() } },
  { name: '已过期', account: { token_valid: false, token_expires_at: '2025-01-01' } },
  { name: '未知(无字段)', account: {} },
  { name: '有效(无expires)', account: { token_valid: true } },
];
cases.forEach(c => {
  const result = getTokenStatus(c.account);
  console.log(c.name, '->', result.status, result.label);
});
```

**预期输出：**

| 测试用例 | 期望 status | 期望 label |
|---------|------------|-----------|
| 有效(2027) | `valid` | `✓ 有效` |
| 即将过期 | `expiring` | `⚠ 即将过期` |
| 已过期 | `expired` | `✗ 已过期` |
| 未知(无字段) | `unknown` | `? 未知` |
| 有效(无expires) | `valid` | `✓ 有效` |

---

## 10. P0 — 邮件区域测试

### TC-EMAIL-001：邮件列表行渲染

**测试目标：** 验证邮件列表行符合新设计

**前置条件：** 已选中账号，已点击"获取邮件"

**预期结果：**
- [ ] 每封邮件显示为独立行（`.email-item`）
- [ ] 每行包含：发件人头像字母 + 发件人名 + 主题 + 预览文字 + 时间戳
- [ ] 未读邮件行有左侧彩色边框或特殊背景色
- [ ] 发件人头像有彩色渐变背景
- [ ] 时间戳对齐到行的右上角

---

### TC-EMAIL-002：邮件行 hover 效果

**测试目标：** 验证邮件行 hover 时有视觉反馈

**测试步骤：**
1. 将鼠标移到邮件列表行上
2. 观察背景颜色变化

**预期结果：**
- [ ] hover 时行背景有轻微颜色变化（主色调淡色背景）
- [ ] 过渡平滑（有 CSS transition）

---

### TC-EMAIL-003：邮件详情区渲染

**测试目标：** 验证点击邮件后详情区正确显示

**测试步骤：**
1. 点击某封邮件
2. 观察右侧详情区变化

**预期结果：**
- [ ] 邮件详情区（`#emailDetail`）内容更新
- [ ] 显示邮件发件人、主题、时间
- [ ] 正文内容通过 iframe 沙箱显示（HTML 邮件）
- [ ] `#emailDetailToolbar` 可见（含删除/回复等按钮）

---

### TC-EMAIL-004：邮件空状态

**测试目标：** 验证未选中邮件时详情区显示占位提示

**前置条件：** 已选中账号但未点击任何邮件

**预期结果：**
- [ ] `#emailDetail` 显示占位内容：图标 + "选择一封邮件查看详情"
- [ ] 占位内容居中显示
- [ ] `#emailDetailToolbar` 隐藏

---

### TC-EMAIL-005：文件夹 Tab 切换

**测试目标：** 验证收件箱/已删除 Tab 切换正常

**测试步骤：**
1. 选中账号后，`#folderTabs` 出现
2. 点击"已删除"Tab
3. 观察邮件列表变化
4. 点击"收件箱"Tab 切回

**预期结果：**
- [ ] 活跃 Tab 有下划线或背景高亮样式（`.active` 类）
- [ ] 切换 Tab 后邮件列表重新请求对应文件夹
- [ ] `currentFolder` 全局变量正确更新

---

## 11. P1 — 验证码高亮测试

### TC-VERIF-001：6位数字验证码高亮

**测试目标：** 验证包含6位数字的邮件正文自动高亮验证码

**测试步骤：**
1. 选中含有如下内容的邮件：`"您的验证码是 123456，有效期10分钟"`
2. 点击该邮件查看详情
3. 观察详情区正文

**预期结果：**
- [ ] `123456` 被 `<mark class="verif-code">` 标记包裹
- [ ] 高亮样式醒目（如黄色/主色调背景）
- [ ] 其他非验证码文字不被错误高亮

---

### TC-VERIF-002：4位数字验证码高亮

**测试步骤：**
1. 打开含有 `"PIN: 7890"` 的邮件

**预期结果：**
- [ ] `7890` 被高亮

---

### TC-VERIF-003：无验证码邮件不误判

**测试步骤：**
1. 打开一封普通邮件（如通知邮件，无短数字序列）

**预期结果：**
- [ ] 邮件正文正常显示，无 `<mark>` 高亮元素

---

### TC-VERIF-004：验证码算法 Console 验证

**测试步骤：**
在 Console 执行：
```javascript
const cases = [
  '您的验证码是 123456，请在5分钟内使用',
  'Your OTP: 9876',
  'PIN code: 987654',
  '普通通知邮件，没有特殊数字',
  'Meeting at 10:00 on Dec 25, 2025',
];
cases.forEach(text => {
  const result = extractAndHighlightVerifCode(text);
  const hasHighlight = result.includes('<mark');
  console.log(hasHighlight ? '✅ 有高亮' : '⬜ 无高亮', '->', text.substring(0, 30));
});
```

**预期结果：**
- [ ] 前三条 `有高亮`
- [ ] 后两条 `无高亮`（日期中的数字不被误判）

---

## 12. P1 — 仪表盘页面测试

### TC-DASH-001：仪表盘基本显示

**测试目标：** 验证点击仪表盘导航项后正确显示

**测试步骤：**
1. 点击侧边栏"仪表盘"图标

**预期结果：**
- [ ] `#dashboardPage`（或对应元素）可见
- [ ] `.workspace`（邮件管理三栏）隐藏
- [ ] 顶栏标题更新为"仪表盘"

---

### TC-DASH-002：统计卡片渲染

**测试目标：** 验证仪表盘统计卡片（总账号数/有效Token数等）正确显示

**预期结果：**
- [ ] 显示至少 3 个统计卡片（总账号、有效 Token、需要刷新）
- [ ] 数字正确（与数据库中的账号数一致）
- [ ] 卡片有图标 + 数字 + 说明文字

---

## 13. P1 — 临时邮箱页面测试

### TC-TEMP-001：临时邮箱页面导航

**测试步骤：**
1. 点击侧边栏"临时邮箱"图标

**预期结果：**
- [ ] 切换到临时邮箱相关视图
- [ ] 或者自动选中临时邮箱分组，在账号列表显示临时邮箱

---

### TC-TEMP-002：临时邮箱卡片样式

**前置条件：** 有临时邮箱数据

**预期结果：**
- [ ] 临时邮箱以卡片网格方式显示（`.mailbox-card`）
- [ ] 卡片包含邮箱地址 + 创建时间
- [ ] 有"复制"或"查看"操作按钮

---

## 14. P1 — 全量刷新进度条测试

### TC-PROGRESS-001：进度条显示

**测试目标：** 验证全量刷新时进度条正确显示

**测试步骤：**
1. 点击"全量刷新 Token"按钮
2. 确认弹窗，开始刷新
3. 观察进度条区域

**预期结果：**
- [ ] 顶部出现进度条（`.refresh-progress-bar` 或类似）
- [ ] 进度条颜色为主色（砖红）
- [ ] 显示当前正在刷新的账号邮箱
- [ ] 进度条随账号刷新推进而增长

---

### TC-PROGRESS-002：进度条完成隐藏

**测试目标：** 验证刷新完成后进度条消失

**预期结果：**
- [ ] 所有账号刷新完成后，进度条自动消失（或有完成状态）
- [ ] Toast 提示刷新完成

---

## 15. P0 — 登录页测试

### TC-LOGIN-001：登录页外观

**测试目标：** 验证新登录页视觉符合设计

**测试步骤：**
1. 退出登录
2. 观察登录页

**预期结果：**
- [ ] 页面背景为宣纸米白（亮色）或对应暗色
- [ ] 登录框居中显示（`.auth-card`）
- [ ] 登录框有卡片阴影
- [ ] Logo / 应用名称可见
- [ ] 密码输入框和登录按钮样式符合新设计

---

### TC-LOGIN-002：登录功能正常

**测试步骤：**
1. 在登录页输入正确密码
2. 点击登录按钮

**预期结果：**
- [ ] 成功登录，跳转到主页
- [ ] 主页正常加载分组和账号

---

### TC-LOGIN-003：登录错误提示

**测试步骤：**
1. 输入错误密码
2. 点击登录

**预期结果：**
- [ ] 显示错误提示（红色文字或 Toast）
- [ ] 不跳转

---

## 16. P2 — 移动端响应式测试

### TC-MOBILE-001：768px 宽度布局

**测试步骤：**
1. 打开开发者工具 → 切换设备模式
2. 设置宽度为 768px

**预期结果：**
- [ ] 侧边栏折叠或隐藏
- [ ] 三栏布局自适应（可能合并为单栏）
- [ ] 无内容溢出

---

### TC-MOBILE-002：侧边栏切换（移动端）

**前置条件：** 窗口宽度 <= 768px

**测试步骤：**
1. 点击顶栏的汉堡菜单按钮（☰）
2. 观察侧边栏行为

**预期结果：**
- [ ] 侧边栏从左侧滑出
- [ ] 有半透明遮罩
- [ ] 点击遮罩或关闭按钮收起侧边栏

---

## 17. P0 — 后端功能回归测试

### TC-REGRESSION-001：自动化测试套件

**测试目标：** 确认前端改动不影响后端功能

**测试步骤：**
```bash
# 启动应用
python web_outlook_app.py &

# 等待启动，运行测试套件
python -m unittest discover -s tests -v
```

**预期结果：**
- [ ] 所有测试通过，0 错误，0 失败
- [ ] 测试数量与改动前相同
- [ ] 无新的 `ERROR` 或 `FAIL`

---

### TC-REGRESSION-002：关键 API 手动验收

**测试步骤：**
在 Console 执行 API 检查：
```javascript
// 检查分组 API
fetch('/api/groups').then(r => r.json()).then(d => {
  console.assert(d.success || Array.isArray(d), '分组 API 正常');
  console.log('✅ 分组 API:', d.success ? d.groups.length + '个分组' : d.length + '个分组');
});

// 检查设置 API
fetch('/api/settings').then(r => r.json()).then(d => {
  console.log('✅ 设置 API:', d.success ? '正常' : d);
});
```

**预期结果：**
- [ ] 分组 API 返回正常数据
- [ ] 设置 API 返回正常数据

---

## 18. P0 — JS 兼容性回归测试

### TC-JS-COMPAT-001：核心函数存在性验证

**测试目标：** 验证所有原有全局 JS 函数在新 HTML 中仍然存在

**测试步骤：**
在 Console 执行：
```javascript
const requiredFunctions = [
  'selectAccount', 'loadGroups', 'loadEmails', 'renderEmailList',
  'renderGroupList', 'renderAccountList', 'showEmailDetail',
  'loadAccountsByGroup', 'showAddAccountModal', 'showAddGroupModal',
  'showRefreshModal', 'showExportModal', 'showSettingsModal',
  'logout', 'copyCurrentEmail', 'switchFolder',
  'initCSRFToken'
];
const missing = requiredFunctions.filter(fn => typeof window[fn] !== 'function');
console.log('缺失函数:', missing.length === 0 ? '✅ 全部存在' : '❌ ' + missing.join(', '));
```

**预期结果：**
- [ ] 控制台输出 `缺失函数: ✅ 全部存在`

---

### TC-JS-COMPAT-002：新增函数存在性验证

**测试步骤：**
```javascript
const newFunctions = [
  'navigateTo', 'toggleTheme', 'updateTopbar', 'updateSidebarBadge',
  'showRefreshProgress', 'updateRefreshProgress', 'hideRefreshProgress',
  'stringToColor', 'getTokenStatus', 'extractAndHighlightVerifCode'
];
const missing = newFunctions.filter(fn => typeof window[fn] !== 'function');
console.log('新增函数:', missing.length === 0 ? '✅ 全部存在' : '❌ 缺失：' + missing.join(', '));
```

**预期结果：**
- [ ] 全部新增函数均存在

---

### TC-JS-COMPAT-003：全局状态变量存在性

**测试步骤：**
```javascript
const vars = ['currentAccount', 'currentGroupId', 'currentFolder',
  'currentEmails', 'currentMethod', 'currentSkip', 'hasMoreEmails',
  'groups', 'emailListCache', 'tempEmailGroupId', 'isTempEmailGroup'];
vars.forEach(v => {
  const exists = typeof window[v] !== 'undefined';
  console.log(exists ? '✅' : '❌', v, '=', window[v]);
});
```

**预期结果：**
- [ ] 所有全局状态变量存在，初始值符合预期（null / [] / false 等）

---

### TC-JS-COMPAT-004：完整用户操作流程

**测试目标：** 端到端验证核心业务流程不受 UI 改动影响

**测试步骤：**
1. 登录系统
2. 点击一个分组
3. 验证账号列表加载
4. 点击一个账号
5. 点击"获取邮件"
6. 验证邮件列表加载
7. 点击一封邮件
8. 验证邮件详情显示
9. 点击"已删除" Tab
10. 验证切换文件夹
11. 退出登录

**预期结果：**
- [ ] 每一步都正常执行
- [ ] 无 JS 错误（Console 0 errors）
- [ ] 所有 API 调用返回 200

---

## 19. 测试数据准备

### 19.1 SQL 数据准备脚本

```sql
-- 确保数据库路径: data/outlook_accounts.db
-- 连接: sqlite3 data/outlook_accounts.db

-- 插入测试分组
INSERT OR IGNORE INTO groups (id, name, is_system) VALUES (100, '测试分组A', 0);
INSERT OR IGNORE INTO groups (id, name, is_system) VALUES (101, '测试分组B', 0);

-- 插入测试账号（不同 Token 状态）
-- 1. 有效 Token（远期过期）
INSERT OR IGNORE INTO accounts (email, group_id, token_valid, token_expires_at)
  VALUES ('valid.long@test.com', 100, 1, '2027-06-01T00:00:00');

-- 2. 即将过期（10天后）
INSERT OR IGNORE INTO accounts (email, group_id, token_valid, token_expires_at)
  VALUES ('expiring.soon@test.com', 100, 1, datetime('now', '+10 days'));

-- 3. 已过期
INSERT OR IGNORE INTO accounts (email, group_id, token_valid, token_expires_at)
  VALUES ('expired.token@test.com', 100, 0, '2024-01-01T00:00:00');

-- 4. 有效但无过期时间
INSERT OR IGNORE INTO accounts (email, group_id, token_valid, token_expires_at)
  VALUES ('valid.noexp@test.com', 101, 1, NULL);

-- 5. 状态未知
INSERT OR IGNORE INTO accounts (email, group_id, token_valid, token_expires_at)
  VALUES ('unknown.status@test.com', 101, NULL, NULL);
```

### 19.2 清理测试数据

```sql
-- 测试完成后清理
DELETE FROM accounts WHERE email LIKE '%@test.com';
DELETE FROM groups WHERE id IN (100, 101);
```

---

## 20. 验收标准与门禁

### 20.1 P0 门禁（上线必须通过）

以下测试用例必须全部通过，否则**不允许上线**：

| 测试用例 | 类别 | 关键点 |
|---------|------|-------|
| TC-LAYOUT-001 ~ 004 | 结构 | 新布局骨架 + DOM ID 完整 |
| TC-CSS-001 ~ 002 | CSS | 设计变量颜色正确 |
| TC-THEME-001 ~ 003 | 主题 | 亮暗切换 + 持久化 |
| TC-SIDEBAR-001 ~ 002 | 导航 | 导航切换 + 激活态 |
| TC-ACCOUNT-001 ~ 003 | 账号 | 卡片渲染 + 选中 |
| TC-TOKEN-001 ~ 004 | Token | 四种状态徽章全覆盖 |
| TC-EMAIL-001 ~ 005 | 邮件 | 列表 + 详情 + Tab |
| TC-LOGIN-001 ~ 002 | 登录 | 外观 + 功能 |
| TC-REGRESSION-001 | 回归 | 自动化测试 0 失败 |
| TC-JS-COMPAT-001 ~ 004 | 兼容 | JS 函数 + 完整流程 |

**P0 门禁通过率：100%（不允许 skip 或已知失败）**

### 20.2 P1 门禁（发布 v1.0 前通过）

| 测试用例 | 关键点 |
|---------|-------|
| TC-VERIF-001 ~ 004 | 验证码高亮 |
| TC-DASH-001 ~ 002 | 仪表盘页面 |
| TC-PROGRESS-001 ~ 002 | 刷新进度条 |
| TC-TEMP-001 ~ 002 | 临时邮箱 |
| TC-TOKEN-005 | 算法 Console 验证 |

### 20.3 P2（后续迭代修复）

| 测试用例 | 关键点 |
|---------|-------|
| TC-MOBILE-001 ~ 002 | 移动端响应式 |

### 20.4 最终验收对照表

| 维度 | 期望值 | 验证方式 |
|------|-------|---------|
| Python 后端测试通过率 | 100% | `python -m unittest discover -s tests -v` |
| Console JS 错误数 | 0 | 浏览器开发者工具 Console |
| CSS 变量颜色正确率 | 5/5 | TC-CSS-001 脚本 |
| DOM ID 存在率 | 12/12 | TC-LAYOUT-004 脚本 |
| 全局函数存在率 | 全部 | TC-JS-COMPAT-001/002 脚本 |
| Token 徽章状态覆盖 | 4种全覆盖 | TC-TOKEN-001~004 |
| 主题切换功能 | 双向正常 + 持久化 | TC-THEME-001~003 |
| 完整操作流程 | 无报错 | TC-JS-COMPAT-004 |

---

*文档编号：TEST-00004 | 最后更新：2026-03-01*
