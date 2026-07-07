# Changelog

## v1.0.12 (2026-07-07)

### 新增

- **Payload 缓存自动重试** — 操作失败时自动清除缓存 Payload 重新握手一次，无需手动重启服务即可恢复过期会话

### 清理

- **移除 AUTH_TOKEN** — 删除启动弹窗中的鉴权 Token 字段，无需接口认证


---

## v1.0.11 (2026-07-06)

### 修复

- **exec_command 统一 Base64 编码** — `exec_command` 命令输出也走 `toSafeBase64()` 包装，防止日文/韩文等非 UTF-8 目标系统输出炸 Gson 序列化（与 `list_files`、`read_file` 保持一致的全量编码防护）

---

## v1.0.10 (2026-07-06)

### 修复

- **全量 NPE 防护** — 新增 `requireParam()` 统一参数校验，AI 缺参数时返回明确错误信息而非 NPE
- **堆栈日志** — MCP 和 SSE 所有异常路径打印完整堆栈

---

## v1.0.9 (2026-07-06)

### 修复

- **NPE 日志安全** — 所有 `e.getMessage()` 改为 `stringifyError(e)`，NPE 时显示类名而非 null
- **list_files 乱码防护** — 目录列表结果统一 Base64 编码返回，避免非 UTF-8 文件名炸 Gson
- **read_file 空值保护** — `fileContent` 为 null 时不调用 `.getBytes()`

---

## v1.0.8 (2026-07-06)

### 修复

- **DB 查找修复** — `Db.getOneShell(url)` 改为先遍历 DB 匹配 URL，再用 ID 加载（Godzilla 按 ID 而非 URL 查找）

---

## v1.0.7 (2026-07-06)

### 修复

- **http/https 兼容** — `getOrInitPayload` 精确匹配失败时自动尝试互换协议匹配
- **错误日志** — SSE 和直接 MCP 路径的异常不再静默吞咽，全部输出到日志文件
- **null 排查** — `Db.getOneShell()` 返回 null 时列出 DB 中全部 URL 帮助排查

---

## v1.0.6 (2026-07-06)

### 日志文件

- **文件日志** — 日志从 `System.out` 改为写入 `godzilla-mcp.log`（哥斯拉无控制台，文件日志才是可见的）
- **双请求握手** — 初始化后双重 `getBasicsInfo`：第一次加密握手，第二次验证连通性
- **全链路日志** — 每次操作记录 action、targetUrl、结果预览

---

## v1.0.5 (2026-07-06)

### 日志 & 调测

- **双请求握手** — `getOrInitPayload` 初始化后发送两次 `getBasicsInfo`：第一次建立加密会话（忽略空回包），第二次获取真实数据验证连通性
- **全链路控制台日志** — 每个关键步骤输出 `[MCP]` 前缀日志：Shell 信息、Payload 类型、握手回包、验证回包、操作结果
- **`executeOnTarget` 日志** — 记录 action、targetUrl 和结果预览

---

## v1.0.4 (2026-07-06)

### 修复

- **回滚连通性测试** — 去掉 1.0.3 加入的 `getBasicsInfo()` 连通性验证，因其与 Godzilla 加密握手协议冲突（第一次请求用于握手返回空 `[]`，导致误判为连接失败）
- **回滚 EDT 调度** — 去掉 `SwingUtilities.invokeAndWait()`，`initShellOpertion()` 直接在当前线程调用
- **启动弹窗显示版本号** — `MCP 服务启动成功！v1.0.4`
- **默认端口变更** — `8888` → `5566`

### v1.0.3 已知问题

连通性测试在 Godzilla 加密握手完成前发送请求，导致握手回包（空 `[]`）被误判为失败，所有目标操作永远无法执行。

---

## v1.0.3 (2026-07-06)

### Payload 初始化修复

- **EDT 线程初始化** — `initShellOpertion()` 改为通过 `SwingUtilities.invokeAndWait()` 在 Swing EDT 上执行
- **连通性验证** — 初始化后立即调用 `getBasicsInfo()` 验证连通性
- **详细日志** — 各阶段输出 `[MCP]` 前缀日志

### 已知问题 **(v1.0.3)**

连通性测试与 Godzilla 加密握手冲突，导致操作返回空 `[]`。**请升级到 v1.0.4。**

---

## v1.0.2 (2026-07-06)

### MCP SSE 传输层

- **SSE 传输支持** — 实现标准 MCP SSE (Server-Sent Events) 传输协议，Claude Desktop 等 MCP 客户端可正常连接
- **GET /mcp** — 建立 SSE 长连接，分配 sessionId 并发送 `endpoint` 事件
- **POST /mcp?sessionId=xxx** — 带 sessionId 的 JSON-RPC 请求通过 SSE 通道推送响应
- **新增 /sse 端点** — 兼容硬编码 `/sse` 路径的客户端
- **兼容模式保留** — POST 不带 sessionId 仍走直接 HTTP JSON 响应

### 修复

- 修复 MCP 客户端连接报 `fetch failed` 的问题（原因为缺少 SSE 传输支持）

---

## v1.0.1 (2026-07-06)

### 架构升级

- **协议改造：标准 MCP JSON-RPC 2.0**
  - 将自定义 REST API 升级为 MCP (Model Context Protocol) 标准协议
  - 新增 `/mcp` 端点，兼容 Claude Desktop、Cursor 等 MCP 客户端
  - 保留 `/api/agent_router` 端点，向后兼容旧版调用
  - 自动检测请求格式：MCP JSON-RPC vs 旧版自定义 JSON

- **新增 MCP 方法**
  - `initialize` — 协议握手，返回协议版本 `2024-11-05` 和能力声明
  - `tools/list` — 返回 9 个工具的完整 JSON Schema 定义
  - `tools/call` — 调用工具并返回标准 `content[]` 格式
  - `resources/list` — 将 Webshell 连接暴露为 MCP 资源
  - `notifications/initialized` — 客户端就绪通知

### 新增功能

- **退出按钮** — 新增"停止 AI Agent 自动化引擎"菜单项，支持安全关闭 MCP 服务
- **文件读取** — 新增 `read_file` 工具，读取靶机文件并 Base64 编码返回
- **CORS 支持** — 添加跨域头，支持浏览器端 MCP 客户端调用

### 变更

- **关闭 Auth 认证** — 因 MCP 客户端不支持自定义 Authorization Header，移除 Bearer Token 校验
- **工具重命名** — MCP 工具名采用 `snake_case` 规范（如 `getEnvConfig` → `get_env_config`）
- **Gson 兼容性** — 修复 Gson 2.10.1 中 `JsonObject` 为 `final` 类导致的编译错误

### 工具列表 (v1.0.1)

| 工具 | 类型 | 说明 |
|---|---|---|
| `list_shells` | 资产管理 | 列出所有已保存的 Webshell |
| `add_shell` | 资产管理 | 添加新的 Webshell 连接 |
| `get_env_config` | 资产管理 | 获取支持的 Payload/加密方式 |
| `get_basics_info` | 靶机操作 | 获取靶机系统信息 |
| `exec_command` | 靶机操作 | 执行系统命令 |
| `list_files` | 靶机操作 | 列出目录内容 |
| `read_file` | 靶机操作 | 读取文件内容 (Base64) |
| `upload_file` | 靶机操作 | 上传文件到靶机 |
| `exec_sql` | 靶机操作 | 通过隧道执行 SQL |

### Bug 修复

- 修复 `buildStringProp`/`buildNumberProp` 方法签名错误导致编译失败
- 修复 `new JsonObject() {{ }}` 匿名子类化在 Gson 2.10.1 中失败
- 修复 `executeOnTarget()` varargs 传 `null` 的潜在 NPE 风险

---

## v0.1.0 (初始版本)

- 基础 HTTP REST API 服务器
- 6 个自定义 action：getEnvConfig, listShells, addShell, getBasicsInfo, execCommand, listFile, uploadFile, execSql
- GUI 菜单启动服务器
- Bearer Token 鉴权
