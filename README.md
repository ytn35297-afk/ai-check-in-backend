# 🏥 病娇AI查岗系统 · 后端（Railway）

接收 App 打开/关闭事件上报，计算每个 App 的使用时长，并暴露给 MCP 代理层查询。

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

## 📱 客户端上报（安卓 · MacroDroid）

安卓用 **MacroDroid**（Play 商店免费）替代 iOS 快捷指令，监听 App 打开/关闭后 POST 到后端。

### 准备工作

1. Play 商店安装 MacroDroid。
2. 首次打开按提示授予「无障碍」和「使用情况访问」（Usage Access）权限——没有这些监听不到 App 打开/关闭。

### 宏①：打开 App 时上报

1. MacroDroid → 右上角 `+` 新建宏。
2. **触发器（Trigger）**：选 `应用已打开`（Application Opened）→ 勾选要监控的 App（可全选）。
3. **动作（Action）**：选 `HTTP 请求`（HTTP Request）：
   - **URL**：`https://你的Railway域名/report`
   - **方法**：`POST`
   - **请求头**：加一行 `Authorization: Bearer 你的密码`
   - **请求体**（Body）：`{"app_name":"微信","event":"open"}`
4. 保存。

### 宏②：关闭 App 时上报

同上，但触发器选 `应用已关闭`（Application Closed），请求体改为：

```json
{"app_name":"微信","event":"close"}
```

两个宏都建好，后端才能算出每个 App 用了多久。若想区分不同 App，手动把每个监控 App 各建一对宏、`app_name` 填对应名字即可（懒的话全用同一个字符串也能统计总量）。

---

配套 MCP 代理层见仓库 [ai-check-in-mcp](https://github.com/ytn35297-afk/ai-check-in-mcp)。
