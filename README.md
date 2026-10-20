# 太空弹球 · PWA

单文件弹球游戏 + PWA 外壳，目标设备是 iPad（竖屏 9:16）。

## 文件

- `index.html`：入口页，立即跳到 `太空弹球.html`（避免复制 2000 行游戏代码）。
- `太空弹球.html`：游戏本体，也是唯一需要改的文件。
- `manifest.webmanifest`：PWA 配置（standalone、竖屏、图标、主题色）。
- `sw.js`：离线缓存。改游戏后把 `CACHE` 的版本号 +1。
- `icons/`：图标。`icon.svg` 和 `icon-maskable.svg` 是源文件；
  `icon-180.png`、`icon-192.png`、`icon-512.png`、`icon-maskable-512.png` 需要先生成。
- `README.md`：本文件。

## 先生成图标

用**桌面浏览器**打开 `icons/make-icons.html`，依次点 4 个按钮，会下载 4 个 PNG。
把它们放进 `icons/`，文件名保持不变：

```
icon-180.png
icon-192.png
icon-512.png
icon-maskable-512.png
```

没有 PNG 也能安装，只是 iPad 主屏图标会变成网页截图；Chrome 桌面要求 192/512，
先生成更稳。

也可以命令行（ImageMagick）：

```bash
magick icons/icon.svg -resize 180x180 icons/icon-180.png
magick icons/icon.svg -resize 192x192 icons/icon-192.png
magick icons/icon.svg -resize 512x512 icons/icon-512.png
magick icons/icon-maskable.svg -resize 512x512 icons/icon-maskable-512.png
```

或者用 `rsvg-convert -w 180 -h 180 icons/icon.svg -o icons/icon-180.png`。

## 部署（必须 HTTPS）

三选一：

- **Netlify Drop**：打开 https://app.netlify.com/drop ，把整个文件夹拖进去，拿到 HTTPS 网址。
- **GitHub Pages**：推到仓库 → Settings → Pages → Deploy from branch。
- **Cloudflare Pages**：连仓库或直接上传。

国内访问 GitHub Pages / Netlify 可能慢，可以换腾讯云 COS、阿里云 OSS 静态托管
（需要实名，自定义域名需要备案）。

## 本地测试

在文件夹里执行：

```bash
py -m http.server 8000
```

电脑浏览器打开 `http://localhost:8000/`，Service Worker 可以注册。
iPad 用局域网 IP（`http://192.168.x.x:8000`）只能测玩法，
Service Worker 需要 HTTPS 或 localhost，不会生效。

## iPad 安装

1. iPad Safari 打开部署后的网址（根地址或 `太空弹球.html` 都可以，根地址会自动跳）。
2. 点分享按钮 → **添加到主屏幕**。
3. 从主屏打开，就是独立窗口；首次打开后断网也能玩。

## 已知限制

- iOS 没有真正的全屏，状态栏和小白条还在；已用安全区避开。
- 网页不能锁方向；横屏会显示“请把 iPad 竖过来”，可以点掉。
- iPad 是 3:4，台面是 9:16，左右会有黑边，这是正常的；
  想铺满可以再改成 3:4 画布 + 9:16 台面 + 两侧面板。
- 侧边静音开关可能把 WebAudio 静音；玩的时候拨到响铃。
- Safari 可能清站点数据，最高分可能丢失。
- Service Worker 只在 HTTPS / localhost 生效；直接双击 `太空弹球.html` 仍能玩，
  只是没有离线缓存。

## 更新游戏

改完 `太空弹球.html` 后，把 `sw.js` 里的 `space-pinball-v1` 改成 `space-pinball-v2`，
再部署；iPad 上重开一次即可拿到新版本。

## 想改成真正的 index.html

如果希望入口本身就是游戏，而不是跳转页：

1. 把 `太空弹球.html` 改名为 `index.html`，删掉现在的跳转页。
2. `manifest.webmanifest` 的 `start_url` 改成 `"./index.html"`。
3. `sw.js` 的 `CORE` 里把 `"./太空弹球.html"` 改成 `"./index.html"`。
