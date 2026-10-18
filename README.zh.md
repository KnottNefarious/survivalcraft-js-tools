
[English](README.md) | **简体中文** | [한국어](README.ko.md) | [Español](README.es.md)

# survivalcraft-js-tools
单文件 HTML 工具，可通过游戏内置的 JavaScript 远程控制，在 Survivalcraft API 1.9.3.1 中运行 JavaScript 并传送。

# Survivalcraft JS 运行器与传送器

一个单文件 HTML 工具，用于在 Android 上通过游戏内置的 JavaScript 远程控制服务器，对 **Survivalcraft API 1.9.3.1** 运行 JavaScript。

它提供真正的文本编辑器（无字符限制）、实用命令预设下拉菜单，以及一键传送功能。

---

## 功能

- **JS 运行器标签页** — 编写或粘贴游戏 API 接受的任意 JavaScript，然后发送并查看结果。
- **预设下拉菜单** — 从预加载的命令中选择，配有通俗易懂的说明。选中后会把代码载入编辑器，你可以查看、修改或直接运行。
- **传送器标签页** — 输入 X/Y/Z 即可传送。还包括微调按钮、"我在哪？"以及保存/前往出生点。

---

## 环境要求

- **Survivalcraft API 1.9.3.1**（Android）。Play 商店的原版不包含 JavaScript 远程控制。
- **Chrome for Android**（或基于 Chromium 的浏览器）。
- 游戏必须**已打开并加载了世界**，且**远程控制已开启**。
- 运行脚本时，游戏必须保持在**前台**或**分屏**状态。如果 Android 暂停了游戏，其服务器就会失效。

---

## 平台支持

本工具在 **Android** 上构建并测试。底层协议（向 `127.0.0.1:7765` 发送 HTTP POST）并非 Android 专属，但游戏和浏览器必须运行在**同一台设备**上，因为 `127.0.0.1` 指向的是浏览器所在设备的回环地址。

| 平台 | 状态 | 说明 |
|---|---|---|
| Android | ✅ 已测试 | 需要分屏，让游戏保持在前台 |
| Windows / Linux | ✅ 应该可用 | 在同一台 PC 上运行游戏和 Chrome；无需分屏 |
| macOS | ⚠️ 未测试 | 仅当有 API 版本和 JS 远程控制时可用 |
| iOS | ⚠️ 未测试 | JS 远程最初就是作为 iOS 的变通方案加入的，所以可能可用 |
| 手机浏览器 → PC 游戏 | ❌ 不可用 | `127.0.0.1` 无法跨设备 |

### 如果你在 PC 上

你不需要这个 HTML 文件。使用 `curl`、Python 的 `requests` 或任何 HTTP 客户端直接向游戏服务器发送 POST 请求：

```
curl -X POST http://127.0.0.1:7765/ -H "password: YOUR_PASSWORD" -d 'findSubsystem("Players").PlayersData.Count'
```

这个 HTML 工具之所以存在，主要是因为手机上很少装有好用的 HTTP 客户端，而且在手机终端里输入长段 JavaScript 很痛苦。

## 设置

### 1. 在游戏中开启远程控制

- 启动 Survivalcraft API 并加载一个世界。
- 打开游戏内菜单。
- 找到 **JavaScript Remote Control**。
- 将其切换为 **开启**。
- 记下屏幕上显示的**端口**（默认 `7765`）和**密码**。

### 2. 在非 root 设备上启用 Chrome 命令行

- 打开 Chrome → `chrome://flags`。
- 搜索 **"Enable command line on non-rooted devices"**。
- 设为 **Enabled** 并重启 Chrome。

### 3. 创建 Chrome 命令行文件

该文件会关闭 Chrome 的 Web 安全限制，使其能与游戏的本地服务器通信。

你需要一种在手机上执行 shell 命令的方式。**aShell You** 配合 **Shizuku** 可行，**PC 上的 ADB** 也可以。

运行以下命令（原样）：

```bash
echo "chrome --disable-web-security --user-data-dir=/data/user/0/com.android.chrome/app_chrome/Default" > /data/local/tmp/chrome-command-line
```

4. 强制停止并重新打开 Chrome

设置 → 应用 → Chrome → 强行停止。然后重新打开 Chrome。

5. 验证标志已生效

在 Chrome 中访问 chrome://version。在 Command Line 下，你应该看到：

```
chrome --disable-web-security --user-data-dir=...
```

如果出现了，就准备好了。

6. 将 Chrome 与游戏分屏

如果你切换应用，Android 会挂起游戏，远程控制服务器随之失效。把 Chrome 和 Survivalcraft 设为分屏，让两者都保持运行。

---

使用工具

1. 在 Chrome 中打开 survivalcraft-js-runner.html（从"下载"文件夹或你保存的位置）。
2. 确认顶部的密码和端口与游戏显示的一致。
3. JS 运行器标签页 — 从下拉菜单选择预设，或输入你自己的代码，然后点 Run。
4. 传送器标签页 — 输入 X/Y/Z 然后点 Teleport。

输出框显示游戏返回的原始 JSON。

---

## 工作原理

整个环境都在你的手机上本地运行，不经过互联网。

```
[你的手机]
     │
     ├── Survivalcraft API（在分屏的一半中运行）
     │        └── 内置 HTTP 服务器位于 127.0.0.1:7765
     │             （接受带 "password" 头和 JS 正文的 POST）
     │
     └── Chrome（在分屏的另一半中运行）
              └── teleport.html（从你的"下载"文件夹加载）
                   └── fetch() 向 127.0.0.1:7765 发送 HTTP POST
                        └── 服务器运行 JS，返回 JSON 结果
```

每次你在 HTML 页面中点 **Run** 或 **Teleport**：

1. Chrome 构建一个 HTTP POST，密码放在请求头里，JavaScript 作为正文。
2. 请求发往 `127.0.0.1:7765` — 你自己手机的回环地址。它从不离开设备。
3. Survivalcraft 的内置服务器接收请求，验证密码，把 JS 交给 Jint 引擎。
4. Jint 在游戏进程中运行代码，可访问 C# 对象（`findSubsystem`、`ComponentBody` 等）。
5. 服务器返回 JSON 响应，Chrome 在输出框中显示。

`--disable-web-security` 标志之所以存在，只是因为 Chrome 通常不允许 `file://` 或 `content://` 页面调用 `http://127.0.0.1`。移除该标志会重新启用拦截，工具会停止工作，直到你重新应用。

已知限制

这些是游戏 API 的限制，不是工具的限制：

· 仅支持一次性脚本。在这个版本中，帧处理器会注册但从不触发，所以不会有任何东西在后台自动运行。每个操作都需要你点 Run。
· 读取某些属性会让游戏死锁。读取某些属性（生命值、时间、天空、天气、部分组件）会冻结脚本引擎。发生时你必须强制停止游戏才能恢复。
· 没有队列或超时。如果脚本卡住，服务器会一直返回 "There is already a script running"，直到你重启游戏。
· 无渲染。你无法用 JavaScript 绘制 HUD、覆盖层或小地图。
· 无自定义按键绑定。原因同上——处理器不会触发。

可靠可用的功能

· 读取玩家位置、速度、名称、等级、生命值、出生点
· 写入位置（传送）和速度（弹射、停止）
· 写入出生点
· 枚举子系统、实体和 API 对象

---

故障排查

"Invalid password"
请求头名称区分大小写。试试 password、Password 或 PASSWORD。同时确认游戏菜单中显示的密码没有变化。

"There is already a script running"
之前的脚本让引擎死锁了。强制停止游戏、重新打开、加载世界、重新开启远程控制。其他方法都清不掉——刷新页面、重启 Chrome 都不行。

输出框显示 "FETCH FAILED"
通常意味着游戏没有处于分屏状态，已被挂起。把它重新与 Chrome 分屏后再试。

传送可用但读取属性会卡住
有些属性读取不安全。坚持使用 Position、Velocity、Name、Level 和 Health。如果其中任何一个卡住，就从脚本中去掉。

游戏完全冻结
从 设置 → 应用 → Survivalcraft → 强行停止。

---

分享

此工具无法托管在 GitHub Pages（或任何 HTTPS 网站）上。浏览器会阻止 HTTPS 页面调用 http://127.0.0.1，即使不考虑这个问题，CORS 也会阻止它。

预期用法是：

1. 下载 HTML 文件。
2. 在已禁用 Web 安全的 Chrome 中本地打开它。
3. 对同一设备上运行的游戏使用。（必须分屏）

这就是为什么它以单文件形式分发，而不是托管页面。

---
