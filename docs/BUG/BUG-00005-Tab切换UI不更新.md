# BUG-00005 — 收件箱/垃圾邮件 Tab 切换 UI 不更新

> 关联 PRD: PRD-00004 UI全局美化重设计
> 创建日期: 2026-03-01
> 状态: 修复中

## 问题清单

### BUG-023: 收件箱/垃圾邮件文件夹 Tab 切换时 active 状态不更新 (HIGH)

**现象描述**:
在邮箱管理页面，选中一个账号后，右侧邮件列顶部显示"📨 收件箱 | ⚠️ 垃圾邮件"两个 Tab 按钮。点击切换 Tab 时，虽然邮件数据会切换，但**Tab 按钮的 active 样式不会跟随切换**，始终停留在初始状态。

**根因分析**:
HTML 模板中的 Tab 按钮使用 CSS 类名 `.email-tab`:
```html
<button class="email-tab active" data-folder="inbox">📨 收件箱</button>
<button class="email-tab" data-folder="junkemail">⚠️ 垃圾邮件</button>
```

但 JavaScript 中查找的是 `.folder-tab`（不匹配！）:
- `main.js` 第 497 行: `document.querySelectorAll('.folder-tab')` → 找不到任何元素
- `accounts.js` 第 28 行: `document.querySelectorAll('.folder-tab')` → 找不到任何元素

CSS 样式定义是正确的 `.email-tab.active`，但由于 JS 查找的选择器错误，active 类永远无法切换。

**修复方案**: 将 JS 中所有 `.folder-tab` 替换为 `.email-tab`
- `static/js/main.js` `switchFolder()` 函数
- `static/js/features/accounts.js` `selectAccount()` 函数

**状态**: ✅ 已修复 — 4 处 `.folder-tab` → `.email-tab`（main.js ×2, accounts.js ×1, emails.js ×1）
