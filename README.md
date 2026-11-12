# Proxy Routing

同一套公开分流策略提供 [Mihomo 配置](config/Mihomo/Proxy-Routing.yaml) 和 [Shadowrocket 配置](config/Shadowrocket/Proxy-Routing.conf)。配置内没有节点或订阅凭据。Mihomo 通过客户端覆写添加自己的节点订阅，Shadowrocket 使用客户端中已导入的节点。

AI、Google、影音社交和兜底默认跟随默认代理；金融初始选择 DIRECT，保留代理出口供手选，直连失败不自动切换，已有有效手选不因初始默认值变化而重置；OpenAI 和 Claude 默认跟随 AI，也可以独立选出口。IBKR、BBAE、富途等服务命中金融。券商不单独设组。通用境外分类和最终未命中流量进入兜底，以保持 Shadowrocket 既有终结正则适配的语义。

默认代理和各服务组可以选择香港、台湾、日本、新加坡、韩国、美国、其他地区或全部节点。地区组按名称筛选节点，全部节点包含未识别地区的节点。所有组都是手选，不自动测速切换。引用同一地区组的服务共享该组手选。公开配置不按 VPS、机场或住宅分类。Shadowrocket 默认代理初始跟随首页 PROXY，Mihomo 初次导入需手选可用节点；有效手选随客户端更新保留。

地区识别包含常见中文、繁体、英文、国旗、两字母代码及 `HKG/TWN/JPN/SGP/KOR`，并覆盖部分常见城市名。英文不区分大小写，地区代码保留字母边界，避免把 Australia/Russia 误归美国。其他地区是上述六区的补集，包含未识别名称。名称同时提及多个地区时可能进入多个组；分类不验证真实出口 IP，也不推断中转与落地关系。编辑组成员属于配置内容，更新整份远程配置会替换这些编辑；选择已有成员与编辑成员列表是两回事。

Mihomo 默认开启 HTTP、TLS 和 QUIC 域名嗅探，辅助缺少域名的连接匹配服务规则。`override-destination: false` 保留原连接目标。Shadowrocket 使用客户端内置的域名识别机制。

Mihomo 启用 `profile.store-selected` 和 `profile.store-fake-ip`，保存手选与 Fake-IP 映射缓存。

确认的共享遥测和反馈入口由[遥测聚合](data/groups/telemetry)统一维护，优先于广告拦截和普通业务规则，HTTPS 放行并经过隐藏的「共享遥测」手选出口。该出口只引用代理节点组，初始跟随全部节点，不包含 DIRECT 或首页 PROXY；其他服务改为直连不改变这些入口的处理。UDP 明确拒绝，代理失效不回落直连。可在支持显示隐藏分组的客户端选择遥测出口。清单仅包含已确认入口，并不放行整个广告分类或供应商所有域名。共享遥测与业务可使用不同出口；主动直连的业务网站仍能看到本机公网 IP。

从旧版三模式配置迁移时，请重新导入上述新入口；现在每个客户端只提供一份完整配置。迁移前可导出自己的旧配置及节点，个人编辑不会自动合并到新模板。

[全量规则索引](data/README.md)按用途归类，来源和项目补充记录在构建清单中。规则库与完整配置分开交付。完整配置只加载其分流策略引用的规则集。

| 目录 | 内容 |
| --- | --- |
| [data/services](data/services) | 全量服务、公司及服务子集，如 [Netflix](data/services/netflix)、[Google](data/services/google)、[IBKR](data/services/ibkr)、[BBAE](data/services/bbae) |
| [data/groups](data/groups) | 上游分类、通用集合与项目聚合组，如 [金融](data/groups/category-finance)、[AI](data/groups/ai)、[共享遥测](data/groups/telemetry) |
| [rule/Mihomo](rule/Mihomo) | 服务、聚合和配置专用的 YAML 规则 |
| [rule/Shadowrocket](rule/Shadowrocket) | 服务、聚合和配置专用的 RULE-SET 文件 |
| [config/Mihomo](config/Mihomo) | 完整配置和导入链接 |
| [config/Shadowrocket](config/Shadowrocket) | 完整配置 |

完整配置直接引用 `rule/` 链接。内容一致时共用服务或聚合规则，过滤广告属性、DNS 专用格式及已审阅的正则转换等差异版本放在相应客户端的 `variants/`，使用的格式、规则数及文件摘要记录在构建清单中。

聚合文件通过 `include:` 引用成员，路径以 `data/` 为起点，例如 `include:services/ibkr`。上游分类继承成员关系和属性筛选，成员引用解析到同一份服务数据及其本地增补。上游直接定义且归属未确认的规则保留在原组。客户端产物在构建时展开并去重。具体匹配规则每行一条，普通域名表示后缀匹配，`full:` 表示精确域名，`keyword:` 和 `regexp:` 分别表示关键词和正则，`@` 保留上游属性。项目服务也可以定义 `ip-cidr:`、`ip-cidr6:` 和 `ip-asn:`，聚合组继承成员的全部匹配条件。完整配置分别决定出口、顺序和 `no-resolve`。独立规则集含 ASN 时，Mihomo 需要可用的 ASN 数据库。Shadowrocket 独立规则集遇到未经验证的域名正则时，索引和清单标为不可用，对应规则仍完整保存在纯文本和 Mihomo 产物中。

[构建清单](manifest.json)记录上游版本、规则摘要、补充条目和与 MetaCubeX 的差异。[第三方来源与许可](THIRD_PARTY_NOTICES.md)记录数据归属。可把规则 URL 中的 `main` 换成公开仓库的完整提交 SHA，以固定规则版本。
