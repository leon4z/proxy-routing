# Proxy Routing

面向 **Shadowrocket（小火箭）和 Mihomo / Clash Meta** 的分流配置。使用 MetaCubeX 完整规则集，提供手动选择、自动故障转移和混合模式，按域名、IP 和服务分类选择出口。

本项目提供规则、DNS 设置和策略组，**不提供代理节点**。请先准备自己的节点或 Clash 格式节点订阅。公开仓库只保存生成后的通用配置与规则；生成器和个人配置保存在私密仓库。当前不提供 Karing 配置。

小火箭使用 `.conf`；基于 Mihomo 内核的客户端共用标准 `.yaml`，但导入入口、扩展设置和一键唤起协议可能不同。旧 Clash 内核的兼容性不在当前验证范围内。

## 先选一个模式

首次使用建议选择 **手动版**。三份配置使用相同的分流规则，区别是节点选择方式。

| 模式 | 普通代理流量 | AI / Google | 适合谁 |
| --- | --- | --- | --- |
| 手动 `select` | 在 PROXY 组选择节点或地区组 | 跟随 PROXY | 想先用起来，或自己决定出口 |
| 故障转移 `fallback` | PROXY 默认使用「自动切换」组，按可用性切换 | 独立「稳定节点」组 | 普通流量故障时切换，AI 使用指定出口 |
| 混合 `hybrid` | 在 PROXY 组手动选择 | 独立「稳定节点」组 | 普通流量经常换出口，AI 保持指定线路 |

**故障转移版和混合版必须先配置「稳定节点」组。** 通用版「稳定节点」组初始为 REJECT，AI / Google 在设置完成前会被拒绝，不会自动改走「自动切换」组或直连。

手动版没有「自动切换」和「稳定节点」组；故障转移版都有；混合版只有「稳定节点」组。三个模式的地区组均为 `url-test`：选择某个地区后，由该组自动选择节点。「自动切换」和「稳定节点」组为 `fallback`，按成员顺序选择可用节点，**不等于始终选最低延迟**。

“普通代理流量”指按规则需要代理的请求。Apple、Microsoft、PayPal、Amazon、BiliBili、Spotify 默认 DIRECT，游戏分类固定 DIRECT，国内和局域网规则也保持直连。

先导入一份即可。同一时刻启用一份主配置；分组设置属于各自的配置，切换模式后需要重新检查。**本项目显式定义了 PROXY 组，不能直接沿用旧配置“在首页换节点就改变所有代理出口”的用法。**

## 复制配置地址

每个代码块只包含一个完整地址，可直接复制。主配置和分类规则均使用 GitHub Raw；首次下载需要网络能够访问该地址。缓存可能延迟显示新版，不保证所有网络都可直连。

### 小火箭

手动版：

```text
https://raw.githubusercontent.com/leon4z/proxy-routing/main/shadowrocket/Shadowrocket-select.conf
```

故障转移版：

```text
https://raw.githubusercontent.com/leon4z/proxy-routing/main/shadowrocket/Shadowrocket-fallback.conf
```

混合版：

```text
https://raw.githubusercontent.com/leon4z/proxy-routing/main/shadowrocket/Shadowrocket-hybrid.conf
```

### Mihomo / Clash Meta

手动版：

```text
https://raw.githubusercontent.com/leon4z/proxy-routing/main/mihomo/select/config.yaml
```

故障转移版：

```text
https://raw.githubusercontent.com/leon4z/proxy-routing/main/mihomo/fallback/config.yaml
```

混合版：

```text
https://raw.githubusercontent.com/leon4z/proxy-routing/main/mihomo/hybrid/config.yaml
```

### Clash Verge Rev 一键导入

以下使用官方支持的 `clash://install-config` 格式，已对 YAML URL 做 URI 编码。复制后通过支持外部协议的浏览器打开；能否唤起应用取决于协议注册和浏览器行为。没有唤起时，把上方 HTTPS 地址粘贴到客户端的远程配置入口即可。

手动版：

```text
clash://install-config?url=https%3A%2F%2Fraw.githubusercontent.com%2Fleon4z%2Fproxy-routing%2Fmain%2Fmihomo%2Fselect%2Fconfig.yaml
```

故障转移版：

```text
clash://install-config?url=https%3A%2F%2Fraw.githubusercontent.com%2Fleon4z%2Fproxy-routing%2Fmain%2Fmihomo%2Ffallback%2Fconfig.yaml
```

混合版：

```text
clash://install-config?url=https%3A%2F%2Fraw.githubusercontent.com%2Fleon4z%2Fproxy-routing%2Fmain%2Fmihomo%2Fhybrid%2Fconfig.yaml
```

一键导入只下载配置，不会添加节点或开启代理。普通 URL 和协议地址也保存在 `mihomo/import-links.json`。协议格式参考 [Clash Verge Rev 官方说明](https://www.clashverge.dev/guide/url_schemes.html)。

## 小火箭：导入、设置与检查

以下使用中文界面名称，不同版本的位置可能略有差异。应用入口参考原仓库教程和 [Shadowrocket 社区使用手册](https://github.com/LOWERTOP/Shadowrocket/wiki)，新配置仍需真机验收。

### 1. 添加节点，再下载配置

1. 在首页添加自己的节点或节点订阅。节点订阅填服务商地址，不能填本项目的 `.conf` 地址。
2. 复制所选模式的小火箭地址，进入「配置」→ 右上角「+」→ URL 栏粘贴 → 下载。
3. 下载后先设置分组，再点文件名称 →「使用配置」。文件右侧 ⓘ 进入编辑页；分组名称用于查看、选择成员，分组右侧 ⓘ 用于编辑类型和成员。

### 2. 手动版：在 PROXY 组选出口

进入该配置右侧 ⓘ →「代理分组」→ 点 PROXY 名称，选择自己的节点或有成员的地区组。AI、Google、YouTube 等默认指向 PROXY 的服务使用这个出口；默认 DIRECT 的服务保持直连。

PROXY 通过名称筛选收集节点。节点未出现时，检查首页订阅和分组成员是否加载。地区名称匹配节点备注，不代表实际 IP 属地或目标服务可用性；不要选择空组。

### 3. 故障转移版、混合版：设置「稳定节点」组

编辑成员前，先按后文「更新时保留自己的设置」复制配置并处理自动更新，再编辑副本。

1. 在首页打开选定节点右侧 ⓘ，复制完整「备注」。这里需要节点名称，不是订阅 URL 或服务器地址。
2. 进入配置副本右侧 ⓘ →「代理分组」→ 稳定节点右侧 ⓘ，保持类型 fallback，关闭「订阅」，清空筛选正则（如有）。
3. 删除初始 REJECT 成员，在策略添加输入框粘贴备注，点左侧「+」插入，确认成为列表的一行后保存。仅填写输入框不等于添加成功。
4. 至少添加一个可用节点。有备用线路可添加多个，用拖动手柄把优先线路排在前面，保存后返回「稳定节点」组检查。
5. 保持 AI / Google 选择稳定节点。故障转移版检查「自动切换」组有成员、PROXY 选择自动切换；混合版在 PROXY 组选普通流量的节点或地区组。

稳定节点由你决定，不依据公开测速自动指定。备注必须完整匹配，包含旗帜、空格和符号；订阅改名后需要同步成员。不要把 DIRECT、PROXY 或「自动切换」组作为稳定候选。

### 4. 启用并确认分流

1. 回到配置列表，点准备好的文件名称 →「使用配置」，等待规则加载，检查当前使用标记。
2. 首页「全局路由」选择「配置」，再开启连接；选择「代理」不会按本项目规则分流。
3. 在该配置的「测试规则」输入实际域名，例如 `claude.com`、`www.youtube.com`，检查是否命中 AI、YouTube 等预期策略。
4. 检查策略组选中的出口，再实际访问目标 App。规则命中不能证明节点地区、登录或服务可用。

若要单独改变某个服务的出口，需要先编辑该服务组的候选成员，再选择它。当前 YouTube 等组默认只有 PROXY，未列出的地区组不会直接出现在菜单。故障转移版和混合版的 AI / Google 保持「稳定节点」组绑定。

## Mihomo：导入与绑定节点

按 URL 导入，无需解压 ZIP 或手工放置规则目录。主配置会下载 **43 个分流分类和 2 个 DNS 分类**，`path` 是自动下载后的缓存位置。

### 1. 导入远程配置

在客户端远程配置／订阅入口粘贴对应 YAML 地址，下载并选中。下文以 Clash Verge Rev 为例，其他 Mihomo 客户端使用自己的覆写或合并入口。

公开模板没有节点。仅导入 YAML 后，代理请求会被拒绝；客户端中另一份机场订阅不会自动合并过来。

### 2. 用持久扩展添加订阅

右键这份配置卡片 → 编辑扩展配置，加入以下内容，并替换 URL 占位文字：

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

订阅须返回 Clash 格式的 `proxies` 列表，不能直接填任意 V2Ray 文本订阅。实际 URL 只保存在自己的设备或私密存储中，不上传公共仓库。示例经 DIRECT 下载订阅，网络需要能访问该地址。

保存扩展并重新加载，确认 provider 和分组节点已加载，再在 PROXY 组选出口。手动版的 AI / Google 跟随 PROXY。没有地区匹配时，可以在手动版 PROXY 中直接选节点。

扩展独立保存，主配置刷新后继续应用。不要只修改临时运行配置，也不要用只写「稳定节点」组的 `proxy-groups` 列表覆写整个列表，会丢失其他组。合并规则及应用设置优先级见 [Clash Verge Rev 扩展说明](https://www.clashverge.dev/guide/extend.html)。

### 3. 故障转移版、混合版的稳定出口

添加普通订阅不会自动设置「稳定节点」组，需要绑定自己选定的节点。不会根据节点名称或测速自动判断它适合 AI。

<details>
<summary>Clash Verge Rev：用订阅扩展脚本筛选稳定候选</summary>

以下只用于故障转移版、混合版，且假定上一步 provider 名为 nodes。右键这份配置卡片 → 编辑扩展脚本，把名称替换成客户端显示的完整节点名称：

```javascript
function main(config) {
  const names = ["你的稳定节点完整名称"];
  const stable = config["proxy-groups"].find(group => group.name === "稳定节点");
  if (!stable) throw new Error("当前模式没有「稳定节点」组，请使用故障转移版或混合版");
  const escape = text => text.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");
  stable.use = ["nodes"];
  stable.proxies = [];
  stable.filter = "^(?:" + names.map(escape).join("|") + ")$";
  stable["include-all-providers"] = false;
  stable["empty-fallback"] = "REJECT";
  return config;
}
```

保存并重新加载，检查「稳定节点」组只包含选定节点，AI / Google 仍指向稳定节点。名称不匹配时拒绝请求。可以在 names 中增加候选，但**筛选只决定成员，不决定顺序**；顺序来自 provider。需要固定主备优先级时，应维护有序节点配置，不能把脚本数组或正则顺序当作优先级保证。

</details>

### 4. 检查实际运行

启用规则模式，检查最终生效配置、规则 provider 加载状态和服务组成员。在连接／日志页查看实际访问域名命中的规则、策略、节点，再测试目标 App。

客户端可能接管 IPv6、运行模式、DNS、端口和 TUN。导入 YAML 不会自动开启系统代理或 TUN，也不表示应用设置已与配置一致。旁路由使用相同 Mihomo 语法，但下载、节点注入和重载由网关管理，不使用桌面唤起协议。

## 规则覆盖与网络设置

服务分组包括 AI、Google、YouTube、Telegram、Twitter、Facebook、TikTok，以及上述默认直连服务。游戏分类保留并直接使用 DIRECT，不显示 Game 分组。Netflix、Disney+、Max 不设置独立分组或专属规则：请求继续匹配其余规则，未命中的最终使用 PROXY；命中国内规则仍直连。宽泛海外分类和末尾规则使用 PROXY；更靠前的服务、局域网和国内规则优先匹配。

规则优先级为：网络拒绝 → 基础广告拦截 → 显式自定义规则 → 局域网 → CN 域名直连 → 各服务分类 → 专门服务 IP → CN IP 直连 → 普通海外分类与最终 PROXY。CN 域名与 CN IP 是独立规则集，均直接使用 DIRECT，不额外建立 CN 策略组。命中 CN 域名时优先直连，包括与 Google、Apple、Microsoft、AI 等分类重叠的域名；这些请求不会再进入对应服务组。确需代理的个别域名应放入更靠前的显式自定义规则。

基础广告拦截使用 MetaCubeX 完整版 `category-ads-all`，小火箭通过远程规则集、Mihomo 通过 HTTP provider 加载，匹配后直接 REJECT，不新增广告策略组。广告规则优先于 CN 和服务规则。它不需要证书、HTTPS 解密或脚本，不能保证删除与正常内容共用域名的开屏广告、视频广告。

小火箭不能原样表达上游的一条测速广告正则，因此仅对 `speed.coe.ad.*.prod.hosts.ooklaserver.net` 和 `speed.open.ad.*.prod.hosts.ooklaserver.net` 使用通配替代；相较正则，允许不符合 2–6 位小写字母限制的中间标签，差异写入 manifest。普通测速域名不因这条替代被整体封锁。Mihomo 保留原正则。若出现疑似误拦截，应查看实际命中的广告域名，再审查规则；不要直接关闭所有分流。

AI 分类包含 Anthropic / Claude、OpenAI、Gemini、GitHub Copilot、Cursor 等。Dia 的 `diabrowser.engineering`、Claude 的 `claude.dev`，以及 Cursor 的 `cursor.com`、`cursor.sh`、`cursorapi.com`、`cursor-cdn.com` 均由上游分类覆盖，根域及子域使用 AI；不再重复内嵌规则。上游尚未覆盖的 `cursorvm.com` 保留显式 AI 规则。命中 AI 不代表地区限制已解除。

`kimi.ai`、`moonshot.ai`、`minimax.io` 及其子域默认直连，由前置 CN 域名规则判定，无需额外直连例外；需要代理的具体接口可单独调整。地区组排在服务组之后，作为 PROXY 等组的候选并方便手动选择地区；展示顺序不改变候选顺序或分流规则优先级。

两客户端共享以下网络策略，具体语法分别适配：

- 关闭 IPv6；拒绝 UDP 443（QUIC）。支持回退的应用使用 TCP，其他 UDP 不因此全部关闭。
- 拒绝域名包含 stun 的请求及 UDP 3478；这不是完整 STUN 协议识别，语音、直播连麦等可能受影响。
- 国内／直连解析使用国内 DNS；普通代理解析使用 Cloudflare / Google DNS，并按代理策略处理。Mihomo 的 DIRECT 出口有独立直连 DNS，避免下载依赖尚未准备好的规则或节点。
- 局域网、时间同步及 Windows 连通性检测等保留真实 IP 例外；应用自身设置仍需核对。

地区组测试周期为 600 秒，切换公差为 50 毫秒；fallback 不使用该公差。测试访问 gstatic 的 HTTP 连通性地址，不是 ICMP ping、带宽测速或 Claude / 流媒体可用性检测。两次测试之间可能发生故障，切换不保证即时完成。

## 更新时保留自己的设置

**主配置、分类规则和节点订阅是三件事。** 更新节点不更新分流，更新规则不添加节点。

| 更新内容 | 小火箭 | Mihomo |
| --- | --- | --- |
| 主配置、分组结构与内嵌补充规则 | 下载／更新整份 conf | 更新远程 YAML |
| 分类规则 | 使用配置／编译配置重新拉取 | rule-provider 刷新，当前间隔 24 小时 |
| 节点及认证信息 | 更新自己的节点订阅 | 更新自己的 proxy-provider；示例间隔 24 小时 |

### 小火箭的本地修改

主文件没有 update-url，但 URL 导入可能补回下载来源，不能仅凭远程文件没有该字段就认定不会覆盖。准备编辑「稳定节点」组、服务组或规则前：

1. 在配置编辑页复制一份，给副本改一个易识别的名称，后续使用副本。
2. 如果采用手动维护副本，进入「设置」→ 更新中的「配置」，关闭自动后台更新。**这是全局开关**，影响其他配置及其引用资源的后台更新；节点订阅设置独立。
3. 不对自定义副本执行整份更新或重新导入覆盖。刷新规则用「使用配置／编译配置」；新版改变分组结构时，另行导入并重新设置成员。

复制、改名本身不保证防覆盖；按实际版本检查本地更新地址和开关。更新行为可查阅上方社区手册。

### Mihomo 的本地修改

节点绑定和稳定候选放在持久扩展／覆写中，主 YAML 更新后核对合并结果。Clash Verge Rev 的列表字段整体替换；应用全局设置也可能覆盖配置。不要把节点凭据写入公开文件，不靠临时运行配置保存长期设置。

主文件更新间隔由客户端管理，GitHub Raw 没有本项目专用的客户端自动更新响应头。provider 的 24 小时周期不能替代主文件更新。

### 仓库版本与缓存

MetaCubeX 规则在构建时固定版本，经完整性和适配检查后生成本仓库的分类文件，客户端不直接读取不兼容的上游格式。当前公开版本人工验证后发布，**跨仓库自动发布尚未接入**；定期拉取不会让尚未发布的上游变更自动出现。

Raw 或客户端缓存可能返回旧内容。`manifest.json` 记录来源提交和规则 SHA-256；只更新分类时，主文件更新时间不一定变化。旧设备的配置地址不会自动迁移到这里。

## 常见问题

| 现象 | 先检查 |
| --- | --- |
| 下载失败、规则一直加载 | Raw 是否可访问，下载日志和规则资源加载数；Mihomo 规则经 DIRECT 下载 |
| 导入后没有节点 | 小火箭检查首页订阅；Mihomo 检查持久扩展 provider，而非另一份订阅卡片 |
| AI / Google 被拒绝 | 「稳定节点」组是否仍为 REJECT 或为空，节点名称是否匹配 |
| 地区组／「自动切换」组为空 | 备注是否匹配关键词，订阅是否加载；只带旗帜的备注未必匹配 |
| 首页换节点，服务出口没变 | 检查 PROXY／服务／「稳定节点」组选了什么，本项目由显式分组控制出口 |
| 延迟正常，Claude 仍报地区不可用 | 规则命中、实际出口 IP 和服务限制；通用连通性不代表目标服务可用 |
| 自己添加的成员消失 | 是否覆盖了主配置，设置是否放在持久副本／扩展中 |
| 切换模式后设置不见 | 每份主配置独立设置，检查当前文件与对应扩展 |
| 想让一个服务走指定地区 | 先把地区组加入服务候选，再选择；不能直接选未声明的候选 |
| 语音、直播或部分 App 不通 | UDP 443、STUN 限制及节点 UDP 能力，依据连接日志判断 |

## 来源、兼容性与验证

唯一直接规则上游为 [MetaCubeX/meta-rules-dat](https://github.com/MetaCubeX/meta-rules-dat) 完整版，使用完整 geosite classical 与 geoip 分类，不使用 geo-lite。MetaCubeX 聚合多份社区数据，来源和许可见 [第三方说明](THIRD_PARTY_NOTICES.md)。配置策略由本项目维护。

Mihomo 保留原生域名正则；小火箭使用明确列出的客户端适配，少数通配替代范围更宽。来源版本、完整性与差异见 [manifest.json](manifest.json)，未知规则或未审查的适配会阻止构建，不静默删规则。

已有验证覆盖生成、规则完整性、Mihomo 原生加载、无缓存域名解析／HTTP 下载及测试节点绑定。小火箭真机导入、Clash 一键唤起和持久扩展界面、实际 App 访问仍需设备验收。
