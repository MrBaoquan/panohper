# Changelog

All notable changes to this project will be documented in this file.

## [1.1.0] - 2026-04-02

### Added

- `nav_styles.xml` — 导航 UI 样式覆盖组件
  - 热点样式：前进箭头精灵动画（720yun 风格）+ 悬停透明反馈
  - 热点点击：去掉默认飞出动画，直接切换场景
  - 热点 setup：自动添加 tooltip + 永久场景名标签
  - 缩略图文字样式：半透明黑底
  - 悬停 tooltip：自适应宽度 + 圆角半透明背景
- `assets/animate/forward.png` — 前进箭头精灵图
- `assets/animate/720yun/` — 720yun 风格动画精灵集（qianjin2, youzhuan2, zuozhuan2, wurenji, spotd12, spotd6）

### Changed

- README.md 更新：新增 nav_styles.xml 文档、加载顺序说明、API 参考

## [1.0.0] - 2026-01-01

### Initial Release

- `popup.xml` — 图文弹窗（横向/纵向可滚动，支持子热点叠加）
- `videoplayer.xml` — 全屏视频播放器（iOS 兼容）
- `tips.xml` — 滑动提示（自动淡出）
- `utils.xml` — 精灵动画引擎 + 通用工具
- 内置资源：热点图标、加载动画、UI 按钮
- 插件：pp_blur.js、scrollarea.js、videoplayer.js
