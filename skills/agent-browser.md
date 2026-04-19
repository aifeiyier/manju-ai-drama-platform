# Browser Automation

> SOLO 适配文档 | 原始项目：agent-browser | 版本 1.0.0

---

## 技能概述

| 项目 | 内容 |
|------|------|
| **技能名称** | Browser Automation |
| **原始来源** | manju/agent-browser |
| **核心定位** | 基于 Rust 的高性能无头浏览器自动化 CLI |
| **适用平台** | SOLO Agent 环境 |

---

## 触发词

当用户提到以下关键词时，应激活此技能：

| 类别 | 触发词 |
|------|--------|
| 浏览器操作 | 浏览器自动化、网页操作、打开网页 |
| 网页抓取 | 网页抓取、抓取网页、提取网页内容 |
| 表单操作 | 自动填表、自动填写、表单提交 |
| 网页测试 | 网页测试、自动化测试、UI测试 |
| 网页交互 | 点击按钮、填写输入框、网页截图 |
| 网页导航 | 导航到、打开网站、访问网页 |

---

## 核心工作流：Snapshot-First

> 原则：先了解页面状态，再执行操作；每次操作后验证结果。

### 三步工作法

1. **快照感知（Snapshot First）** - 每次导航或 DOM 变化后，立即执行 `snapshot -i`
2. **定位与操作（Locate & Act）** - 从快照中选择合适的 ref 或使用语义定位器
3. **验证与迭代（Verify & Iterate）** - 操作后立即验证，如需要重新 snapshot

---

## 命令说明

### 导航

| 命令 | 说明 |
|------|------|
| `open <url>` | 导航到 URL |
| `back` | 后退 |
| `forward` | 前进 |
| `reload` | 刷新页面 |
| `close` | 关闭浏览器 |

### 快照（页面分析）

| 命令 | 说明 |
|------|------|
| `snapshot` | 完整无障碍树 |
| `snapshot -i` | 仅可交互元素（推荐） |
| `snapshot -c` | 紧凑输出 |
| `snapshot -d 3` | 限制深度为 3 |
| `snapshot -s "#main"` | 限定 CSS 选择器范围 |

### 交互操作（使用快照中的 @ref）

| 命令 | 说明 |
|------|------|
| `click @e1` | 点击 |
| `dblclick @e1` | 双击 |
| `focus @e1` | 聚焦元素 |
| `fill @e2 "text"` | 清空并输入 |
| `type @e2 "text"` | 不清空直接输入 |
| `press Enter` | 按键 |
| `press Control+a` | 组合键 |
| `hover @e1` | 悬停 |
| `check @e1` | 勾选复选框 |
| `uncheck @e1` | 取消勾选 |
| `select @e1 "value"` | 选择下拉菜单 |
| `scroll down 500` | 滚动页面 |
| `scrollintoview @e1` | 滚动元素到视图 |
| `drag @e1 @e2` | 拖放 |
| `upload @e1 file.pdf` | 上传文件 |

### 获取信息

| 命令 | 说明 |
|------|------|
| `get text @e1` | 获取元素文本 |
| `get html @e1` | 获取 innerHTML |
| `get value @e1` | 获取输入值 |
| `get attr @e1 href` | 获取属性 |
| `get title` | 获取页面标题 |
| `get url` | 获取当前 URL |
| `get count ".item"` | 计数匹配元素 |
| `get box @e1` | 获取边界框 |

### 检查状态

| 命令 | 说明 |
|------|------|
| `is visible @e1` | 检查是否可见 |
| `is enabled @e1` | 检查是否可用 |
| `is checked @e1` | 检查是否勾选 |

### 截图与 PDF

| 命令 | 说明 |
|------|------|
| `screenshot` | 截图到标准输出 |
| `screenshot path.png` | 保存到文件 |
| `screenshot --full` | 全页截图 |
| `pdf output.pdf` | 保存为 PDF |

### 视频录制

| 命令 | 说明 |
|------|------|
| `record start ./demo.webm` | 开始录制 |
| `record stop` | 停止并保存 |
| `record restart ./take2.webm` | 停止当前+开始新录制 |

### 等待

| 命令 | 说明 |
|------|------|
| `wait @e1` | 等待元素出现 |
| `wait 2000` | 等待毫秒数 |
| `wait --text "Success"` | 等待文本出现 |
| `wait --url "/dashboard"` | 等待 URL 匹配 |
| `wait --load networkidle` | 等待网络空闲 |
| `wait --fn "window.ready"` | 等待 JS 条件 |

### 鼠标控制

| 命令 | 说明 |
|------|------|
| `mouse move 100 200` | 移动鼠标 |
| `mouse down left` | 按下按钮 |
| `mouse up left` | 释放按钮 |
| `mouse wheel 100` | 滚轮 |

### 语义定位器（ref 的替代方案）

| 命令 | 说明 |
|------|------|
| `find role button click --name "Submit"` | 按角色查找并点击 |
| `find text "Sign In" click` | 按文本查找并点击 |
| `find label "Email" fill "user@test.com"` | 按标签查找并填写 |
| `find first ".item" click` | 查找第一个匹配项 |
| `find nth 2 "a" text` | 查找第N个匹配项 |

### 浏览器设置

| 命令 | 说明 |
|------|------|
| `set viewport 1920 1080` | 设置视口大小 |
| `set device "iPhone 14"` | 模拟设备 |
| `set geo 37.7749 -122.4194` | 设置地理位置 |
| `set offline on` | 切换离线模式 |
| `set headers '{"X-Key":"v"}'` | 额外 HTTP 头 |
| `set credentials user pass` | HTTP 基本认证 |
| `set media dark` | 模拟暗色模式 |

### Cookie 与存储

| 命令 | 说明 |
|------|------|
| `cookies` | 获取所有 Cookie |
| `cookies set name value` | 设置 Cookie |
| `cookies clear` | 清除 Cookie |
| `storage local` | 获取 localStorage |
| `storage local key` | 获取特定键 |
| `storage local set k v` | 设置值 |
| `storage local clear` | 清除所有 |

### 网络

| 命令 | 说明 |
|------|------|
| `network route <url>` | 拦截请求 |
| `network route <url> --abort` | 阻止请求 |
| `network route <url> --body '{}'` | 模拟响应 |
| `network unroute [url]` | 移除路由 |
| `network requests` | 查看跟踪的请求 |

### 标签页与窗口

| 命令 | 说明 |
|------|------|
| `tab` | 列出标签页 |
| `tab new [url]` | 新标签页 |
| `tab 2` | 切换到标签页 |
| `tab close` | 关闭标签页 |
| `window new` | 新窗口 |

### 框架

| 命令 | 说明 |
|------|------|
| `frame "#iframe"` | 切换到 iframe |
| `frame main` | 回到主框架 |

### 对话框

| 命令 | 说明 |
|------|------|
| `dialog accept [text]` | 接受对话框 |
| `dialog dismiss` | 关闭对话框 |

### JavaScript

| 命令 | 说明 |
|------|------|
| `eval "document.title"` | 执行 JavaScript |

### 状态管理

| 命令 | 说明 |
|------|------|
| `state save auth.json` | 保存会话状态 |
| `state load auth.json` | 加载已保存状态 |

### 调试

| 命令 | 说明 |
|------|------|
| `open example.com --headed` | 显示浏览器窗口 |
| `console` | 查看控制台消息 |
| `console --clear` | 清除控制台 |
| `errors` | 查看页面错误 |
| `errors --clear` | 清除错误 |
| `highlight @e1` | 高亮元素 |
| `trace start` | 开始录制追踪 |
| `trace stop trace.zip` | 停止并保存追踪 |

### 选项

| 选项 | 说明 |
|------|------|
| `--session <name>` | 使用隔离的会话 |
| `--json` | 提供 JSON 输出 |
| `--full` | 全页截图 |
| `--headed` | 显示浏览器窗口 |
| `--timeout` | 设置命令超时（毫秒） |
| `--cdp <port>` | 通过 Chrome DevTools Protocol 连接 |

---

## 表单提交示例

```bash
# 1. 打开表单页面
agent-browser open https://example.com/form

# 2. 获取初始快照
agent-browser snapshot -i
# 输出: textbox "Email" [ref=e1], textbox "Password" [ref=e2], button "Submit" [ref=e3]

# 3. 填写表单
agent-browser fill @e1 "user@example.com"
agent-browser fill @e2 "password123"

# 4. 点击提交
agent-browser click @e3

# 5. 等待页面加载
agent-browser wait --load networkidle

# 6. 验证结果
agent-browser snapshot -i
```

---

## 认证示例

```bash
# 首次登录
agent-browser open https://app.example.com/login
agent-browser snapshot -i
agent-browser fill @e1 "username"
agent-browser fill @e2 "password"
agent-browser click @e3
agent-browser wait --url "/dashboard"
agent-browser state save auth.json

# 后续会话：加载已保存状态
agent-browser state load auth.json
agent-browser open https://app.example.com/dashboard
```

---

## 反模式清单

### 绝对不要这样做

| 错误做法 | 问题说明 | 正确做法 |
|---------|---------|---------|
| 不 snapshot 就直接点击元素 | 元素 ref 可能不存在或已过期 | 每次操作前先执行 `snapshot -i` |
| 用过期的 ref 操作新页面 | 导航后 ref 会全部失效 | 每次导航后立即重新 snapshot |
| 不等待就直接操作 | 页面还在加载，元素还没出现 | 操作前使用 `wait` 命令等待 |
| 用 `type` 代替 `fill` 填充表单 | 输入框可能已有内容，导致重复 | 表单输入优先使用 `fill` |
| 不设置超时时间 | 操作可能无限等待卡住 | 根据网络情况设置合理的 `--timeout` |
| 在生产环境录制视频 | 视频文件会占用大量磁盘空间 | 仅在调试时使用 `record` |
| 不检查元素可见性就点击 | 元素可能被遮挡或隐藏 | 操作前用 `is visible` 检查 |
| 同时打开太多 session | 系统资源消耗过大 | 合理控制 session 数量 |
| 不保存状态就关闭浏览器 | 登录态、Cookie 都会丢失 | 需要保留状态时用 `state save` |

### 常见错误

1. 不要忘记 snapshot 后再操作：ref 是页面加载时生成的
2. 不要在 iframe 外操作 iframe 内元素：需要先用 `frame` 切换
3. 不要跳过 wait 命令：网络延迟、动态加载都需要时间
4. 不要在生产环境用 --headed：无头模式性能更好
5. 不要忽略 console 和 errors：先看日志和错误信息
6. 不要用绝对坐标鼠标操作：不同屏幕尺寸坐标不同
7. 不要同时操作多个标签页不切换：操作前确保用 `tab` 切换
8. 不要在没有验证的情况下继续：每个关键操作后验证结果

---

## 约束与校准

### 必须做的（Hard Constraints）
- 每次导航后必须立即 snapshot
- 操作前必须检查元素是否存在/可见
- 操作后必须验证结果
- 表单输入优先用 fill 而非 type
- 动态元素必须先 wait 再操作

### 推荐做的（Soft Constraints）
- 调试时尽量用 --headed 模式
- 复杂操作考虑用 trace 记录
- 需要保留状态时用 state save/load
- 有问题时先看 console 和 errors
- 保持 session 数量合理，不用时及时释放

---

## 关键思维要点

1. **Ref 的时效性**：ref 随页面加载而变，导航后必须重新 snapshot
2. **等待的艺术**：宁愿多等1秒，不要盲目重试3次
3. **验证优先**：每次操作后先验证结果，再继续下一步
4. **调试三板斧**：--headed 看现象，console/errors 看原因，snapshot 看元素
5. **状态管理**：需要保留登录态时，及时 save/load state
6. **不要假设**：不要假设元素一定存在，每次都检查
