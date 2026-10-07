# Proxy Routing

适用于 Shadowrocket（小火箭）与 Mihomo / Clash Meta 的通用分流配置，规则来自 MetaCubeX 完整规则集。配置不含代理节点，需要使用你自己的订阅或节点。

## 获取配置

| 客户端 | 下载 | 使用方式 |
| --- | --- | --- |
| 小火箭 | [手动选择](https://raw.githubusercontent.com/leon4z/proxy-routing/main/shadowrocket/Shadowrocket-select.conf) · [故障转移](https://raw.githubusercontent.com/leon4z/proxy-routing/main/shadowrocket/Shadowrocket-fallback.conf) · [混合模式](https://raw.githubusercontent.com/leon4z/proxy-routing/main/shadowrocket/Shadowrocket-hybrid.conf) | 配置 → 添加配置 → 从 URL 下载，随后使用自己的节点 |
| Mihomo / Clash Meta | [下载配置包](https://raw.githubusercontent.com/leon4z/proxy-routing/main/downloads/mihomo.zip) | 解压，选择一种模式，保留 config.yaml 和 rules/；在该模式目录的 local/nodes.yaml 放置原生 proxies 列表 |
| Karing | [下载规则集](https://raw.githubusercontent.com/leon4z/proxy-routing/main/downloads/karing.zip) | 规则集和分流绑定说明，需要在应用内手动绑定；不提供完整客户端配置 |

建议先用 **手动选择模式**。故障转移和混合模式的 AI / Google 使用独立稳定组；通用配置初始为 REJECT，须将该组中的 REJECT 改为自己的可信稳定节点再使用。直接导入这两种模式不会自动为你挑选稳定节点。

## 模式区别

| 模式 | 行为 |
| --- | --- |
| select：手动选择 | 自己选择代理出口 |
| fallback：故障转移 | 速度候选故障时切换；AI / Google 使用稳定组 |
| hybrid：混合模式 | 主出口手动选择；AI / Google 使用稳定组 |

Apple、Microsoft、Game、PayPal、Amazon、BiliBili、Spotify 默认直连。配置关闭 IPv6，拒绝 QUIC（UDP 443）及指定 STUN 流量；国内 DNS 直连、国外 DNS 通过代理。

## 来源与更新

- 本仓库仅发布生成后的通用配置、使用说明与来源报告。生成器和个人配置保存在私密仓库。
- 当前规则版本与小火箭正则适配差异见 [来源与差异报告](manifest.json)；许可见 [第三方说明](THIRD_PARTY_NOTICES.md)。
- 小火箭对少数域名正则使用显式通配替代，范围差异逐条记录。Mihomo 保留原生正则。
- 当前配置为首次发布版本；后续自动发布链路尚待接入。旧设备使用的配置地址仍然保留，尚未切换到本仓库。

配置已通过构建和 Mihomo 核心规则加载检查。小火箭真机导入、Karing 手动绑定和各客户端实际出口仍需验证。
