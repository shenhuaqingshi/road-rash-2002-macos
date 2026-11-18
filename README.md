# 暴力摩托 2002 · Road Rash (macOS Apple Silicon 原生优化版)

基于 1996/1997 PC 原版《Road Rash》（国内俗称《暴力摩托 2002》）逆向工程开源项目深度定制优化的 **macOS Apple Silicon (`arm64`) 原生 C++20 / SDL3 客户端**。无需 Wine、无需 Rosetta 2 转译，原生支持 Retina 高分屏与高刷屏。

## 本版定制与优化亮点

1. **Retina 高清原生光栅化**：
   - 修复 macOS Retina 物理像素识别（`SDL_GetWindowSizeInPixels`），默认开启 `HiRes`、`HiResBikers`、`render_scale = 2`（约 960p 内部光栅化，M1 上稳定 60 fps）与 `Widescreen::match`（原生宽屏视野无黑边）。
   - 开启 3D 透视校正贴图（`perspective = true`）与高刷插帧（`interpolate = true`），采用 `SDL_SCALEMODE_PIXELART` 锐利像素采样，默认关闭高速模糊。
   - **画面虚影修复**：早期版本默认 `render_scale = 3`（1440p），在 Apple M1 上软件光栅化每帧约 24 ms，60 Hz 垂直同步下只有约 41 fps，画面在 1 帧 / 2 帧推进之间来回跳，高速行驶时出现明显虚影（重影）。现默认改为 `2`，实测稳定 60 fps、帧步进均匀。已安装旧版的用户请在设置中将渲染倍率改为 2，或编辑 `~/Library/Application Support/RoadRashNative/RoadRash/settings.txt` 中的 `render_scale=2`；M3/M4 等更强芯片可自行尝试 3。
2. **运行内存（RAM）与体积精简**：
   - 移除 `CourseCanvas` 与 `PostChain` 冗余缓冲区预分配，默认走零膨胀 8-bit 索引高清光栅化管线，赛后自动回收 RGBA Mipmap 缓存。
   - 开启 `-Wl,-dead_strip` 死代码消除与符号剥离，独立打包 `.app` 仅约 **10 MB**。
3. **顺手街机攻击键位（`WASD` / 方向键 + `J` `K` `L`）**：
   - `W / S` 或 `↑ / ↓`：加速 / 刹车
   - `A / D` 或 `← / →`：左右转向
   - **`J`**（或 `Z`）：**挥拳 / 空手夺武器 (Punch)**
   - **`K`**（或 `X`）：**重脚猛踹 (Kick)**
   - **`L`**（或 `C`）：**反手重击 / 抡棍链 (Backhand)**
   - **`N` / `左 Shift`**（或双击油门）：**氮气加速 (Nitro)**
   - 内置亚帧按键锁存（防止高刷屏快速点按漏键）及 `SDL_StopTextInput`（防止中文拼音输入法拦截字母键）。
4. **增强原盘导入器**：兼容 ISO-9660 下划线与连字符文件名变体，支持一键导入原版 `ROADRASH.ISO`（341 文件 SHA-256 校验）。

## 快速下载与本地编译

可直接前往本仓库 **[Releases](../../releases)** 下载预编译好的 `RoadRash2002-macOS-arm64.zip` 解压运行（首次启动选择原版 `ROADRASH.ISO` 即可完成导入）。

如需从源码编译：

```bash
cmake -S . -B build-release -DCMAKE_BUILD_TYPE=Release -DRR_BUNDLE_SDL=ON -DBUILD_TESTING=OFF
cmake --build build-release --target rr-game rr-import --parallel
```
