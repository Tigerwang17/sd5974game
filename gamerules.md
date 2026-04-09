
# Goal
请帮我开发一个基于网页（Web）的互动手势解谜游戏。核心玩法是：游戏中的 NPC 角色会说出一段对白，玩家需要根据对白中隐藏的线索做出相应的手势，电脑或手机的摄像头会实时读取玩家的手势并进行判定，答对加分，答错扣分。


# Tech Stack (推荐)
- HTML5, CSS3, JavaScript (ES6+)
- 建议使用原生 JS 或 Vue3 / React（请帮我选择最轻量、最容易在单文件里跑起来的方案）
- **手势识别库**：请使用 MediaPipe Hands 或 tracking.js（强烈推荐 MediaPipe，因为它在浏览器端对原生手势，如剪刀石头布、OK、指点、手掌等的识别非常精准且流畅）。



# Game Logic & Flow
1. **初始化**：页面加载后，展示start.png页面。请求调用用户的摄像头。在界面一侧显示摄像头画面（需要镜像处理，符合人类习惯）。
2. **游戏开始**：在start.png页面展示“开始游戏”按钮。点击“开始游戏”进入到游戏界面。

3. **出题阶段**：
   - character_data中所有角色在背景图中间中随机行走。
   - 每3秒，系统随机从character_data中抽取一个角色和对白。
   - 被抽中角色走到画面中央，在对话框中打字机式输出对白dialogue和dialogue_en。

4. **判定阶段**：
   - 对话输出完，开启倒计时3秒。
   - 摄像头实时捕捉玩家手势。如果玩家在倒计时结束前做出了正确的手势，触发“答对”逻辑。
   - 如果倒计时结束仍未做出正确手势，或做出了错误手势，触发“答错”逻辑。

5. **奖惩逻辑**：
   - 答对：播放成功音效，角色展示correctAction动效。工作量进度条（起始值为10）每答对一次工作量-1；
   - 答错：播放失败音效，角色展示wrongAction动效。压力值进度条（起始值为0）。每答错一次压力值+2；

6. **游戏结束**：
   - 游戏失败：当压力值达到10，则游戏失败。展示弹窗提示：Game Over，点击展示gameover.png,右下角询问是否再来一局，点击重新加载游戏页面。
   - 游戏获胜：当工作值达到0，则游戏获胜。展示弹窗提示：Game Win,点击展示gamewin.png，右下角询问是否再来一局，点击重新加载游戏页面。


# UI/UX Requirements
- **布局**：中央为游戏剧情区（NPC 形象、对话框、倒计时），左侧为红色压力值进度条、蓝色工作量进度条。右下角为摄像头实时画面。
  - 工作量进度条:十个白色文件icon 堆叠，玩家每答对一次则对应从顶部减少一个。
- **视觉反馈**：当识别到玩家做出手势时，在摄像头画面或UI显著位置实时显示“当前检测到的手势：XXX”，方便玩家调试。
- **自适应**：支持 PC 端和手机端浏览器（注意手机端摄像头的权限请求逻辑）。

# Requirements
1. **最新的 JSON 数据结构**：
   请在代码中定义 `CHARACTER_DATA`，每个角色包含以下属性：
   - `chineseName` (中文名)
   - `englishName` (英文名)
   - `baseImage` (常态图片名)
   - `correctAction`: 值为 "action-bounce" (代表答对时的跳跃动作CSS类)
   - `wrongAction`: 值为 "action-shake" (代表答错时的抖动动作CSS类)
   - `dialogues`: 数组，里面包含 10 个对象，每个对象**仅包含**：`dialogue`(中文对白)、`dialogue_en`(英文对白)、`targetGesture`(目标手势)。

2. **CSS 动画实现**：
   请在 CSS 中帮我实现两个动画类：
   - `.action-bounce`：利用 `transform: translateY()` 实现角色图片欢快地上下跳跃（Bounce）效果。
   - `.action-shake`：利用 `transform: translateX()` 实现角色图片快速地左右抖动（Shake）效果，模拟生气的震颤感。

3. **游戏交互逻辑**：
   - 每轮展示 `dialogues` 里的中英文对白。
   - 如果玩家在限时内做对动作：
     给图片加上 `correctAction` 属性对应的 CSS 类（`.action-bounce`）。
     持续 1.5 秒后，恢复 `baseImage` 并移除动画类。
   - 如果玩家答错或超时：
     给图片加上 `wrongAction` 属性对应的 CSS 类（`.action-shake`）。
     持续 1.5 秒后，恢复 `baseImage` 并移除动画类。


#关于手势
请为我引入 fingerpose 库，并基于 MediaPipe 的 Landmarks 帮我定义 7 种 MediaPipe 的原生手势（Victory, Open_Palm, Closed_Fist, Pointing_Up, Thumb_Up, Thumb_Down, ILoveYou）。
另外 1 种请用 fingerpose 的 FingerCurl 帮我自定义定义：OK 手势（大拇指和食指半弯曲，其余三指伸直），对应"targetGesture": "wc"


# Supplemental Requirements 
为了确保游戏的可用性和流畅的交互体验，请在刚才的代码中，务必实装以下两个关键的技术细节：

### 1. 手势防抖与稳定判定逻辑 (Gesture Debouncing & Frame Counter)
* **痛点**：玩家在摄像头前做出手势、或者手势切换时，MediaPipe 的单帧识别可能会出现瞬间的跳变或误判。
* **要求**：请勿使用“单帧触发”的判定逻辑。请实现一个“**持续帧判定机制**”：
  - 设定一个帧数计数器（例如 `requiredFrames = 5`）。
  - 只有当摄像头连续 5 帧（或持续约 200-300 毫秒）都识别出**同一种**目标手势时，才判定该手势有效并触发加分逻辑。
  - 如果中间帧断开或识别为其他手势，计数器重置。以此来保证手势判定的稳定，防止游戏过于敏感。

### 2. 本地开发环境支持 (Local Server Setup)
* **痛点**：现代浏览器出于安全考虑，必须在 `https://` 或 `localhost` 环境下才允许调用 Webcam 摄像头。直接双击 `index.html` 运行会报错。
* **要求**：请在代码输出的最后，为我提供两种最简单的本地运行方案说明：
  - **方案 A（免代码）**：指导我如何在 Cursor 中安装和使用 "Live Server" 插件来一键预览。
  - **方案 B（Node.js 脚本）**：请帮我写一个极简的 `server.js` 静态服务器代码（可以使用 Node 原生 `http` 模块或 `express`），并告诉我如何在终端用 `node server.js` 启动它，以便我能在 `http://localhost:3000` 顺利调试摄像头。



1、获胜条件：在每轮倒计时结束前做出角色要求的正确手势；每答对一次工作量 -1，当工作量降到 0 时游戏胜利。
2、失败条件：若在倒计时内手势错误或超时未作答，则压力值 +2；当压力值达到 10 时游戏失败。
3、手势含义：
👍🏻：领导您说的太对了！
👎🏻：快闭嘴别说了！
🖐🏻：不好意思我要去WC！