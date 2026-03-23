# wxmemory

> 微信聊天记录归档工具 — 一款基于OCR的将微信对话导出为 Markdown，支持语音转写、图片、视频等多媒体内容的工具
>
> 本工具的初衷是为了让微信的聊天记录能够顺利的被本地保存下来，保存自己的记忆。仅供个人使用交流和学习。
>
> P.S.  闭源收费的原因很简单，想让项目的寿命长一点。因为不保证项目永久有效，故无永久高级授权。
>

---

**！！！现在购买license，有“Donor”方案测试优惠，本项目已经进入基本可用的测试状态。只需5元即可获得基本可用的免破库的多媒体导出功能。仍然有不完善的地方，理性购买。**

## 功能

### 免费功能
- **文字消息** — 使用 Apple Vision 框架在本地完成 OCR，完整导出所有文字对话
- **语音转写** — 使用 Whisper MLX 在本地完成语音识别，不上传任何音频数据

### 付费功能（需要 License）
- **文件** — 自动另存为本地文件
- **图片** — 截图保存
- **视频** — 自动另存为本地视频
- **表情包** — 截图保存
- **小程序** — 提取跳转链接
- **链接** — 提取分享卡片的原始 URL

---

## 系统要求

| 项目 | 要求 |
|------|------|
| macOS | Mac OS 15 及 26 |
| 芯片 | Apple Silicon（M1 / M2 / M3 / M4 / M5） |
| 微信 | macOS 版（微信 4.1.7.x） |
| Homebrew | 用于安装系统依赖 |

### 分辨率要求

程序依赖固定的屏幕坐标进行 OCR 和自动化操作，请在系统设置 → 显示器中将分辨率调整为以下推荐值：

| 设备 | 推荐分辨率 | 是否测试过 |
|------|-----------|------ |
| MacBook Pro 14'' | 1800 × 1169 | Yes |
| MacBook Pro 16'' | 2056 × 1329 |  No |
| MacBook Air 13'' | 1710 × 1112 |  No |
| MacBook Air 15'' | 1920 × 1243 | Yes |
| 4K / 5K 外接显示器 | 2304 × 1296 | Yes |

!!!程序不会自动切换分辨率，请提前设好再运行。

如果你正好有MacBook air 13‘’和 MacBook Pro 16‘’ 可以和我联系增加适配。tg群:[Link](https://t.me/+jUBVwOWhRzc1N2Y1)

---

## 安装

从 [Releases](../../releases) 页面下载最新的 `wxmemory` 可执行文件，放到任意目录即可运行：

```bash
chmod +x wxmemory
./wxmemory
```

首次运行需要在同目录下创建 `config.yaml` 配置文件（参考下方配置说明）。

---

## 系统依赖

首次使用前需要安装以下工具。可以手动安装，也可在程序内选择 **[2] 环境准备** 自动引导安装：

```bash
brew install ffmpeg switchaudio-osx blackhole-2ch displayplacer
```

| 依赖 | 用途 | 是否必须 |
|------|------|---------|
| `ffmpeg` | 音频格式转换（录音 → WAV） | 语音转写必须 |
| `switchaudio-osx` | 导出时自动切换系统音频输出设备 | 语音转写必须 |
| `blackhole-2ch` | 虚拟音频设备，用于录制微信播放的语音消息 | 语音转写必须 |
| `displayplacer` | 检测和调整屏幕分辨率 | 必须 |

> 安装 BlackHole 后需要重启电脑才能生效。

### Whisper 语音模型

语音转写需要下载 Whisper Large V3 MLX 模型（约 2.9 GB）。可在程序内 **[2] 环境准备** 中选择自动下载或手动下载。

手动下载地址（支持迅雷等 P2P 工具加速）：

- `config.json`（4 KB）：`https://huggingface.co/mlx-community/whisper-large-v3-mlx/resolve/main/config.json`
- `weights.npz`（~2.9 GB）：`https://huggingface.co/mlx-community/whisper-large-v3-mlx/resolve/main/weights.npz`

下载后将两个文件放到以下目录：

```
~/.wxmemory/models/whisper-large-v3-mlx/
```

---

## 配置文件

在 `wxmemory` 可执行文件同目录下创建 `config.yaml`：

```yaml
wechat:
  main_window:
    size: [1152, 1271]
    position: [0, 25]
  chat_history_window:
    size: [1152, 1271]
    position: [0, 25]
  more_button_offset: [28, 37]

capture:
  target_resolution: [2304, 1296]   # 根据你的显示器分辨率调整

vision:
  recognition_level: 0              # 0=快速(支持中文), 1=精确(仅拉丁字符)
  languages: ["zh-Hans", "en-US"]

scroll:
  method: "scroll_wheel"
  scroll_amount: -100
  scroll_delay: 0.5
  max_pages: 500                    # 最大翻页数
  no_new_threshold: 5

export:
  output_dir: "exports"

multimedia:
  enabled: true
  media_dir: "media"
  voice:
    enabled: true
    whisper_model: "mlx-community/whisper-large-v3-mlx"
    timestamp_tolerance: 120
    audio_output: "BlackHole 2ch"
  image:
    enabled: true
  video:
    enabled: true
  sticker:
    enabled: true
  mini_program:
    enabled: true
  link:
    enabled: true
  file:
    enabled: true
    extra_extensions: []             # 额外文件扩展名，如 ["3mf", "step"]
```

> `capture.target_resolution` 需要与你在系统设置中调整的分辨率一致。

---

## 使用方法

### 交互式菜单（推荐）

```
$ ./wxmemory

[1] 开始导出
[2] 环境准备
[3] License 管理
[4] 关于
[0] 退出
```

选择 **[1] 开始导出** 后，按提示输入聊天对象名称、选择导出模式和功能，确认后程序自动完成导出。

### 命令行模式

```bash
# 导出指定聊天，最多翻 10 页
./wxmemory-cli export --chat "好友名称" --max-pages 10

# 增量导出（从上次断点继续）
./wxmemory-cli export --chat "好友名称" --mode incremental

# 仅导出文字和语音（不处理图片、视频等）
./wxmemory-cli export --chat "好友名称" --no-image --no-video --no-file

# 从已有数据重新生成 Markdown
./wxmemory-cli regenerate --chat "好友名称"
```

### 导出前注意事项

- 请确保微信已打开并置于前台
- 目标聊天对象需要在左侧栏"全名可见"
- 微信设置 → 通用 → 字体大小调到"最小"
- 微信设置 → 通用 → "使用系统默认浏览器打开网页" 关闭
- 运行期间请勿操作电脑（鼠标键盘都不要碰）
- 如果找不到聊天对象，请把聊天框点到目标对象的上一个再重试
- 语音转写期间电脑会静音（音频输出切换到 BlackHole），导出结束后自动恢复(这个有bug，麻烦先自己手动切一下)

### 导出结果

导出文件保存在 **`wxmemory` 可执行文件同目录**（即 `config.yaml` 所在目录）下的 `exports/` 文件夹：

```
exports/好友名称/
├── chat.md          # Markdown 格式的聊天记录
├── export.log       # 详细运行日志（含时间戳）
├── messages.jsonl   # 结构化消息数据（用于断点续传和重新生成）
├── state.json       # 运行状态
└── media/           # 媒体文件（图片、视频、文件、语音 WAV 等）
```

---

## 技术原理

wxmemory 完全在本地运行，除 License 验证外不产生任何网络请求。

**OCR 识别**
使用 Apple Vision 框架（`VNRecognizeTextRequest`）对微信窗口截图进行本地文字识别，支持中英文混合。

**语音转写**
导出期间将系统音频输出切换到 BlackHole 2ch 虚拟设备，实时录制微信播放的语音消息，录音完成后通过 Whisper Large V3 MLX 在本地异步转写为简体中文文字。

**增量存储与断点续传**
消息以 JSONL 格式逐条追加写入，每页完成后更新断点状态，中断后可从上次位置继续导出。语音转写结果独立缓存，即使进程意外终止也不会丢失已完成的转写。

---

## 隐私声明

- 所有 OCR 识别、语音转写均在本机完成，**不上传任何聊天内容、图片或音频**
- 唯一的网络通信：License 验证（向服务器发送 License Key + 机器 UUID，使用 RSA 签名防篡改）
- License 缓存有效期内离线也可正常使用全部已解锁功能

---

## 购买 License

免费版支持文字导出和语音转写，付费版解锁图片、视频、表情包、链接、小程序、文件等多媒体功能。

**购买地址：[爱发电](https://afdian.com/a/scoltc0723)**

购买后在程序内选择 **[3] License 管理 → [1] 输入爱发电订单号** 完成激活。License 绑定当前机器，到期后联网自动续签。离线状态下有 2 天宽限期。

---

## 免责声明

1. 本工具仅供个人用户备份和归档自己的微信聊天记录，用于保存个人记忆。请勿将本工具用于任何商业用途或侵犯他人隐私的行为。
2. 本工具通过屏幕截图和 OCR 识别的方式读取聊天内容，不修改、不破解、不逆向微信客户端，不调用任何微信内部接口或非公开 API。
3. 用户应确保仅导出自己参与的聊天记录，并对导出内容的保管和使用承担全部责任。未经对方同意，请勿公开或传播聊天记录中涉及他人的内容。
4. 本工具的开发者不对因使用本工具而导致的任何直接或间接损失承担责任，包括但不限于：数据丢失、账号异常、隐私泄露等。
5. 微信及相关商标归腾讯公司所有。本工具与腾讯公司无任何关联，未获得腾讯公司的授权或认可。
6. 由于微信客户端可能随时更新界面布局，本工具不保证在所有版本的微信上均能正常工作。付费功能目前处于测试阶段，不保证 100% 的成功率，请理性购买。
7. 使用本工具即表示您已阅读并同意以上声明。如不同意，请立即停止使用并删除本工具。

---

## 致谢

本项目的实现依赖以下开源项目和框架：

| 项目 | 用途 |
|------|------|
| [Apple Vision](https://developer.apple.com/documentation/vision) | macOS 本地 OCR 框架 |
| [mlx-whisper](https://github.com/ml-explore/mlx-examples/tree/main/whisper) | Apple Silicon 原生 Whisper 语音识别推理 |
| [MLX](https://github.com/ml-explore/mlx) | Apple 机器学习框架 |
| [BlackHole](https://github.com/ExistentialAudio/BlackHole) | macOS 虚拟音频设备 |
| [PyObjC](https://github.com/ronaldoussoren/pyobjc) | Python ↔ macOS 原生框架桥接 |
| [OpenCV](https://github.com/opencv/opencv-python) | 图像处理与媒体区域检测 |
| [Pillow](https://github.com/python-pillow/Pillow) | 图像处理 |
| [Click](https://github.com/pallets/click) | 命令行接口框架 |
| [HTTPX](https://github.com/encode/httpx) | HTTP 客户端 |
| [PyYAML](https://github.com/yaml/pyyaml) | YAML 配置文件解析 |
| [cryptography](https://github.com/pyca/cryptography) | RSA 签名验证 |
| [NumPy](https://github.com/numpy/numpy) | 数值计算 |
| [OpenCC](https://github.com/yichen0831/opencc-python) | 繁简中文转换 |
| [Hugging Face Hub](https://github.com/huggingface/huggingface_hub) | 模型下载管理 |
| [FFmpeg](https://ffmpeg.org/) | 音频录制与格式转换 |
| [SwitchAudioSource](https://github.com/deweller/switchaudio-osx) | macOS 音频输出设备切换 |
| [displayplacer](https://github.com/jakehilborn/displayplacer) | macOS 显示器分辨率管理 |
| [PyArmor](https://github.com/dashingsoft/pyarmor) | Python 代码混淆保护 |
| [PyInstaller](https://github.com/pyinstaller/pyinstaller) | Python 应用打包为独立可执行文件 |
