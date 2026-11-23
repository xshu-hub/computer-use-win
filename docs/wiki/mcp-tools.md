# Windows Computer Use tools

Use the MCP `tools/list` response for the schemas, enabled actions, and limits of this server instance. The server's tool names start with `windows_computer_use_`.

- `health` checks the backend and reports `inputDesktopAvailable`. `list_windows` identifies windows. Pass `nativeWindowHandle` in later desktop calls to select the intended window.
- `accessibility_tree` and `find` expose UI Automation controls. Use `element_info` for a single control. A stale control ID calls for a fresh observation of the same window.
- `screenshot` returns inline PNG content by default. `snapshot` combines an observation with optional image content. `ocr` reads visible text when an app exposes few accessible controls. Use the returned image bounds and scale to convert image pixels to screen coordinates.
- `invoke`, `set_value`, and element-targeted `click` can act through the control. Some apps still need foreground input. Screen-coordinate input should use a fresh, verified window observation.
- `wait_for` can confirm a visible change. A successful action with `verification: "sent"` means the action was dispatched. Inspect the expected result before repeating it.
- `batch` accepts planned input steps and can stop on errors or changed UI. Review completed steps before going further.
- For a web task, prefer the browser lane when available. `set_value` with `elementId: "new-tab"` and a URL starts the agent browser on demand. By default the Chromium window is visible and its dedicated profile retains site sign-ins. The returned tab ID is the `browserTab` target for subsequent page calls. Browser element IDs and page pixels are scoped to the page.
