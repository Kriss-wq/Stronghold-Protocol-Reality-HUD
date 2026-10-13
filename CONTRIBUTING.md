# 参与贡献 / Contributing

欢迎提 Issue 和 PR。这个 mod 的定位是**轻量、无依赖、零侵入**，希望所有改动都守住这三条。

## 开发环境

```bash
git clone https://github.com/xianshi3/reality-hud.git
cd reality-hud
npm install                      # 只有 devDependency（puppeteer-core），mod 本体无任何依赖
```

`hud.js` / `hud.css` 就是全部运行时代码，没有构建步骤——改完直接刷新游戏页面即可看到效果。

## 跑测试

```bash
cd ../Stronghold-Protocol        # 一个游戏仓库的检出
node server/index.js             # 本地起服，默认 http://127.0.0.1:3000
cd ../reality-hud
npm test                         # = node test/hud-smoke.mjs [URL]
# Windows 以外请先设置 CHROME_PATH 指向 Chrome 可执行文件
```

冒烟脚本用 puppeteer 驱动真实页面：34 项断言覆盖挂载 / 折叠 / 菜单 / 邀请栏 / 模拟邀请横幅 / 页面零异常。改动 HUD 逻辑后请保持全绿。

## 代码约定

- 只使用浏览器公开 API 与游戏公开接口（`globalThis.__SP__`：`store` / `net` / `data` / `version`），不碰游戏内部实现；
- 零运行时依赖、零打包器、原生 ESM；
- 渲染遵守每帧预算：批量读写、合并 rAF、隐藏 / 暂停时停掉一切定时器；
- 界面文案以中文为主（游戏本身的语言），关键注释可中英混排；
- 服务端帧要做容错：`room.state` 等推送可能缺字段，用可选链，别让一帧异常拖垮整个渲染循环。

## 提 PR

- 一个 PR 只做一件事，说明动机与截图（UI 改动务必附图）；
- 涉及版本号变更时同步 `mod.json`、`hud.js` 的 `MOD_VERSION` 与 `CHANGELOG.md`；
- 大改动（新分区、新协议消息）建议先开 Issue 讨论。

## 许可

贡献即表示同意以 [MIT](LICENSE) 授权你的代码（与本仓库一致）。游戏本体是 GPL-3.0 的粉丝作品，本 mod 是独立衍生作品，**不要提交任何游戏代码或游戏素材**。
