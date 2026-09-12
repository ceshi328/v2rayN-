# 🚀 v2rayN 自定义路由分流规则配置指南

本仓库/配置文件提供了一套经过优化的 **v2rayN 自定义路由规则集 (Routing Rules)**。通过精确匹配请求域名、IP 与传输协议，实现广告拦截、局域网直连、国外代理、AI/社交媒体专属节点分流等功能，提升日常上网体验与访问效率。

---

## 📋 规则匹配逻辑与顺序说明

v2rayN 路由匹配遵循 **从上到下、首条命中即生效** 的原则。当前配置的匹配顺序如下：

| 顺序 | 规则名称 | 出站标记 (`outboundTag`) | 说明 / 匹配内容 |
| :---: | :--- | :--- | :--- |
| **1** | **UDP 443 阻断** | `block` | 阻断 UDP 443 端口（QUIC 协议），强制浏览器回退到 TCP 协议，大幅提升 YouTube / Google 等网站加载稳定性。 |
| **2** | **局域网专用规则** | `direct` | 直连内网 IP (`geoip:private`) 与内网域名 (`geosite:private`)，确保路由器管理页、NAS 等访问正常。 |
| **3** | **广告拦截** | `block` | 自动拦截常见广告与追踪域名 (`geosite:category-ads-all`)。 |
| **4** | **GitHub 专用** | `proxy` | 强制 `geosite:github` 走代理，解决国内直连被污染/阻断的问题。 |
| **5** | **指定直连域名** | `direct` | 微软服务及**特定自定义域名直连**（如个人博客、特定工具网站等）。 |
| **6** | **社交平台** | `11` *(示例标签)* | 匹配 OpenAI、Facebook、TikTok、Instagram、X/Twitter、Discord 等平台。 |
| **7** | **AI 服务** | `11` *(示例标签)* | 匹配非大陆 AI 服务域名 (`geosite:category-ai-!cn`)。 |
| **8** | **YouTube 专用** | `US1` *(示例标签)* | 匹配 YouTube 专用域名 (`geosite:youtube`)，可定向分配至美国节点以获取最佳体验。 |
| **9** | **国内直连** | `direct` | 匹配大陆域名 (`geosite:cn`) 与大陆 IP (`geoip:cn`)，实现国内流量不走代理。 |
| **10** | **国外兜底代理** | `proxy` | 兜底规则，其余未匹配的国外流量全部通过代理节点出站 (`0-65535`)。 |

---

## ⚙️ 直连域名（Direct Domains）修改指南（重点）

规则集中的第 5 条为 **直连域名规则 (`remarks: "直连域名"`)**。此规则的作用是让指定的域名绕过代理、直接连接。

### ⚠️ 注意事项：
* **如果不需要某些内置域名**：部分内置域名（如个人博客、工具网等）仅适用于特定原作者。**如果您不需要这些域名直连，建议直接删除**，否则可能导致这些网站因直连而打不开。
* **自定义添加**：您可以把不需要走代理的大陆边缘服务、个人自建服务或局域网域名加入该列表中。

### 修改示例：

在 JSON 配置文件中找到以下段落：

```json
{
  "outboundTag": "direct",
  "domain": [
    "geosite:microsoft",
    "domain:shasha.website",
    "domain:playback-speed.com",
    "domain:xiaozhukankan.com",
    "domain:pinoymovieshub.win",
    "domain:clashverge.dev"
  ],
  "enabled": true,
  "remarks": "直连域名"
}
```

#### 1. 精简/删除不需要的域名（推荐）
如果您只需要微软服务直连，可以将多余域名删除，修改为：
```json
{
  "outboundTag": "direct",
  "domain": [
    "geosite:microsoft"
  ],
  "enabled": true,
  "remarks": "直连域名"
}
```

#### 2. 添加您自己的直连域名
如果想添加自定义域名（例如 `myblog.com`），使用 `domain:` 前缀追加在列表中（注意末尾的逗号）：
```json
{
  "outboundTag": "direct",
  "domain": [
    "geosite:microsoft",
    "domain:myblog.com",
    "domain:nas.local"
  ],
  "enabled": true,
  "remarks": "直连域名"
}
```

---

## 📥 如何导入到 v2rayN

### 方式一：直接在 v2rayN 界面导入 JSON 内容

1. 复制下方 [完整 JSON 配置模版](#-完整-json-配置模版) 中的完整文本。
2. 打开 **v2rayN**，点击顶部菜单 **路由设置** -> **基础功能**（或选择当前的路由规则集）。
3. 点击 **高级功能** / **高级路由设置** -> 选择 **从剪贴板导入规则** 或直接替换现有 JSON 规则。
4. 点击 **确定** 并保存。

### 方式二：保存为 `.json` 文件并导入

1. 在本地新建一个文本文件，将 JSON 内容粘贴进去，保存为 `custom_routing.json`。
2. 打开 v2rayN，进入 **路由设置**。
3. 点击 **从文件导入规则**，选择该 `.json` 文件。
4. 确保将其应用为**当前激活的路由规则**。

---

## 📄 完整 JSON 配置模版

您可复制以下已还原（解码 Unicode 注释）的完整 JSON 配置，根据需求修改后使用：

```json
[
  {
    "port": "443",
    "network": "udp",
    "outboundTag": "block",
    "enabled": true,
    "remarks": "UDP443阻断"
  },
  {
    "outboundTag": "direct",
    "ip": [
      "geoip:private"
    ],
    "domain": [
      "geosite:private"
    ],
    "enabled": true,
    "remarks": "局域网专用规则"
  },
  {
    "outboundTag": "block",
    "domain": [
      "geosite:category-ads-all"
    ],
    "enabled": true,
    "remarks": "广告拦截"
  },
  {
    "outboundTag": "proxy",
    "domain": [
      "geosite:github"
    ],
    "enabled": true,
    "remarks": "github"
  },
  {
    "outboundTag": "direct",
    "domain": [
      "geosite:microsoft",
      "domain:shasha.website",
      "domain:playback-speed.com",
      "domain:xiaozhukankan.com",
      "domain:pinoymovieshub.win",
      "domain:clashverge.dev"
    ],
    "enabled": true,
    "remarks": "直连域名"
  },
  {
    "outboundTag": "11",
    "domain": [
      "geosite:openai",
      "domain:openai.com",
      "geosite:facebook",
      "domain:facebook.com",
      "domain:fbcdn.net",
      "geosite:tiktok",
      "domain:tiktok.com",
      "domain:tiktokcdn.com",
      "geosite:instagram",
      "domain:instagram.com",
      "domain:cdninstagram.com",
      "geosite:twitter",
      "domain:x.com",
      "domain:twitter.com",
      "domain:twimg.com",
      "geosite:threads",
      "domain:threads.net",
      "geosite:pinterest",
      "domain:pinterest.com",
      "geosite:reddit",
      "domain:reddit.com",
      "domain:redditmedia.com",
      "geosite:discord",
      "domain:discord.com",
      "domain:discordapp.com"
    ],
    "enabled": true,
    "remarks": "社交"
  },
  {
    "outboundTag": "11",
    "domain": [
      "geosite:category-ai-!cn"
    ],
    "enabled": true,
    "remarks": "AI专用规则"
  },
  {
    "outboundTag": "US1",
    "domain": [
      "geosite:youtube"
    ],
    "enabled": true,
    "remarks": "油管专用规则"
  },
  {
    "outboundTag": "direct",
    "ip": [
      "geoip:cn"
    ],
    "domain": [
      "geosite:cn"
    ],
    "enabled": true,
    "remarks": "国内直连"
  },
  {
    "port": "0-65535",
    "outboundTag": "proxy",
    "enabled": true,
    "remarks": "国外代理"
  }
]
```

---

## ❓ 常见问题 (FAQ)

1. **为什么部分直连域名在修改前打不开？**
   * 因为直连域名列表中的部分域名在国内没有 CDN 加速或被 SNI 阻断，如果它们被强制设置在 `direct`（直连）出站中，就会导致无法连接。把不需要的域名从 `direct` 规则中删掉即可恢复走代理。
2. **`outboundTag: "11"` 或 `"US1"` 是什么意思？**
   * 这些是指定节点/组的标签（Tag）。请确保您的 v2rayN 客户端中存在别名为 `11` 或 `US1` 的自定义节点组，否则请将它们修改为通用代理标签 `proxy`。
3. **如何开启/关闭某条规则？**
   * 将目标规则项中的 `"enabled": true` 修改为 `"enabled": false` 即可临时禁用该条分流策略，亦或者直接删除。