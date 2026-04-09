# 5974 Gesture Game

基于摄像头的职场主题手势小游戏：根据角色对白在倒计时内做出正确手势，控制「工作量」与「压力值」，看能否撑到下班。

## 玩法摘要

- **胜利**：每轮在倒计时结束前做出题目要求的手势；每答对一次工作量 −1，工作量降到 **0** 时通关。
- **失败**：手势错误或超时未作答，压力值 **+2**；压力值达到 **10** 时失败。
- **手势含义**（与游戏内说明一致）  
  - 👍🏻：领导您说的太对了！  
  - 👎🏻：你快闭嘴别说了！  
  - 🖐🏻：Sorry 我要去洗手间，下次再聊！

更完整的规则说明见游戏内「规则说明」或仓库中的 `gamedescription`、`gamerules.md`。

## 运行要求

- **现代浏览器**（推荐最新版 Chrome / Edge）。
- **摄像头权限**：游戏需要通过 `getUserMedia` 采集画面做手势识别。
- **必须通过 HTTP(S) 服务打开**，不要直接用 `file://` 双击 HTML。  
  原因：浏览器对本地文件的 `fetch`、摄像头、部分 Web API 限制较严；`localhost` 或 **HTTPS**（如 GitHub Pages）下体验正常。

## 本地运行

1. 确保仓库根目录下包含入口页 **`index.html`**，以及 `index.html` 引用的资源（如 `character_data.json`、`characters/`、`vendor/` 下的模型与 WASM 等）。
2. 任选一种静态服务方式，**以仓库根目录为网站根** 启动，例如：
   - VS Code / Cursor 的 **Live Server**（打开根目录后右键 `index.html` → Open with Live Server）。
   - 使用任意静态服务器，例如：  
     `npx --yes serve .`  
     然后在浏览器中打开提示的地址，进入 **`/index.html`** 或站点根路径（若已配置默认文档为 `index.html`）。

3. 仓库内附带极简 Node 静态服务 **`server.js`**（默认端口 `3000`，根路径自动打开 `index.html`）。

```bash
node server.js
```

## 在线仓库与 GitHub Pages

- 源码仓库：<https://github.com/Tigerwang17/sd5974game>
- 开启 Pages 后，游戏地址一般为：  
  `https://tigerwang17.github.io/sd5974game/`  
  （以 GitHub **Settings → Pages** 里显示的 URL 为准。）

## 部署到 GitHub Pages

1. 将本仓库推送到 GitHub（见下文「首次推送」）。
2. 在仓库 **Settings → Pages** 中，选择分支与目录（通常为 `main` 与 `/ (root)`）。
3. 站点入口使用根目录的 **`index.html`**，这样访问 `https://<用户名>.github.io/<仓库名>/` 即可。
4. 注意：**路径区分大小写**，且需把 `character_data.json`、`vendor/models/` 下的 `.task` 模型等资源一并提交（若使用 Git LFS，需单独配置）。

## 项目结构（主要文件）

| 路径 | 说明 |
|------|------|
| `index.html` | 游戏主页面（入口） |
| `character_data.json` | 角色、对白、目标手势等数据 |
| `characters/` | 角色立绘等资源 |
| `vendor/` | MediaPipe WASM、手势相关脚本；`vendor/models/` 为运行时加载的模型文件 |
| `server.js` | 可选的本地静态文件服务 |
| `gamedescription` / `gamerules.md` | 规则与说明文案参考 |

## 自定义内容

- 修改 **`character_data.json`** 可调整角色名称、对白、英文对白、以及每句对应的 `targetGesture`（如 `Thumb_Up`、`Thumb_Down`、`Open_Palm`）。
- 游戏规则弹窗与部分 HUD 文案在 **`index.html`** 内，可按需改动。

## 技术说明（简述）

- 使用 **MediaPipe Tasks Vision**（手部 / 手势）在浏览器端做识别，并结合页面内逻辑判定胜负与 UI 反馈。
- 需联网加载部分 **CDN** 上的 ES 模块（若离线部署，需自行改为本地托管对应包）。

## 许可

若仓库未单独声明许可证，默认以仓库所有者为准；使用第三方库时请遵守其各自许可证。
