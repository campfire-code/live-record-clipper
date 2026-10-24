# 直播边录边切（BiliBili ShadowReplay）

> 篝火出品 · 收录于篝火 App 应用区 · 基于开源项目 [Xinrea/bili-shadowreplay](https://github.com/Xinrea/bili-shadowreplay)（MIT）

> 请只录制、剪辑你有权使用的直播内容，并遵守各直播平台的规则；弹幕、画面的版权归主播和平台所有，转载投稿前请自行确认授权。

直播开播后自动在本机录制并缓存，同时记录弹幕。看直播或回放时用快捷键标出起止位置，就能切出片段，再剪辑、加字幕、压制弹幕，最后直接投稿到 B 站，把整个流程压缩到分钟级。

## 支持的直播平台

按上游文档，目前支持录制：**B 站、抖音、斗鱼、TikTok、Twitch、快手、虎牙、YouTube**。

- B 站、抖音需要先在软件里配置对应平台的账号；斗鱼、虎牙、快手、TikTok、Twitch、YouTube 可直接添加（Twitch 使用公开接口，不需要账号）。
- 投稿功能目前只支持 B 站。

## 下载

到 [Releases](https://github.com/campfire-code/live-record-clipper/releases/latest) 下载：

| 电脑 | 文件 |
|---|---|
| Windows，NVIDIA 显卡 | `live-record-clipper_版本号_bili-shadowreplay-cuda_…_x64-setup.exe`（带 GPU 加速的字幕识别，体积较大） |
| Windows，其他显卡 | `live-record-clipper_版本号_bili-shadowreplay-cpu_…_x64-setup.exe`（字幕识别用 CPU，较慢） |
| Mac（Apple 芯片：M 系列） | `live-record-clipper_版本号_bili-shadowreplay_…_aarch64.dmg` |
| Mac（Intel 芯片） | `live-record-clipper_版本号_bili-shadowreplay_…_x64.dmg` |
| Linux（x86-64） | `live-record-clipper_版本号_bili-shadowreplay_…_amd64.AppImage`（上游说明 Linux 测试较少） |

- 安装包来自上游官方发布，篝火没有修改，附 `SHA256SUMS.txt` 可核对。
- 上游另提供 deb、rpm、msi 等格式，篝火不转发布，需要请到[上游发布页](https://github.com/Xinrea/bili-shadowreplay/releases/tag/v2.24.2)下载。

## 前置条件

- **Mac 和 Linux 需要先安装 FFmpeg**（Windows 版自带）。Mac：`brew install ffmpeg`；Ubuntu / Debian：`sudo apt install ffmpeg`。没有 FFmpeg 会无法预览直播和生成切片。
- Mac 首次打开会提示“无法打开从网络下载的软件”，到“系统设置 → 隐私与安全性”选择仍然打开。
- 录制 B 站、抖音需要在软件的“账号”页配置账号。
- AI 字幕（可选）使用 Whisper：可在本机运行，模型首次使用时自动下载；也可接在线 Whisper 服务（通常需要付费密钥）。录播总结等功能需要自己配置大模型。
- 软件界面为简体中文。

## 怎么用

1. 打开软件，在“账号”页配置 B 站或抖音账号（按需）。
2. 进入“直播间”页添加直播间：选平台，输入直播间号（网址末尾的数字）；也可以把浏览器里直播间地址的 `https://` 换成 `bsr://` 快速唤起添加。
3. 直播间处于启用状态时，开播会自动开始录制。
4. 点进录播的预览窗口，在时间轴上按 `[` 设起点、按 `]` 设终点，点“生成素材”得到片段（默认直接复制视频流，不重新编码，速度很快；素材可能比选区开头多出一两秒）。
5. 在“切片”里裁剪、拼接素材，添加字幕、压制弹幕，导出；需要的话在投稿页发布到 B 站。
6. 如果只想当录播软件用，可在设置里开启“生成整场录播素材”。缓存目录里的录播文件是碎片，不能直接播放或投稿，要用上面的素材功能导出。

## 验收用例

| 操作 | 应得到 |
|---|---|
| 安装并打开（Mac / Linux 已装 FFmpeg），添加一个正在直播的斗鱼或虎牙直播间 | 直播间状态显示正在录制 |
| 打开录播预览，按 `[` 和 `]` 标出一段，点生成素材 | “素材”页出现一个可播放的 mp4 片段 |

## 和上游的区别

- 仅新增中文说明与篝火发布流程（`tool.json`、`.github/workflows/release.yml`），并删除了上游的 GitHub 工作流，软件代码与上游一致。

## 归属与许可证

软件版权属于 BiliBili ShadowReplay 项目作者，使用 MIT 许可证（见 `LICENSE`）。


---

## 以下为上游原文

# BiliBili ShadowReplay

![icon](docs/public/images/header.png)

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/xinrea/bili-shadowreplay/main.yml?label=Application%20Build)
![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/Xinrea/bili-shadowreplay/package.yml?label=Docker%20Build)
![GitHub Release](https://img.shields.io/github/v/release/xinrea/bili-shadowreplay)
![GitHub Downloads (all assets, all releases)](https://img.shields.io/github/downloads/xinrea/bili-shadowreplay/total)
[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/Xinrea/bili-shadowreplay)

BiliBili ShadowReplay 是一个缓存直播并进行实时编辑投稿的工具。通过划定时间区间，并编辑简单的必需信息，即可完成直播切片以及投稿，将整个流程压缩到分钟级。同时，也支持对缓存的历史直播进行回放，以及相同的切片编辑投稿处理流程。

目前支持录制的直播平台：

- B站
- 抖音
- 斗鱼
- TikTok
- Twitch
- 快手
- 虎牙
- YouTube

## Star History

<a href="https://www.star-history.com/?repos=xinrea%2Fbili-shadowreplay&type=date&legend=top-left">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/chart?repos=xinrea/bili-shadowreplay&type=date&theme=dark&legend=top-left" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/chart?repos=xinrea/bili-shadowreplay&type=date&legend=top-left" />
   <img alt="Star History Chart" src="https://api.star-history.com/chart?repos=xinrea/bili-shadowreplay&type=date&legend=top-left" />
 </picture>
</a>

## 安装和使用

![rooms](docs/public/images/summary.png)

前往网站查看说明：[BiliBili ShadowReplay](https://bsr.xinrea.cn/)

## 参与开发

可以通过 [DeepWiki](https://deepwiki.com/Xinrea/bili-shadowreplay) 了解本项目。

贡献指南：[Contributing](.github/CONTRIBUTING.md)



<a href="https://github.com/Xinrea/bili-shadowreplay/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Xinrea/bili-shadowreplay" />
</a>

Made with [contrib.rocks](https://contrib.rocks).

## 赞助

<!-- markdownlint-disable MD033 -->
<img src="docs/public/images/donate.png" alt="donate" width="300">
