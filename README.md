# v2rayN 自定义分流规则集 (Routing Rules)

本仓库提供一套适用于 v2rayN 的自定义路由分流规则，包含广告拦截、局域网直连、国外代理、AI 服务分流及特定节点分流等策略。

---

## 📌 规则配置预览

| 规则名称 | 动作 (outboundTag) | 端口 / 协议 | 匹配条件 (Domain / IP) |
| :--- | :--- | :--- | :--- |
| **UDP443阻断** | `block` | Port: `443` / Network: `udp` | 针对 QUIC 协议阻断，提升加载稳定性 |
| **局域网专用规则** | `direct` | - | `geosite:private`, `geoip:private` |
| **广告拦截** | `block` | - | `geosite:category-ads-all` |
| **进程 / 常用直连** | `direct` | - | `geosite:microsoft`, 自定义域名（博客、自建服务等） |
| **油管专用规则** | `proxy` | - | `geosite:youtube` |
| **AI 专用规则** | `11` *(示例节点/策略)* | - | `geosite:category-ai-!cn` |
| **CF / Google / GitHub** | `jp` *(特定节点)* | - | `geosite:cloudflare`, `geosite:google`, `geosite:github`, `gist.github.com` 等 |
| **国内直连** | `direct` | - | `geosite:cn`, `geoip:cn` |
| **国外代理** | `proxy` | Port: `0-65535` | 兜底代理规则 |

---

## 🚀 使用方法

### 1. 获取订阅 / 规则 URL
点击本仓库中的规则 JSON 文件，点击右上角的 **`Raw`** 按钮，复制浏览器地址栏的完整链接。

> **国内优化链接**（若直连 Raw 链接较慢，建议使用 CDN 加速）：
> `https://cdn.jsdelivr.net/gh/你的GitHub用户名/仓库名@main/规则文件名.json`

### 2. 导入 v2rayN
1. 打开 **v2rayN** 客户端。
2. 进入 **设置** -> **路由设置** -> **自定义规则集**（或路由规则列表顶部菜单）。
3. 点击 **从订阅 Url 中导入规则**。
4. 将复制的 `Raw` / `jsDelivr` 链接粘贴到输入框中，点击 **确定** 保存。
5. 更新规则列表，选中生效即可。

---

## 📝 说明与提醒
* 如果导入报错或请求超时，请检查网络是否能够正常访问 GitHub Raw 节点，或尝试开启全局代理后再操作。
* 规则文件（`.json`）更新后，只需在 v2rayN 中点击“更新规则”即可自动同步最新配置。