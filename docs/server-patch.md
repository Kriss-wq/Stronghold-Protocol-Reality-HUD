# 服务端补丁（可选）：在线名单 + 一键邀请

Reality HUD 的核心功能（延迟、服务状态、本局、性能、日志、**免密钥邀请链接**）**不需要任何服务端改动**，在原版 Stronghold Protocol 上即装即用。

本补丁只是可选增强：给服务器加上「在线名单」`GET /api/players` 与「定向邀请」`room.invite` / `room.invited`，让 HUD 能列出服务器上所有在线玩家并一键邀请。

## 应用

补丁基于上游 [Stronghold Protocol](https://github.com/sganggs/Stronghold-Protocol) **v0.2.6** 生成（`server-patch/reality-hud-invite.patch`，共 4 个代码文件 + 1 个文档）：

```bash
cd Stronghold-Protocol            # 游戏仓库，v0.2.6
git apply reality-hud-invite.patch
# 验证后重启服务器使新代码生效：
systemctl restart stronghold      # 会断开所有连接、结束内存中的对局，请挑空闲时段
curl -s http://127.0.0.1:3000/api/players   # 应返回 {"ok":true,"players":[...]}
```

反向撤销：`git apply -R reality-hud-invite.patch`。

## 补丁内容

| 文件 | 改动 |
|---|---|
| `shared/protocol.js` | C2S 增加 `room.invite { playerId }`；S2C 增加 `room.invited { from, name, code }`（文档表） |
| `server/lobby.js` | `invite()` 处理器：发送者必须在自己的房间里（否则 `NOT_IN_ROOM` / 观战席 `SPECTATOR`）；目标必须是另一个在线会话（未知 / 离线 / 本人 / 已在同房间 → `BAD_TARGET`）；通过后把 `{ t:'room.invited', from, name, code }` 定向推给目标 |
| `server/net.js` | `room.invite` 加入重桶（`HEAVY_TYPES`，2/s、突发 6）防刷邀请 |
| `server/http/routes.js` | `GET /api/players`：返回 `{ ok, players: [{ id, name, inRoom, inMatch }] }`，永不缓存 |
| `docs/design/network.md` | 协议文档同步 |

## 安全设计

- **房间码不进名单**：房间码是进房钥匙；`/api/players` 只含昵称、`playerId` 与状态布尔值，不含房间码、token、地址。房间码只经服务端定向推给被邀请的会话本人。
- **限流**：`room.invite` 走每连接重桶（与 `room.loadout` 等同级），刷不出每秒几十条邀请。
- **向后兼容**：原版客户端收到 `room.invited` 会当作未知推送静默忽略（客户端 `_emit` 直接透传给监听器，无监听器即无副作用）；不打补丁的服务器上，HUD 检测到 `/api/players` 404 后自动隐藏名单并停止请求。

## 验证

- `node --test test/lobby-invite.test.js` —— 推送 relay、全部拒绝路径、限流、名单不含密钥（5 例）
- `node hud-smoke.mjs http://127.0.0.1:3000` —— HUD 侧 40 项断言（含模拟 `room.invited` 横幅与旧服务器降级路径）
