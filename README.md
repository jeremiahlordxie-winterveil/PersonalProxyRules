# PersonalProxyRules

Shadowrocket 与 Clash Verge Rev / Mihomo 的共享分类规则与客户端适配候选。

## 当前进度

- Windows 已单独完成 ISP 接入：HY2 → Webshare 和 VLESS → Webshare，两条测试出口均为同一个 ISP IP。AI 仅可选择这两条链；普通 Google 使用独立的自有 VPS 测速组。
- 按用户最新要求，Windows当前使用的配置已移除InstaBox节点及其选择/自动组引用；保留美西HY2、VLESS、日本HY2和两条ISP链。手机分组按其最新导出单独保留。
- 本仓库提供 35 份共享规则，以及两端引用它们的配置候选。**整套统一规则尚未切换到两端生产环境。**
- 手机交易所实际节点与公网出口仍待核对；电脑现用基线没有三个专用规则，不能把新映射称为原出口的等效迁移。

## 文件

| 路径 | 用途 |
|---|---|
| `Rule/*.txt` | 共享 classical 文本规则；没有策略组名称、节点认证信息 |
| `Shadowrocket/personal.conf` | 手机候选；基于原完整配置保留本地例外、组、DNS/IPv6/QUIC/UDP 设置 |
| `Mihomo/rules.fragment.yaml` | 电脑规则引用片段；不是完整可运行配置，不可直接替换现有订阅 |
| `Mihomo/groups.fragment.yaml` | 使用电脑已有节点的美日自动测速组；两组均为 url-test |
| `SOURCES.json` | 上游来源、快照哈希、各文件条目数与无法通用的规则计数 |
| `LICENSE` | GPL-2.0；衍生自 blackmatrix7/ios_rule_script 的规则保留其授权要求 |

共享指两端使用相同的分类数据。两端节点名称、策略组映射、DNS/TUN 和 UDP 拒绝措施分别适配。手机仍有本地 Muse、交易所、Notion、Kelivo 等规则；电脑片段增加了 AI UDP 显式拒绝。不能把不同客户端的语法或保护措施直接互换。

## AI 与 Google

AI 候选清单有 88 条，覆盖主要 AI 产品，并补入 `chat.com`、Gemini 专用 API、Microsoft Copilot 等明确遗漏。它不是对全部 AI 域名的完整证明；新产品及附属端点仍需按连接日志维护。普通 `googleapis.com` 不整体归入 AI。

- 手机 AI 组只连接本地“美国静态住宅”组，该组引用本地已有 `WEBSHARE US ISP` 节点。该节点的代理通过关系需在手机核对；本仓库不创建含凭据节点，也未在真实手机验证。
- 电脑 AI 组名为 `🤖 AI（严格ISP）`，必须仅包含真实 ISP 链路。不可加 DIRECT 或 VPS 直出备用。
- 电脑普通 Google 映射到 `🌐 Google（速度优先）`，与 AI 拆组。
- Webshare 链按当前已知能力只承载 TCP。Mihomo 对无 UDP 能力的节点可能继续向下匹配，故片段在 AI 关键词及规则集前显式拒绝对应 UDP；不能只依靠节点的 `udp: false`。

## 交易所保护与待验收项

手机三条规则及其前面的 Muse 规则相对位置保留：

```text
DOMAIN-KEYWORD,bybit,🇯🇵日本节点
DOMAIN-KEYWORD,binance,🇯🇵日本节点
DOMAIN-KEYWORD,okx,🇺🇸美国节点
```

手机日本、美国组的原成员和 `url-test` 参数保留；它们是自动组，组名不证明固定国家或固定 IP。电脑片段需要自行建立同名地区组，并按手机实测结果明确映射。尚未验收前不要把整个片段投入交易所流量。

手机基线已更新为用户于2026-11-07提供的 `lazy_group1.conf`：静态住宅组包含两条 ISP 线路并使用 url-test；美国地区组保留新成员、顺序与测速参数，日本组也原样保留。电脑地区组片段均使用 url-test，按手机600秒周期、100毫秒容差、5秒超时适配；目前电脑可用的是1条日本线路、4条美国线路（含两条ISP链），不会凭名称编造手机其余订阅节点。因此两端分类可共享，但节点池不完全相同，不能承诺同一瞬间选择同一出口IP。

手机候选只修改 AI 组为严格 ISP、规则 URL 指向本仓库、规范已有非交易所目标组的大小写、删除无效的完整 URL 域名规则，并增加链缺失时关闭连接的设置。客户端版本支持、真实代理链、DNS/IPv6/UDP 仍需手机验收。原 DNS 的国内普通 DNS、system 回退及 Apple/iCloud 系统 DNS 例外并未在此阶段宣称修复。

## 如何继续部署

1. 保留目前已接通的 Windows ISP 配置；不要直接用本仓库的片段覆盖它。
2. 在手机记录日本、美国自动组当前实际节点及公网出口，并核对 Binance、Bybit、OKX 的连接日志。
3. 对照电脑候选的本地组映射。Mihomo 做内核校验与出口验证；手机候选另行导入，先不覆盖旧配置。
4. 验收后再启用整套统一规则。两端引用本仓库 main 上的 Rule 文件，后续更新分类数据时刷新远程规则即可。

手机候选原始链接：[personal.conf](https://raw.githubusercontent.com/jeremiahlordxie-winterveil/PersonalProxyRules/main/Shadowrocket/personal.conf)。

## 来源与转换限制

规则数据主要来自 [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)，按原手机的远程分类和优先级生成。`HOST` 系列转换为 `DOMAIN` 系列，`IP6-CIDR` 转换为 `IP-CIDR6`，去掉 Quantumult X 的出站字段，并保留适用的 `no-resolve` 参数。AI 数据以用户提供的草案为基础修订。

本轮没有纳入共享数据的上游内容：130 条 USER-AGENT、1 条 URL-REGEX。它们依赖客户端特有能力，故此候选不能声称对原配置全部行为完全等效。部分旧规则本身较宽泛；对其含义的进一步优化应独立评估，避免在统一数据格式时擅自更改分类。

凭据和完整本地节点只留在客户端及私密配置，不能提交本仓库。
