# 🏥 病娇AI查岗系统 · 后端（Railway）

接收 iPhone 快捷指令上报的 App 打开/关闭事件，计算每个 App 的使用时长，并暴露给 MCP 代理层查询。

## 接口

| 方法 | 路径 | 说明 |
|------|------|------|
| `POST` | `/report` | 接收 App 打开/关闭上报，需 `Authorization: Bearer <AUTH_TOKEN>` |
| `GET` | `/activity/summary` | 返回最近打开的 App 和各 App 累计使用时长 |
| `GET` | `/ping` | 健康检查，返回 `pong` |

### `/report` 请求体

```json
{"app_name": "微信", "event": "open"}
```

`event` 取值：`open`（打开）/ `close`（关闭）。

## 部署（Railway）

1. 导入本仓库：Railway → New Project → Deploy from GitHub repo。
2. 添加环境变量：

   | 变量 | 说明 |
   |------|------|
   | `AUTH_TOKEN` | 你自己设的访问密码（务必设置，否则上报会被拒） |

3. 部署完成后拿到域名，形如 `https://你的项目名.up.railway.app`。

## 本地运行

```bash
pip install -r requirements.txt
AUTH_TOKEN=你的密码 uvicorn app:app --port 8000
```

---

配套 MCP 代理层见仓库 [ai-check-in-mcp](https://github.com/ytn35297-afk/ai-check-in-mcp)。
