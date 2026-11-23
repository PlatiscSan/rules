# Custom Clash Rules

## OpenClash

1. 在本地完整配置 `custom.yaml` 中填写或确认 `proxy-providers.Subscription.url`。使用订阅服务提供的 Clash/Mihomo YAML 地址，响应应包含顶层 `proxies` 节点列表。
2. 打开 **服务 → OpenClash → 配置管理**，选择 **规则集文件** 上传 `rules/` 下的全部 62 个 `.list` 文件。OpenClash 将它们放在 `/etc/openclash/rule_provider/`，对应 `custom.yaml` 中的 `./rule_provider/`。
3. 在同页选择 **配置文件** 上传 `custom.yaml` 并应用，确保 Mihomo 核心已安装。规则使用 GeoSite 的 `category-ads-all`、`cn` 和 GeoIP 的 `CN`，需要核心可加载对应的 GEO 数据。
4. 打开仪表盘，在 `Proxy` 组选默认节点。各服务组默认跟随 `Proxy`，也可以独立选择订阅节点。巴哈姆特走 `Bahamut`，该组按节点名筛选台湾节点，组为空时回退到 `REJECT`。使用支持 `empty-fallback` 的 Mihomo 核心。

[OpenClash 配置管理指南 §12.4–12.8](https://github.com/vernesong/OpenClash/blob/dev/.github/skills/openclash-user-guide/12-subscribe-config.md)

## 独立运行 Mihomo

本地完整配置 `config.yaml` 从 Mihomo 工作目录下的 `rules/` 读取规则。首次配置时，可将 `config.example.yaml` 合并到现有配置的对应顶层段落，再填写 `Subscription.url`。合并规则时保留在末尾的单一 `MATCH`。在仓库目录运行 `mihomo -t -d . -f config.yaml` 校验，或运行 `mihomo -d . -f config.yaml` 启动。

`proxy-providers/` 中的示例用于补充节点订阅，可将其条目合并到主配置的 `proxy-providers`，并确保策略组的 `use` 引用对应的键。私有订阅地址仅保存在本地配置中。

## Routing

| Ruleset | Policy | Coverage |
| --- | --- | --- |
| `AI` | `AI` group | OpenAI, Anthropic, Moonshot/Kimi, Gemini, DeepSeek, xAI, and other major AI providers |
| `Google` | `Google` group | Google services and YouTube, including country-specific Google domains |
| `GitHub` | `GitHub` group | GitHub websites, APIs, releases, packages, and user content |
| `GameAPI` | `GameAPI` group | Steam, Epic, EA, Ubisoft, Blizzard, and Riot service endpoints |
| `GameCDN` | `GameCDN` group | Game downloads, updates, static assets, and video content |
| `MicrosoftAPI` | `MicrosoftAPI` group | Microsoft identity, Graph, Azure management, OneDrive, and Microsoft 365 APIs |
| `MicrosoftCDN` | `MicrosoftCDN` group | Microsoft downloads, updates, and content-delivery endpoints |
| `Bahamut` | `Bahamut` group | Bahamut/Gamer pages, APIs, and video streams |
| `LocalNetwork` | `DIRECT` | LAN, localhost, private addresses, link-local addresses, and Tailscale-style carrier-grade NAT ranges |
| `Developer` | `Developer` group | Docker, npm, PyPI, Go, Rust, package registries, and developer communities |
| `Telegram` | `Telegram` group | Telegram domains and the IP ranges used by its clients |
| `Discord` | `Discord` group | Discord pages, APIs, voice, and media endpoints |
| `SocialMedia` | `SocialMedia` group | X/Twitter, Facebook, Instagram, Threads, Reddit, WhatsApp, and LinkedIn |
| `Streaming` | `Streaming` group | Netflix, Disney+, Spotify, Twitch, and Vimeo |

## 服务子组

配置共 62 个策略组。下表的 34 个子组默认跟随所属策略组，也可直接选订阅节点。子组规则写在所属规则集之前，剩余域名仍按所属规则集处理。CDN 规则排在对应服务规则之前。

| 所属策略组 | 子组 |
| --- | --- |
| `AI` | OpenAI, Claude, Kimi, Gemini, DeepSeek, Grok, Perplexity, Copilot, Qwen, Zhipu |
| `Streaming` | Netflix, DisneyPlus, Spotify, Twitch, Vimeo |
| `GameAPI` | Steam, EpicGames, EA, Ubisoft, Blizzard, RiotGames |
| `SocialMedia` | Twitter, Facebook, Instagram, Threads, Reddit, WhatsApp, LinkedIn |
| `Developer` | Docker, Npm, PyPI, Go, Rust, StackOverflow |

## CDN 策略组

16 个 CDN 组默认选 `DIRECT`，也可以切换到对应服务组。Google 软件下载走 `GoogleCDN`，YouTube 媒体单独走 `YouTubeCDN`。如果巴哈姆特视频需要台湾出口，可把 `BahamutCDN` 切到 `Bahamut`。

## 验证分流

在 OpenClash 仪表盘的连接页查看命中的规则和出站策略。用 `steamcontent.com` 下的下载域名检查游戏 CDN，用 `generativelanguage.googleapis.com` 检查 Gemini，用 `ani.gamer.com.tw` 检查台湾组。

规则顺序为本地直连、广告拦截、CDN 直连、服务分流、中国大陆直连、最终代理。
