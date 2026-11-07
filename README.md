# PersonalProxyRules

Shadowrocket 与 Clash Verge Rev / Mihomo 的共享分类规则与客户端适配候选。

## 当前进度

- Windows 已单独完成 ISP 接入：HY2 → Webshare 和 VLESS → Webshare，两条测试出口均为同一个 ISP IP。AI 仅可选择这两条链；普通 Google 使用独立的自有 VPS 测速组。
- 按用户最新要求，Windows当前使用的配置已移除InstaBox节点及其选择/自动组引用；保留美西HY2、VLESS、日本HY2和两条ISP链。手机分组按其最新导出单独保留。
- 本仓库提供 37 份共享规则，以及两端引用它们的配置候选。**整套统一规则尚未切换到两端生产环境。**
- 手机交易所实际节点与公网出口仍待核对；电脑现用基线没有三个专用规则，不能把新映射称为原出口的等效迁移。

## 文件

| 路径 | 用途 |
|---|---|
| `Rule/*.txt` | 共享 classical 文本规则；没有策略组名称、节点认证信息 |
| `Shadowrocket/personal.conf` | 手机默认配置；保留原节点池与分组，调整为加密 DNS |
| `Shadowrocket/personal-cellular.conf` | 蜂窝候选；仅保留已确认的 VLESS 节点，AI 只走 VLESS 前置 ISP |
| `Shadowrocket/network-and-dns.md` | 两份配置的更新、场景切换和 DNS 验收说明 |
| `Mihomo/rules.fragment.yaml` | 电脑规则引用片段；不是完整可运行配置，不可直接替换现有订阅 |
| `Mihomo/groups.fragment.yaml` | 使用电脑已有节点的美日自动测速组；两组均为 url-test |
| `SOURCES.json` | 上游来源、快照哈希、各文件条目数与无法通用的规则计数 |
| `LICENSE` | GPL-2.0；衍生自 blackmatrix7/ios_rule_script 的规则保留其授权要求 |

共享指两端使用相同的分类数据。两端节点名称、策略组映射、DNS/TUN 和 UDP 拒绝措施分别适配。手机本地保留交易所、Notion、Kelivo 等规则；Muse、OpenRouter 已并入 `Rule/AI.txt`，电脑片段对整个 AI 规则集显式拒绝对应 UDP。不能把不同客户端的语法或保护措施直接互换。

## AI 与 Google

AI 候选清单有 88 条，覆盖主要 AI 产品，并补入 `chat.com`、Gemini 专用 API、Microsoft Copilot 等明确遗漏。它不是对全部 AI 域名的完整证明；新产品及附属端点仍需按连接日志维护。普通 `googleapis.com` 不整体归入 AI。

Muse、OpenRouter 沿用原有关键词匹配范围，统一存放在 `Rule/AI.txt`，客户端不再保留两条前置例外。它们统一经过 AI → 美国静态住宅 → ISP。三家交易所专用规则仍排在 AI 规则集之前；同时含交易所关键词与Muse/OpenRouter关键词的域名优先按交易所规则处理。

- 手机 AI 组只连接本地“美国静态住宅”组，该组引用本地已有 `WEBSHARE US ISP` 与 `WEBSHARE-US-ISP-VLESS` 节点并自动测速。节点的代理通过关系需在手机核对；本仓库不创建含凭据节点。
- 电脑 AI 组名为 `🤖 AI（严格ISP）`，必须仅包含真实 ISP 链路。不可加 DIRECT 或 VPS 直出备用。
- 电脑普通 Google 映射到 `🌐 Google（速度优先）`，与 AI 拆组。
- Webshare 链按当前已知能力只承载 TCP。Mihomo 对无 UDP 能力的节点可能继续向下匹配，故片段在 AI 规则集前显式拒绝对应 UDP；不能只依靠节点的 `udp: false`。

## 交易所保护与待验收项

交易所按出口合并管理，币安/Bybit仍走日本自动组，OKX仍走美国自动组。三条匹配条件分别存放在两份清单中，主配置只需两条引用，并且优先于AI规则集：

```text
RULE-SET,https://raw.githubusercontent.com/jeremiahlordxie-winterveil/PersonalProxyRules/main/Rule/Exchanges-JP.txt,🇯🇵日本节点
RULE-SET,https://raw.githubusercontent.com/jeremiahlordxie-winterveil/PersonalProxyRules/main/Rule/Exchanges-US.txt,🇺🇸美国节点
```

手机日本、美国组的原成员和 `url-test` 参数保留；它们是自动组，组名不证明固定国家或固定 IP。电脑片段需要自行建立同名地区组，并按手机实测结果明确映射。尚未验收前不要把整个片段投入交易所流量。

`Rule/Exchanges-JP.txt` 内依次为 Bybit、Binance 两条关键词规则，`Rule/Exchanges-US.txt` 为 OKX 关键词规则；因此旧规则的匹配范围和日本优先于美国的冲突处理顺序保留。合并的是同一出口的规则清单，不会把三家交易所强制绑到同一个节点。

手机基线已更新为用户于2026-11-07提供的 `lazy_group1.conf`：静态住宅组包含两条 ISP 线路并使用 url-test；美国地区组保留新成员、顺序与测速参数，日本组也原样保留。电脑地区组片段均使用 url-test，按手机600秒周期、100毫秒容差、5秒超时适配；目前电脑可用的是1条日本线路、4条美国线路（含两条ISP链），不会凭名称编造手机其余订阅节点。因此两端分类可共享，但节点池不完全相同，不能承诺同一瞬间选择同一出口IP。

手机配置将 AI 组设为严格 ISP、规则 URL 指向本仓库、规范已有非交易所目标组的大小写、删除无效的完整 URL 域名规则，并增加链缺失时关闭连接的设置。Muse/OpenRouter 合入 AI，交易所合并为日本/美国两份清单。默认版地区组成员及自动测速参数保持；蜂窝版缩减为已确认的 VLESS 候选。两份配置均已调整 DNS，端点测试通过，客户端实际 DNS/IPv6/UDP 行为仍需手机验收。

### 蜂窝 TCP 与 DNS

按网络切换使用两份配置：默认版保持现有自动优选，蜂窝版 AI 仅走 `WEBSHARE-US-ISP-VLESS`，日本／美国组保留对应地区的已确认 TCP 候选；两份规则清单相同，地区组仍为 url-test。蜂窝版需首次添加一次，再通过小火箭原生场景设置蜂窝／默认配置映射。发布文件不等于手机场景已启用，VLESS 底层传输及代理通过设置也需设备核对。

DNS 已移除原普通 DNS、system 备用和 Apple/iCloud 系统解析例外，改用经 VLESS 前置 ISP 的 Cloudflare DoH，以及供 DIRECT／节点引导使用的阿里 DoH。四个端点的 TLS 和真实解析测试通过，手机泄漏复测尚未完成。详见 [手机网络与 DNS 说明](Shadowrocket/network-and-dns.md)。

## 如何继续部署

1. 保留目前已接通的 Windows ISP 配置；不要直接用本仓库的片段覆盖它。
2. 在手机记录日本、美国自动组当前实际节点及公网出口，并核对 Binance、Bybit、OKX 的连接日志。
3. 对照电脑候选的本地组映射。Mihomo 做内核校验与出口验证；手机候选另行导入，先不覆盖旧配置。
4. 验收后再启用整套统一规则。两端引用本仓库 main 上的 Rule 文件，后续更新分类数据时刷新远程规则即可。

手机候选原始链接：[personal.conf](https://raw.githubusercontent.com/jeremiahlordxie-winterveil/PersonalProxyRules/main/Shadowrocket/personal.conf)。

### 长期更新入口

小火箭长期使用上面的 `main` 链接。配置内部的 `update-url` 已指向同一地址：

```text
https://raw.githubusercontent.com/jeremiahlordxie-winterveil/PersonalProxyRules/main/Shadowrocket/personal.conf
```

首次添加后，在同一份配置上使用更新功能即可取得以后推送的配置，不需要每次新增导入。此前提供的固定提交版本中也已包含这个 `update-url`，如已导入可直接检查并使用其更新功能。单独修改远程规则文件时，需让客户端刷新相应规则集；配置更新与规则缓存刷新由客户端分别处理。本轮已核对仓库main地址可访问且文件内更新地址正确，尚未在用户手机点按验证。

完整节点认证信息保留在本地，不放入公共仓库。电脑当前ISP接入版本的AI提供器已直接引用同一个 `main/Rule/AI.txt`（HTTP，86400秒更新间隔，可手动刷新规则提供器），并保留已有GEOSITE补全及AI UDP拒绝措施。其它整套分类和交易所新映射仍保留为候选，不表示电脑已启用整套37份规则。

## 来源与转换限制

规则数据主要来自 [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)，按原手机的远程分类和优先级生成。`HOST` 系列转换为 `DOMAIN` 系列，`IP6-CIDR` 转换为 `IP-CIDR6`，去掉 Quantumult X 的出站字段，并保留适用的 `no-resolve` 参数。AI 数据以用户提供的草案为基础修订。

本轮没有纳入共享数据的上游内容：130 条 USER-AGENT、1 条 URL-REGEX。它们依赖客户端特有能力，故此候选不能声称对原配置全部行为完全等效。部分旧规则本身较宽泛；对其含义的进一步优化应独立评估，避免在统一数据格式时擅自更改分类。

凭据和完整本地节点只留在客户端及私密配置，不能提交本仓库。
