# 手机 DNS 与蜂窝 TCP 配置

两份配置引用相同的 Rule 清单；节点认证仍留在手机，仓库不创建节点。

| 配置 | 网络用途 | AI | 日本／美国地区组 |
|---|---|---|---|
| [personal.conf](https://raw.githubusercontent.com/jeremiahlordxie-winterveil/PersonalProxyRules/main/Shadowrocket/personal.conf) | Wi-Fi／默认 | 两条 ISP 前置自动测速 | 保留现有节点池与 url-test |
| [personal-cellular.conf](https://raw.githubusercontent.com/jeremiahlordxie-winterveil/PersonalProxyRules/main/Shadowrocket/personal-cellular.conf) | 蜂窝 | 只允许 WEBSHARE-US-ISP-VLESS | 日本仅日本 VLESS；美国仅 ISP-VLESS、美西-VPS；仍为 url-test |

蜂窝版的一般代理和最终兜底使用“蜂窝默认TCP”，候选为美西-VPS、日本-软银SOFTBANK。移除未核实协议的地区候选以及内置 PROXY 引用，避免通过首页当前节点意外选中 HY2。原直连规则仍直连；币安/Bybit仍映射日本组，OKX仍映射美国组。原有 DIRECT 选项保留。

## 一次性手机设置

1. 更新已有 personal.conf，保留旧配置用于回退；另添加一次 personal-cellular.conf。以后两份均通过各自内置 update-url 更新原配置，不必反复添加。
2. 核对本地节点名称完全一致：`美西-VPS`、`日本-软银SOFTBANK`、`WEBSHARE-US-ISP-VLESS`。用户已确认日本节点协议为 VLESS；仍须确认其底层传输使用 TCP（如 TCP、WS、gRPC），没有使用 QUIC/KCP。核对 ISP 节点的“代理通过”明确指向美西 VLESS，不指向 HY2、自动组或 DIRECT。
3. 在小火箭原生“场景”中建立目标映射：蜂窝网络 → 使用配置 personal-cellular.conf；默认／Wi-Fi → 使用配置 personal.conf，路由模式均使用“配置”。最后启用场景路由。不要选择全局“代理”模式，否则规则分流不会按本配置执行。
4. 具体入口、字段和网络匹配优先级以当前手机版界面为准；本轮没有远程操作手机，不能把文件发布当作场景已经启用。若界面不能选择配置文件，先核对版本和场景页面，不要用未经确认的 ssid 配置语法代替。

蜂窝版保留 url-test。只有一条候选的日本组无法在另一条 TCP 日本线路中自动避障；该节点不可用时不要改走美国或直连来代替交易所日本出口。网络切换时已有长连接可能仍保持原路径，验收时重新建立测试连接，必要时重连隧道。

## DNS 改动与验证边界

两份配置的默认、备用 DNS 改为 Cloudflare DoH，并通过明确的 ISP-VLESS 节点；DIRECT 域名与代理节点域名的引导解析使用阿里 DoH。删除原普通 DNS、system 备用和 Apple/iCloud 的 system 解析例外。DNS 路径固定使用 VLESS 前置 ISP，与正常配置中的业务线路优选分开。

四个实际端点已在 Windows 验证 TLS 证书、HTTP 200 和 DNS 正常响应：1.1.1.1、1.0.0.1 经 VLESS→ISP；223.5.5.5、223.6.6.6 直接建立 TLS。该验证不能证明 Shadowrocket 已按参数执行，仍需手机连接日志和复测。保留原 IPv6、DNS 劫持范围及 UDP/QUIC 策略，没有宣称系统全部 DNS 都被接管。

截图中电脑此次只检测到 ISP 递归解析器；手机检测到了国内及香港解析器。解析器地址和所在地不能单独证明 DNS 明文泄漏，也不能证明经过了哪条传输路径。本配置允许 DIRECT 域名使用国内加密 DNS，不能以“所有结果都必须美国”为唯一判断标准。

更新后分别在 Wi-Fi、蜂窝重新连接，进行扩展 DNS 测试并检查连接日志。重点确认：代理测试域名是否命中代理 DNS、蜂窝新连接有无 HY2、AI 最终是否仍为 ISP 出口、币安/Bybit是否仍是日本出口、OKX是否仍是美国出口。测试失败时保留日志并切回旧配置，不把 DNS 测试网页单次通过当作整机无泄漏证明。

DNS 协议依据：[Cloudflare DoH 文档](https://developers.cloudflare.com/1.1.1.1/encryption/dns-over-https/)、[阿里公共 DNS](https://www.alidns.com/)。小火箭发布方的 [App Store 页面](https://apps.apple.com/us/app/shadowrocket/id932747118)列有加密 DNS 功能及场景相关更新；本说明中的场景目标映射仍待实际手机版验证。
