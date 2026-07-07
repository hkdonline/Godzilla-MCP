# Godzilla-MCP
哥斯拉 MCP

改版于：https://github.com/cns1rius/godzilla-mcp

使用方式：

1、添加插件

<img width="760" height="459" alt="image" src="https://github.com/user-attachments/assets/3b5e29b3-c5a1-4dfe-8dad-bf52e3b7f9e7" />

2、启用MCP

<img width="488" height="128" alt="image" src="https://github.com/user-attachments/assets/356bbc17-6176-4d3d-bdce-ee581a00e39a" />

3、配置

····
{
  "mcpServers": {
    "godzilla": {
      "url": "http://127.0.0.1:5566/mcp"
    }
  }
}
····

4、当前支持操作：

<img width="1074" height="545" alt="image" src="https://github.com/user-attachments/assets/b750c0b7-3028-4293-8b2a-7c396592ea5b" />

5、和AI对话操作shell

<img width="1170" height="661" alt="image" src="https://github.com/user-attachments/assets/fc116082-5e27-4cf1-9a9e-2d72b8a4ba04" />
<img width="1185" height="680" alt="image" src="https://github.com/user-attachments/assets/374575e8-0e16-4a6a-91c3-6c86428988a6" />

00：哥斯拉目录下有日志可以排错。

<img width="589" height="193" alt="image" src="https://github.com/user-attachments/assets/ef4c9167-5d9e-49f9-b073-f734b7ad0cb2" />




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
