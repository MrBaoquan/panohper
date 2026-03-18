<div align="center">

# panohper

**krpano 全景交互组件库**

轻量 · 自包含 · 即插即用

[![krpano](https://img.shields.io/badge/krpano-v1.20.x-blue?style=flat-square)](https://krpano.com)
[![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

</div>

---

panohper（**pano** + **h**ot**p**oint + help**er**）是一组适用于 [krpano](https://krpano.com) v1.20.x 的通用全景交互组件，封装了 **图文弹窗**、**视频播放器**、**滑动提示** 及 **精灵动画** 等展览场景中高频使用的功能。

**自包含设计** — 组件 XML、插件 JS、图标资源全部封装在同一目录内，通过 git submodule 嵌入项目即可使用，开发项目与沉淀组件同步进行。

## ✨ 特性

| | |
|---|---|
| 📸 **图文弹窗** | 支持横向 / 纵向可滚动大图，自动适配屏幕，可叠加子热点 |
| 🎬 **视频播放** | 全屏遮罩播放，移动端兼容（iOS 手动触发），自适应尺寸 |
| 💡 **滑动提示** | 图片超出可视区自动提示 "可上下/左右滑动查看"，3 s 后淡出 |
| 🎞️ **精灵动画** | 通用精灵图帧动画引擎，用于热点图标、加载指示器等 |
| 🔒 **无侵入** | 全部 action 使用 `scope="private:*"` 私有作用域，不污染全局命名空间 |
| 📦 **自包含** | XML + JS 插件 + 图标资源一个目录搞定，submodule 即插即用 |

## 📂 目录结构

```
panohper/                     ← git submodule，克隆到 vtour/panohper/
├── popup.xml                 # 图文弹窗组件
├── videoplayer.xml           # 视频播放器组件
├── tips.xml                  # 滑动提示组件
├── utils.xml                 # 精灵动画 & 工具
├── assets/                   # 组件内置资源（图标/按钮/加载动画）
│   ├── animate/
│   │   ├── DJQQ.png          #   图文热点图标（精灵图）
│   │   ├── Xsdj.png          #   子热点图标
│   │   └── SPTB.png          #   视频热点图标
│   ├── common/
│   │   └── loadinganimation.png
│   └── ui/
│       ├── Gb01.png           #   关闭按钮
│       └── BF.png             #   播放按钮 (iOS)
├── plugins/                   # 依赖的 krpano 插件
│   ├── pp_blur.js             #   背景高斯模糊
│   ├── scrollarea.js          #   可滚动区域
│   └── videoplayer.js         #   视频播放
├── example/                   # 使用示例
│   ├── tour.xml
│   └── assets/
├── README.md
└── LICENSE
```

## 📦 安装

### 推荐：Git Submodule（开发与沉淀同步）

```bash
cd your-project/vtour
git submodule add https://github.com/MrBaoquan/panohper.git panohper
```

项目结构变为：

```
vtour/
├── tour.xml
├── panohper/          ← git submodule（panohper 仓库）
│   ├── popup.xml
│   ├── assets/
│   ├── plugins/
│   └── ...
├── assets/            ← 项目自己的素材（展板图、视频等）
├── plugins/           ← 项目自己的插件（gyro、webvr 等）
├── skin/
└── panos/
```

**开发工作流：**

```bash
# 在项目中修改 panohper 组件
vim vtour/panohper/popup.xml

# 提交 panohper 改动
cd vtour/panohper
git add -A && git commit -m "fix: ..."
git push

# 回到项目提交 submodule 指针更新
cd ../..
git add vtour/panohper
git commit -m "chore: update panohper"

# 其他项目拉取最新 panohper
cd other-project/vtour/panohper
git pull origin main
```

### 替代：直接复制

将仓库内容复制到 `vtour/panohper/` 即可。

## 🚀 快速开始

在 `tour.xml` 顶部添加：

```xml
<krpano version="1.20.11" title="Virtual Tour">

    <include url="panohper/utils.xml" />
    <include url="panohper/popup.xml"/>
    <include url="panohper/videoplayer.xml"/>
    <include url="panohper/tips.xml"/>

    <!-- 你的 skin 和场景 ... -->
</krpano>
```

> ⚠️ **加载顺序**：`utils.xml` 必须在 `popup.xml` / `videoplayer.xml` **之前**引入。

然后在 `<scene>` 中添加热点：

```xml
<!-- 图文弹窗热点 -->
<hotspot name="my_board"
    url="panohper/assets/animate/DJQQ.png"
    onloaded="do_crop_animation(64,64,60);"
    ath="10" atv="-5"
    onclick="popup('p1', 'assets/hall/exhibit.jpg', 'h');"
/>

<!-- 视频热点 -->
<hotspot name="my_video"
    url="panohper/assets/animate/SPTB.png"
    onloaded="do_crop_animation(64,64,60);"
    ath="30" atv="-8" scale="0.5"
    onclick="playvideo(get(videourl), get(posterurl)); tween(alpha,0);"
    onvideoclosed="tween(alpha,1);"
    videourl="assets/videos/intro.mp4"
    posterurl=""
/>
```

> **路径约定**：
> - 热点图标 `url` → `panohper/assets/animate/XXX.png`（组件内置资源）
> - 弹窗内容图 / 视频 → `assets/XXX`（项目自身素材）

完整示例见 [`example/tour.xml`](example/tour.xml)。

## 📖 API 参考

### `popup.xml` — 图文弹窗

```
popup(pid, image_url, scroll_dir)
popup_close()
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `pid` | string | 弹窗实例 ID，同 ID 复用图层 |
| `image_url` | string | 图片路径（相对于 tour 根目录） |
| `scroll_dir` | `'h'` \| `'v'` | 横向 / 纵向滚动 |

**回调**：`onpopuped`（图片展示后）、`onpopupclosed`（关闭后）

---

### `videoplayer.xml` — 视频播放器

```
playvideo(videourl, posterurl)
closevideo()
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `videourl` | string | 视频路径 |
| `posterurl` | string | 封面图路径（可留空 `""`） |

**回调**：`onvideoclosed`（播放器关闭后）

---

### `tips.xml` — 滑动提示

```
showtip(tip_id)     // 'h' 或 'v'
hidetip()
```

> `popup.xml` 内部会自动调用，通常无需手动使用。

---

### `utils.xml` — 精灵动画

```
do_crop_animation(framewidth, frameheight, framerate)
play_animation(framewidth, frameheight, framerate, loop)
```

## 🧩 进阶用法

### 展板 + 子热点叠加

```xml
<hotspot name="board"
    url="panohper/assets/animate/DJQQ.png"
    onloaded="do_crop_animation(64,64,60);"
    ath="-45" atv="-5"
    onclick="popup('p1', 'assets/hall/exhibit.jpg', 'h');"
    onpopuped="set(global.layer[c_spots],parent='scrollarea#p1',visible=true);"
/>

<layer name="c_spots" type="container" width="100%" height="100%"
    visible="false" bgalpha="0.0">
    <layer name="spot_a" type="container" bgalpha="0.0"
        x="calc:(300*100/1000)+'%'" y="calc:(120*100/500)+'%'"
        width="calc:(200*100/1000)+'%'" height="100"
        align="leftop" bgcapture="true"
        onclick="callwith(layer[spot_a_icon],onclick)">
        <layer name="spot_a_icon"
            url="panohper/assets/animate/Xsdj.png"
            onloaded="do_crop_animation(64,64,60);"
            edge="lefttop" align="right"
            onclick="popup('p2', 'assets/hall/detail.jpg', 'v');"
        />
    </layer>
</layer>
```

### 文字标签热点

```xml
<hotspot name="label"
    type="text" ath="200" atv="-8"
    html="&lt;b&gt;标题&lt;/b&gt;&lt;br/&gt;描述"
    css="color:#FFF; font-size:14px; text-align:center;"
    bg="true" bgcolor="0x2D3E50" bgalpha="0.8" bgroundedge="6"
    padding="8" zoom="false" distorted="false"
    onclick="popup('p1', 'assets/hall/detail.jpg', 'v');"
/>
```

## ⚠️ 注意事项

<details>
<summary><b>1. <code>&lt;action&gt;</code> 内禁止 XML 注释</b></summary>

krpano 将 `<action>` 内容当作脚本处理，写 `<!-- -->` 会导致解析失败。
</details>

<details>
<summary><b>2. 条件用 <code>EQ</code> / <code>GT</code> / <code>LT</code></b></summary>

```
✅ if(get(x) EQ null, ...);
❌ if(get(x) == null, ...);
```
</details>

<details>
<summary><b>3. 私有作用域中用 <code>global.layer[name]</code></b></summary>

```
✅ tween(global.layer[h_tip].alpha, 1.0);
❌ tween(layer[h_tip].alpha, 1.0);
```
</details>

<details>
<summary><b>4. 隐藏 text layer 用 <code>alpha</code> 不用 <code>scale</code></b></summary>

```
✅ set(global.layer[tip].alpha, 0);
❌ set(global.layer[tip].scale, 0);
```
</details>

<details>
<summary><b>5. HTML 实体用 XML 数字引用</b></summary>

```
✅ &#215;   &#160;
❌ &times;  &nbsp;
```
</details>

<details>
<summary><b>6. <code>pp_blur</code> 插件已由 popup.xml 声明，不要重复声明</b></summary>

`videoplayer.xml` 共享同一实例，无需额外 `<plugin>`。
</details>

## 🤝 贡献

欢迎 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/amazing`)
3. 提交更改 (`git commit -m 'feat: add amazing feature'`)
4. 推送分支 (`git push origin feature/amazing`)
5. 创建 Pull Request

## 📄 License

[MIT](LICENSE) © panohper contributors