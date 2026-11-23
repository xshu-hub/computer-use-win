# Windows Computer Use MCP

Windows 桌面操控的独立 MCP 服务和配套 Agent Skill。服务通过标准 MCP stdio 提供窗口查询、UI Automation、截图、OCR、输入、窗口管理和可选的浏览器操作。工具沿用 `windows_computer_use_*` 命名。

## 运行环境

- 运行服务的机器必须是 Windows，使用 Node.js 22 或更高版本和 Windows PowerShell 5.1。
- Windows 桌面操作需要服务进程能访问已登录用户的交互式桌面。MCP 客户端通过 stdio 启动该进程。
- 浏览器通道会使用机器上安装的受支持浏览器。Windows 原生应用的 UI Automation 和截图可直接使用。

## 安装 MCP

从 GitHub 克隆源码后，在仓库根目录运行 `npm.cmd pack`，即可生成下面使用的安装包。

在 PowerShell 中运行以下命令。将安装目录和压缩包路径换成机器上的实际路径。安装目录可以按需指定。

```powershell
npm.cmd install --prefix "C:\Tools\WinCU" --ignore-scripts --no-audit --no-fund "C:\Downloads\windows-computer-use-mcp-0.1.2.tgz"
```

在采用 `mcpServers` 格式的客户端中配置以下内容。其他客户端使用其对应的本地 stdio 配置，沿用同样的命令和参数。如果客户端进程的 PATH 里没有 Node.js，将 `node` 换成 `node.exe` 的绝对路径。

```json
{
  "mcpServers": {
    "wincu": {
      "command": "node",
      "args": [
        "C:\\Tools\\WinCU\\node_modules\\windows-computer-use-mcp\\mcp\\server.mjs"
      ]
    }
  }
}
```

从源码运行时，把参数换成源码里的 `mcp/server.mjs` 绝对路径。服务本身没有第三方 Node.js 运行依赖。连接后调用 `windows_computer_use_health`，检查 `ok` 与 `inputDesktopAvailable`。

## 安装 Skill

把仓库里的 [`skills/windows-computer-use`](https://github.com/xshu-hub/computer-use-win/tree/main/skills/windows-computer-use) 文件夹放到目标 Agent 的 Skills 目录。使用 Skill 压缩包时，先解压再复制同名文件夹。校验路径最终为 `windows-computer-use/SKILL.md`。Skill 按 [Agent Skills 规范](https://agentskills.io/specification) 编写。仅支持 MCP 的客户端可直接发现并调用工具。

## 常用配置

环境变量由 MCP 客户端在启动服务时传入。

网页任务默认优先走浏览器通道。Chromium 专用浏览器按需打开可见窗口，并保留专用配置中的网站登录。默认跟随 Windows 的 Chrome 或 Edge 设置，必要时从已安装的 Chrome 或 Edge 中选择。连接个人浏览器需要单独启用。

| 环境变量 | 用法 |
| --- | --- |
| `WCU_TOOL_MODE` | `full` 是默认值。客户端工具上下文有限时可用 `lite`。使用 `lazy` 时，客户端需要支持工具列表更新或主动刷新。 |
| `WCU_ENABLED_TOOLS` | JSON 数组，使用不带前缀的工具名，例如 `["health","list_windows","screenshot"]`。 |
| `WCU_BROWSER_LANE` | 设为 `off` 可关闭浏览器通道。 |
| `WCU_BROWSER_APP` | 可填 `default`、`edge`、`chrome` 或实验性的 `firefox`。自动启动的 Firefox 使用无界面模式。 |
| `WCU_BROWSER_HEADLESS` | 设为 `1` 可隐藏 Chromium 专用浏览器窗口。 |
| `WCU_BROWSER_SIGNIN` | 设为 `0` 可改用临时浏览器配置。 |
| `WCU_SETTINGS_JSON` | JSON 字符串。设置 `browserFirst` 为 `false` 可改变给 Agent 的路由提示。 |
| `WCU_IMAGE_MODE` | 默认通过 MCP 返回 PNG 图像。设为 `file` 时返回 Windows 上的绝对路径，需要客户端另行读取。 |

[工具工作流](docs/wiki/mcp-tools.md) · [权限和急停](docs/wiki/safety.md)

## 来源和许可

本项目采用 MIT 许可证。版权声明和上游来源见 [LICENSE](LICENSE) 和 [THIRD_PARTY.md](THIRD_PARTY.md)。
