# qiaomu-info-card-designer

> Turn any text or URL into a magazine-quality info card image — auto screenshot included.
> 将任意文本或链接一键转化为杂志质感信息卡片，自动截图输出图片，适合分享到 X、小红书、微信。

**[English](#english) | [中文](#中文)**

---

<!-- qiaomu-profile:start -->
## 关于向阳乔木

向阳乔木（乔向阳 / Joe）是一位实践型 AI 产品与内容创作者，长期把前沿 AI 变化转译成可复用的工作流、产品判断、AI 编程实践、AI 搜索实践和 GEO/AI 营销方法。

- 个人网站: https://qiaomu.ai
- 博客: https://blog.qiaomu.ai
- X: https://x.com/vista8
- GitHub: https://github.com/joeseesun/
- 微信公众号: 向阳乔木推荐看

### 支持与关注

| 打赏支持 | 微信公众号 |
|---|---|
| <img src="assets/qiaomu-profile/qiaomu_reward_qr.png" alt="向阳乔木打赏二维码" width="180" /> | <img src="assets/qiaomu-profile/qiaomu_wechat_public_account_qr.jpg" alt="向阳乔木推荐看公众号二维码" width="180" /> |
| 感谢支持乔木持续分享 AI 实践 | 扫码关注「向阳乔木推荐看」 |

<!-- qiaomu-profile:end -->

<a name="english"></a>
## English

### What it does

Give Claude a URL, paste text, or describe a topic — this skill designs a magazine-style HTML info card and **automatically screenshots it to PNG** ready to post on X (Twitter), Xiaohongshu, or WeChat.

**Key features:**
- Swiss International Style + editorial magazine aesthetic
- Big titles, punchy hook-style summaries (10–20 chars each)
- Supports 480 / 600 / 900px width — just say "make it 480px wide"
- Auto-splits cards taller than 1200px into multiple images
- vw-based font sizing: fonts scale proportionally with card width
- Uses TsangerJinKai (仓耳今楷) local font for consistent rendering

### Prerequisites

- [ ] **Playwright Python** (used for headless screenshot)
  - Install: `pip3 install playwright && python3 -m playwright install chromium`
  - Verify: `python3 -c "from playwright.sync_api import sync_playwright; print('ok')"`
- [ ] **Python 3 + Pillow + NumPy** (for image splitting)
  - Install: `pip3 install Pillow numpy`
  - Verify: `python3 -c "from PIL import Image; import numpy; print('ok')"`

### Installation

```bash
npx skills add joeseesun/info-card-designer
```

### How to trigger

Say any of these to Claude:

- "生成信息卡" / "做张信息卡" / "信息卡片"
- "把这段内容做成卡片"
- "make info card" / "generate card"

### Natural-language examples

```
用户: 把这个链接做成信息卡 https://wise.readwise.io/issues/wisereads-vol-134/
用户: 把这段内容做成 480 宽的信息卡
用户: generate a card from this text, keep the original descriptions
```

### Width options

| Width | Best for | Output (2x DPR) |
|-------|----------|-----------------|
| 480px | Mobile-first, compact | 960px (3x → 1440px) |
| **600px** | **Default, balanced (recommended)** | **1200px** |
| 900px | Desktop / large-screen sharing | 1800px |

### Troubleshooting

| Problem | Solution |
|---------|----------|
| Screenshot fails | Make sure Playwright is installed: `pip3 install playwright && python3 -m playwright install chromium` |
| `No module named playwright` | Run `pip3 install playwright` then `python3 -m playwright install chromium` |
| `No module named PIL` | Run `pip3 install Pillow numpy` |
| Card content is wrong / made up | The skill is instructed to use only your source content — re-trigger with the URL or paste the text directly |
| Card too tall (1 image) | Splitting is automatic when height > 1200px; check that Pillow + NumPy are installed |

### Credits

Design inspiration from [@shao__meng](https://x.com/shao__meng)'s original post on X:
[https://x.com/shao__meng/status/2027368489439334504](https://x.com/shao__meng/status/2027368489439334504)

---

<a name="中文"></a>
## 中文

### 功能说明

给 Claude 一个 URL、粘贴一段文字，或描述一个主题——这个 skill 会自动设计成杂志质感 HTML 信息卡，并**自动截图输出 PNG**，可直接发到 X、小红书、微信。

**核心特点：**
- 瑞士国际主义 + 杂志编辑风格
- 大标题、强视觉张力
- Hook 模式（默认开启）：每条描述改写为 10-20 字钩子句，有冲击感
- 支持 480 / 600 / 900px 宽度，说"生成 480 宽的卡片"即可切换
- 超长卡片（>1200px）自动分割成多张图
- 使用仓耳今楷（TsangerJinKai）本地字体，截图渲染 100% 一致

### 前置条件

- [ ] **Playwright Python**（用于无头截图）
  - 安装：`pip3 install playwright && python3 -m playwright install chromium`
  - 验证：`python3 -c "from playwright.sync_api import sync_playwright; print('ok')"`
- [ ] **Python 3 + Pillow + NumPy**（用于超长图片分割）
  - 安装：`pip3 install Pillow numpy`
  - 验证：`python3 -c "from PIL import Image; import numpy; print('ok')"`

### 安装

```bash
npx skills add joeseesun/info-card-designer
```

### 触发方式

对 Claude 说：

- "生成信息卡"
- "做张信息卡"
- "把这段内容做成卡片"
- "信息卡片"
- "make info card" / "generate card"

### 你可以直接这样说

```
把这个链接做成信息卡：https://wise.readwise.io/issues/wisereads-vol-134/
把这段文字做成 480 宽的卡片
生成信息卡，保持原文描述
```

### Hook 模式开关

| 说法 | 效果 |
|------|------|
| 默认 | 自动将描述改写为 10-20 字钩子句 |
| "保持原文" / "不改描述" | 使用原始文字，不改写 |

### 常见问题

| 问题 | 解决方法 |
|------|----------|
| 截图失败 | 安装 Playwright：`pip3 install playwright && python3 -m playwright install chromium` |
| `No module named PIL` | 运行 `pip3 install Pillow numpy` |
| 内容不对 / 有编造 | 提供原文链接或直接粘贴文本，重新触发 |
| 超长卡没有分割 | 确认 Pillow + NumPy 已安装，高度 >1200px 才触发分割 |

### 致谢

设计风格灵感来自 [@shao__meng](https://x.com/shao__meng) 在 X 上的这篇分享：
[https://x.com/shao__meng/status/2027368489439334504](https://x.com/shao__meng/status/2027368489439334504)

## 验证 / Verification

```bash
python3 ~/.agents/skills/qiaomu-meta-skill/scripts/validate_skill.py <skill-dir>
```

## 许可证

MIT，见 [LICENSE](LICENSE)。
