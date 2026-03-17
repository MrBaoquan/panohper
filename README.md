<div align="center">

# 🌐 panohper

**krpano 全景交互组件库**

轻量 · 开箱即用 · 无侵入

[![krpano](https://img.shields.io/badge/krpano-v1.20.x-blue?style=flat-square)](https://krpano.com)
[![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

</div>

---

panohper（**pano** + **h**ot**p**oint + help**er**）是一组适用于 [krpano](https://krpano.com) v1.20.x 的通用全景交互组件，封装了 **图文弹窗**、**视频播放器**、**滑动提示** 及 **精灵动画** 等展览场景中高频使用的功能。

只需在 `tour.xml` 中 `<include>` 引入，配合简短的 `onclick` 即可工作——无需修改 skin 或 krpano 内核。

## ✨ 特性

| | |
|---|---|
| 📸 **图文弹窗** | 支持横向 / 纵向可滚动大图，自动适配屏幕，可叠加子热点 |
| 🎬 **视频播放** | 全屏遮罩播放，移动端兼容（iOS 手动触发），自适应尺寸 |
| 💡 **滑动提示** | 图片超出可视区自动提示 "可上下/左右滑动查看"，3 s 后淡出 |
| 🎞️ **精灵动画** | 通用精灵图帧动画引擎，用于热点图标、加载指示器等 |
| 🔒 **无侵入** | 全部 action 使用 `scope="private:*"` 私有作用域，不污染全局命名空间 |

## 📦 安装

### 方式 A — 直接复制

将以下目录复制到你的 krpano 项目 `vtour/` 下：

```
vtour/
├── panohper/          ← 复制 src/ 目录并重命名为 panohper/
├── assets/            ← 复制 assets/ 目录
└── plugins/           ← 复制 plugins/ 目录（或合并到已有 plugins）
```

### 方式 B — Git Submodule

```bash
cd your-project/vtour
git submodule add https://github.com/YOUR_USERNAME/panohper.git panohper-repo
# 然后建立符号链接或在构建脚本中复制文件
```

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

然后在任意 `<scene>` 中添加热点：

```xml
<!-- 图文弹窗热点 -->
<hotspot name="my_board"
    url="assets/animate/DJQQ.png"
    onloaded="do_crop_animation(64,64,60);"
    ath="10" atv="-5"
    onclick="popup('p1', 'assets/hall/exhibit.jpg', 'h');"
/>

<!-- 视频热点 -->
<hotspot name="my_video"
    url="assets/animate/SPTB.png"
    onloaded="do_crop_animation(64,64,60);"
    ath="30" atv="-8" scale="0.5"
    onclick="playvideo(get(videourl), get(posterurl)); tween(alpha,0);"
    onvideoclosed="tween(alpha,1);"
    videourl="assets/videos/intro.mp4"
    posterurl=""
/>
```

完整示例见 [`example/tour.xml`](example/tour.xml)。

## 📂 目录结构

```
panohper/
├── src/                        # 核心组件 XML
│   ├── popup.xml               #   图文弹窗
│   ├── videoplayer.xml         #   视频播放器
│   ├── tips.xml                #   滑动提示
│   └── utils.xml               #   精灵动画 & 工具
├── assets/                     # 必需资源
│   ├── animate/                #   热点动画精灵图
│   │   ├── DJQQ.png            #     图文热点图标
│   │   ├── Xsdj.png            #     子热点图标
│   │   └── SPTB.png            #     视频热点图标
│   ├── common/
│   │   └── loadinganimation.png #    加载动画
│   └── ui/
│       ├── Gb01.png            #     关闭按钮
│       └── BF.png              #     播放按钮 (iOS)
├── plugins/                    # 依赖的 krpano 插件
│   ├── pp_blur.js              #   背景高斯模糊
│   ├── scrollarea.js           #   可滚动区域
│   └── videoplayer.js          #   视频播放
├── example/                    # 使用示例
│   ├── tour.xml                #   示例 tour 配置
│   └── assets/                 #   示例素材
├── README.md
├── LICENSE
└── .gitignore
```

## 📖 API 参考

### `popup.xml` — 图文弹窗

```
popup(pid, image_url, scroll_dir)
popup_close()
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `pid` | string | 弹窗实例 ID，同 ID 复用图层 |
| `image_url` | string | 图片路径 |
| `scroll_dir` | `'h'` \| `'v'` | 横向 / 纵向滚动 |

**回调**：在调用热点上设置 `onpopuped`（图片展示后）、`onpopupclosed`（关闭后）。

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

**回调**：在调用热点上设置 `onvideoclosed`（播放器关闭后）。

---

### `tips.xml` — 滑动提示

```
showtip(tip_id)
hidetip()
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `tip_id` | `'h'` \| `'v'` | 左右 / 上下滑动提示 |

> `popup.xml` 内部会自动调用，通常无需手动使用。

---

### `utils.xml` — 精灵动画

```
do_crop_animation(framewidth, frameheight, framerate)
play_animation(framewidth, frameheight, framerate, loop)
```

| 参数 | 类型 | 说明 |
|------|------|------|
| `framewidth` | number | 单帧宽度 (px) |
| `frameheight` | number | 单帧高度 (px) |
| `framerate` | number | 帧率 (fps) |
| `loop` | boolean | 是否循环 |

## 🧩 进阶用法

### 展板 + 子热点叠加

在展板弹窗内的特定位置叠加可点击的子热点：

```xml
<hotspot name="board"
    url="assets/animate/DJQQ.png"
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
            url="assets/animate/Xsdj.png"
            onloaded="do_crop_animation(64,64,60);"
            edge="lefttop" align="right"
            onclick="popup('p2', 'assets/hall/detail.jpg', 'v');"
        />
    </layer>
</layer>
```

**原理**：`onpopuped` 回调将子热点层的 `parent` 设为滚动区域 `'scrollarea#p1'`，使子热点随展板图同步滚动。坐标使用 `calc:(px*100/total)+'%'` 百分比定位。

### 文字标签热点

```xml
<hotspot name="label"
    type="text"
    ath="200" atv="-8"
    html="&lt;b&gt;标题&lt;/b&gt;&lt;br/&gt;描述文字"
    css="color:#FFF; font-size:14px; text-align:center;"
    bg="true" bgcolor="0x2D3E50" bgalpha="0.8" bgroundedge="6"
    padding="8" zoom="false" distorted="false"
    onclick="popup('p1', 'path/to/detail.jpg', 'v');"
/>
```

## ⚠️ 注意事项

<details>
<summary><b>1. <code>&lt;action&gt;</code> 内禁止 XML 注释</b></summary>

krpano 将 `<action>` 内容当作脚本处理，写 `<!-- -->` 会导致解析失败：

```xml
<!-- ✅ 正确 — 注释在 action 外部 -->
<action name="my_action">
    tween(alpha, 0);
</action>

<!-- ❌ 错误 — 注释在 action 内部 -->
<action name="my_action">
    <!-- 这会报错 -->
    tween(alpha, 0);
</action>
```
</details>

<details>
<summary><b>2. 条件用 <code>EQ</code> / <code>GT</code> / <code>LT</code>，不用 <code>==</code> / <code>></code> / <code><</code></b></summary>

```
✅ if(get(x) EQ null, ...);
✅ if(stagewidth GT 600, ...);
❌ if(get(x) == null, ...);
```
</details>

<details>
<summary><b>3. include 文件中 URL 需加 <code>%BASEDIR%/</code></b></summary>

被 include 的 XML 中，相对路径基于该文件自身目录。加 `%BASEDIR%/` 始终相对于 tour 根目录：

```xml
url="%BASEDIR%/plugins/pp_blur.js"   ✅
url="plugins/pp_blur.js"            ❌ → 404
```
</details>

<details>
<summary><b>4. 私有作用域中访问全局 layer 用 <code>global.layer[name]</code></b></summary>

```
✅ tween(global.layer[h_tip].alpha, 1.0);
❌ tween(layer[h_tip].alpha, 1.0);    → 找不到
```
</details>

<details>
<summary><b>5. 隐藏 text layer 用 <code>alpha</code> 不用 <code>scale</code></b></summary>

```
✅ set(global.layer[tip].alpha, 0);
❌ set(global.layer[tip].scale, 0);   → 仍可点击
```
</details>

<details>
<summary><b>6. HTML 实体用数字引用</b></summary>

krpano XML 不支持 `&times;` 等命名实体：

```
✅ &#215;   &#160;
❌ &times;  &nbsp;
```
</details>

<details>
<summary><b>7. <code>pp_blur</code> 插件不要重复声明</b></summary>

`popup.xml` 已声明 `pp_blur` 插件实例，`videoplayer.xml` 共享使用，**不要**在其他 XML 中再次声明同名 plugin。
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
