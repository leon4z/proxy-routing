# Proxy Routing

面向 Shadowrocket（小火箭）和 Mihomo / Clash Meta 的分流配置，使用 MetaCubeX 完整规则集。配置不提供代理节点，需要绑定你自己的节点或订阅。

## 获取配置

建议先用手动选择模式。主配置和规则集都可以通过 URL 获取，无须先解压 ZIP。

| 模式 | 小火箭 | Mihomo / Clash Meta |
| --- | --- | --- |
| 手动选择 | [配置链接](https://raw.githubusercontent.com/leon4z/proxy-routing/main/shadowrocket/Shadowrocket-select.conf) | [YAML 链接](https://raw.githubusercontent.com/leon4z/proxy-routing/main/mihomo/select/config.yaml) |
| 故障转移 | [配置链接](https://raw.githubusercontent.com/leon4z/proxy-routing/main/shadowrocket/Shadowrocket-fallback.conf) | [YAML 链接](https://raw.githubusercontent.com/leon4z/proxy-routing/main/mihomo/fallback/config.yaml) |
| 混合模式 | [配置链接](https://raw.githubusercontent.com/leon4z/proxy-routing/main/shadowrocket/Shadowrocket-hybrid.conf) | [YAML 链接](https://raw.githubusercontent.com/leon4z/proxy-routing/main/mihomo/hybrid/config.yaml) |

小火箭：配置 → 添加配置 → 从 URL 下载。主文件约 10 KB，通过 `RULE-SET` 加载本仓库的 [分类规则集](shadowrocket/rules/)，保留 DNS、策略组和少量优先匹配的补充规则。

Mihomo 客户端：在远程配置／订阅入口粘贴对应 YAML 链接。三个模式使用同一套规则和客户端无关的 Mihomo 语法；每个配置通过 HTTP 下载 46 个分流分类和 2 个 DNS 分类，规则刷新间隔为 24 小时。`path` 只是自动下载后的缓存位置，首次导入不需要手工放置 rules/。

规则下载使用 DIRECT，并使用直连 DNS 解析下载域名，避免首次启动依赖尚未加载的规则或节点。DIRECT 指核心的直连出口；网络本身仍需能访问 GitHub Raw，不能保证所有国内网络都可达。

## 一键导入 Clash

Clash Verge Rev 官方支持以下格式：

```text
clash://install-config?url=经过URI编码的YAML地址
```

手动版的完整链接：

```text
clash://install-config?url=https%3A%2F%2Fraw.githubusercontent.com%2Fleon4z%2Fproxy-routing%2Fmain%2Fmihomo%2Fselect%2Fconfig.yaml
```

[三种模式的导入地址](mihomo/import-links.json) 同时列出普通 HTTPS 和编码后的链接。能否唤起应用取决于客户端是否注册这个协议；GitHub 页面也可能限制非 HTTPS 链接。普通 YAML 地址最通用，可以直接粘贴到客户端。旁路由上的 Mihomo 使用同一份 YAML，由网关下载和重载流程管理；它没有桌面客户端的唤起入口。

一键导入只下载主配置，不负责提供节点，也不替你开启代理。主配置更新频率由客户端管理；GitHub Raw 没有设置客户端专用的自动更新响应头。[Clash Verge Rev 官方说明](https://www.clashverge.dev/guide/url_schemes.html)

## 设置自己的节点

小火箭可以使用首页已有节点。Mihomo 的公开模板不含节点或订阅凭据，未配置节点时代理流量会被拒绝。它不会自动合并客户端里另一份机场订阅。

Mihomo 的地区组和普通代理组会读取配置中的全部代理集合。用客户端持久保存的扩展／覆写添加自己的 Clash 格式节点订阅，例如：

```yaml
proxy-providers:
  nodes:
    type: http
    url: "在本机填写你的Clash格式节点订阅地址"
    path: ./proxy-providers/nodes.yaml
    interval: 86400
    proxy: DIRECT
    health-check:
      enable: true
      url: https://www.gstatic.com/generate_204
      interval: 600
```

这里需要返回 Clash `proxies` 列表的订阅，不能直接填任意 V2Ray 文本订阅。实际链接只保存在自己的设备或私密存储中。Clash Verge Rev 可右键这份配置卡片 → 编辑扩展配置；该扩展独立保存，主配置刷新后继续生效。[官方扩展说明](https://www.clashverge.dev/guide/extend.html)

故障转移和混合模式的 AI / Google 只使用「稳定」组，通用版该组初始为 REJECT；添加普通节点订阅不会自动指定稳定节点。需要在客户端持久的分组设置中，为稳定组绑定可信节点，并保持需要的优先顺序。首次使用不熟悉分组设置时，请选择手动版。

## 模式与网络策略

- 手动选择：自己选择普通代理和 AI 出口。
- 故障转移：速度候选故障时切换；AI / Google 使用独立稳定组。
- 混合模式：普通代理手动选择；AI / Google 使用独立稳定组。

Apple、Microsoft、Game、PayPal、Amazon、BiliBili、Spotify 默认直连。配置关闭 IPv6，拒绝 QUIC（UDP 443）及指定 STUN 流量；国内 DNS 直连、国外 DNS 通过代理。客户端的全局路由、DNS 覆写、IPv6 等应用设置可能覆盖配置，需要在实际设备核对。

## 来源与更新

- 本仓库只发布生成后的通用配置、使用说明与来源报告；生成器和个人配置保存在私密仓库。
- 固定来源版本与小火箭正则适配差异见 [来源与差异报告](manifest.json)，许可见 [第三方说明](THIRD_PARTY_NOTICES.md)。Mihomo 保留原生域名正则。
- 规则、主配置和节点订阅分别更新。小火箭按应用入口刷新远程规则；Mihomo 规则按 provider 周期刷新。跨仓库自动发布尚未接入，当前公开版本经过人工验证后发布；客户端刷新不会代替服务器端的新版本发布。
- [Mihomo 离线文件包](https://raw.githubusercontent.com/leon4z/proxy-routing/main/downloads/mihomo.zip) 仅用于备份；默认主配置仍会刷新远程规则，它不提供节点，也不是完全离线启动包。
- 旧设备的配置地址继续保留，尚未自动切换到本仓库。

已经验证构建、规则完整性、域名解析与 Mihomo 核心从无缓存状态下载 HTTP 规则集及绑定本地测试订阅；本机隔离核心实际从 GitHub Raw 下载全部 48 个分类也已通过。小火箭真机导入、Clash 客户端的一键唤起、持久覆写界面及实际出口仍需设备验收。
