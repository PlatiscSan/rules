# Custom Clash Rules

These rules target Mihomo and Clash variants that support `rule-providers`. "A社" is interpreted as Anthropic.

## Routing

| Ruleset | Policy | Coverage |
| --- | --- | --- |
| `AI` | `AI` group | OpenAI, Anthropic, Moonshot/Kimi, Gemini, DeepSeek, xAI, and other major AI providers |
| `Google` | `Proxy` | Google services and YouTube, including country-specific Google domains |
| `GitHub` | `Proxy` | GitHub websites, APIs, releases, packages, and user content |
| `GameAPI` | `Proxy` | Steam, Epic, EA, Ubisoft, Blizzard, and Riot service endpoints |
| `GameCDN` | `DIRECT` | Game downloads, updates, static assets, and video content |
| `MicrosoftAPI` | `Proxy` | Microsoft identity, Graph, Azure management, OneDrive, and Microsoft 365 APIs |
| `MicrosoftCDN` | `DIRECT` | Microsoft downloads, updates, and content-delivery endpoints |
| `Bahamut` | `Taiwan` | Bahamut/Gamer pages, APIs, and video streams |
| `LocalNetwork` | `DIRECT` | LAN, localhost, private addresses, link-local addresses, and Tailscale-style carrier-grade NAT ranges |
| `Developer` | `Proxy` | Docker, npm, PyPI, Go, Rust, package registries, and developer communities |
| `Telegram` | `Proxy` | Telegram domains and the IP ranges used by its clients |
| `Discord` | `Proxy` | Discord pages, APIs, voice, and media endpoints |
| `SocialMedia` | `Proxy` | X/Twitter, Facebook, Instagram, Threads, Reddit, WhatsApp, and LinkedIn |
| `Streaming` | `Proxy` | Netflix, Disney+, Spotify, Twitch, and Vimeo |

## Proxy Provider（代理节点提供者）

`proxy-providers/` 中的 `subscription.example.yaml` 是远程订阅提供者模板。将其 `url` 替换为你自己的订阅地址，并在完整配置的 `proxy-providers` 下合并；`proxies/local.example.yaml` 是本地节点模板，同样用于参考，不应提交包含私有凭据的文件。

`config.example.yaml` 中的 `Proxy` 分组已通过 `use: [Subscription]` 引用远程提供者，并保留 `include-all: true` 以兼容现有节点。代理节点由 `proxy-providers` 提供，`rule-providers` 只负责流量分流规则，两者互不替代。

The configuration also rejects domains in `GEOSITE,category-ads-all`, then routes remaining mainland China domains and IPs directly with `GEOSITE,cn` and `GEOIP,CN`. Custom rules take precedence, so Moonshot still uses `AI`, Bahamut still uses `Taiwan`, and Google remains fully proxied. These built-in rules require Mihomo's standard GeoSite and GeoIP data files.

## Usage

1. Keep `rules/` beside your merged Mihomo configuration file.
2. Merge `rule-providers`, `proxy-groups`, and `rules` from `config.example.yaml` into that configuration. Do not replace your existing proxy nodes or `proxy-providers`.
3. Add the custom rules before your final `MATCH` rule. Preserve the shown order: local traffic first, direct CDN rules before API rules, `AI` before `Google`, and China direct rules last. Use `MATCH,Proxy` as the fallback if your configuration does not already define one.
4. 在你的完整配置中合并 `proxy-providers`，并将 `subscription.example.yaml` 中的示例 URL 替换为实际订阅地址。
4. Select an outbound node in `Proxy`. The `AI` group can independently use `Proxy` or `DIRECT`. `Taiwan` automatically includes nodes whose names contain Taiwan, Taipei, TW, 台湾, 台灣, 台北, or 臺北.
5. Validate the complete configuration with `mihomo -t -f your-config.yaml`, then reload it and test representative domains.

For remote rule providers, change each provider to `type: http`, replace `path` with its raw-file `url`, and add an update `interval`.
