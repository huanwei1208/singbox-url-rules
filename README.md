# singbox-url-rules

Public-safe rule profiles for personal VPN clients.

This repository only stores client-side routing rules. It intentionally does not
store VLESS URLs, UUIDs, Reality keys, subscription URLs, ntfy URLs, or any other
proxy credentials.

## Shadowrocket

Profile:

```text
shadowrocket/shadowrocket-cn-direct.conf
```

Recommended import URL:

```text
https://raw.githubusercontent.com/huanwei1208/singbox-url-rules/master/shadowrocket/shadowrocket-cn-direct.conf?cb=2026071202-geolite-lite
```

Cache-busting URL for immediate refresh after edits:

```text
https://raw.githubusercontent.com/huanwei1208/singbox-url-rules/master/shadowrocket/shadowrocket-cn-direct.conf?cb=2026071202-geolite-lite
```

Fallback URL:

```text
https://raw.githubusercontent.com/huanwei1208/singbox-url-rules/master/shadowrocket/shadowrocket-cn-direct.conf
```

jsDelivr mirror:

```text
https://cdn.jsdelivr.net/gh/huanwei1208/singbox-url-rules@main/shadowrocket/shadowrocket-cn-direct.conf
```

Note: GitHub raw can cache 404 or stale content for a few minutes. jsDelivr can
cache branch content longer. If an update does not appear immediately, use the
cache-busting GitHub raw URL first.

## Clash Verge / Mihomo

Clash Verge cannot directly import the Shadowrocket `.conf` profile. Clash
Verge uses Clash/Mihomo YAML, so use the Clash-specific rule-provider files:

```text
https://raw.githubusercontent.com/huanwei1208/singbox-url-rules/main/clash-verge/ruleset-reject.yaml
https://raw.githubusercontent.com/huanwei1208/singbox-url-rules/main/clash-verge/ruleset-direct.yaml
https://raw.githubusercontent.com/huanwei1208/singbox-url-rules/main/clash-verge/ruleset-proxy.yaml
```

Template profile with placeholder node fields:

```text
https://raw.githubusercontent.com/huanwei1208/singbox-url-rules/main/clash-verge/profile-template.yaml
```

Do not publish a filled Clash Verge profile because it contains VLESS Reality
credentials. Keep the full client profile private and only publish these rule
files.

## Intended Behavior

Use this profile with your VLESS Reality node stored locally in Shadowrocket.

Rule behavior:

- The Shadowrocket profile is intentionally compact and assumes an up-to-date
  GeoLite database. Explicit rules are limited to routing exceptions that can
  override GeoLite: domestic apps using non-China CDN addresses, critical
  foreign services using regional CDN addresses, Apple, banking, and selected
  financial services.
- Private and local networks go `DIRECT`.
- The VPN server IP goes `DIRECT` to avoid proxy loops.
- DNS uses encrypted AliDNS with encrypted Tencent DoH as the fallback.
  Proxy domains are resolved through the selected proxy, and common hard-coded
  public DNS destinations are intercepted by Shadowrocket.
- IPv6 is disabled. Unsupported proxy UDP is rejected instead of silently
  falling back to the local network, and QUIC is disabled only for proxied
  traffic so VLESS/TCP uses the more stable HTTP/TCP path.
- Apple App Store, Apple CDN, Apple ID, and iCloud basics go `DIRECT`, so app
  downloads and updates keep working when the profile is enabled.
- Mainland China AI tools go `DIRECT`, including DeepSeek, Kimi, Tongyi/Qwen,
  Doubao, Baidu Wenxin, Tencent Yuanbao/Hunyuan, Zhipu/ChatGLM, MiniMax,
  iFlyTek Spark, Metaso, and similar domestic services.
- Mainland China apps, payments, video, and commerce go `DIRECT`, including
  Alipay, WeChat/Weixin, Douyin, Bilibili, Alibaba/Taobao/Tmall, JD,
  Pinduoduo, Meituan, Zhihu, CHSI, 163/Netease, WPS/Kingsoft, and common
  domestic video platforms.
- Mainland China banking domains go `DIRECT`, including China Everbright Bank
  and the common ICBC, CCB, ABC, BOC, Bank of Communications, CMB, CITIC,
  Minsheng, SPD, Industrial Bank, PSBC, Huaxia, Ping An, and CGB domain families.
- Tencent Meeting/Wemeet/VooV Meeting goes `DIRECT`. Clash/Mihomo profiles do
  not use broad port-based STUN/TURN rejects, because those can break meeting
  audio/video paths.
- Report-observed domestic endpoints go `DIRECT`, including BOSS Zhipin,
  Yupoo, Tonghuashun/10jqka, Duolingo China endpoints, and iCloud China
  endpoints.
- Z.ai/Zhipu AI goes `DIRECT`; report data showed stale clients were still
  sending `z.ai` through the node.
- Hong Kong domains/IPs go `DIRECT`. Hong Kong brokerage app families such as
  Longbridge, Futu/Futubull, Moomoo, Tiger Brokers, Webull, uSMART, and SoFi HK
  are also direct.
- Common non-China AI tools go `PROXY` before CN/GEOIP rules are evaluated.
- Common foreign SaaS, design, developer, storage, payment, and content
  platforms go `PROXY`, including Notion, Figma, Canva, Dropbox, Atlassian,
  Airtable, Vercel, Netlify, Docker Hub, npm, PyPI, Stripe, PayPal, Medium,
  Substack, Wikipedia, Spotify, and Netflix.
- Google ecosystem traffic goes `PROXY`, including Google Search, Gemini,
  Google login/API/CDN/helper domains, YouTube, Google Ads/Analytics helper
  domains, and Google video/static asset domains.
- Foreign social media, messaging, collaboration, and developer platforms go
  `PROXY`, including X/Twitter, Instagram, Facebook, Threads, WhatsApp, Signal,
  Slack, Zoom, Skype, Teams, Telegram/Telegraph, Reddit, Discord, LinkedIn, GitHub,
  GitLab, Bitbucket, and Stack Overflow.
- TradingView goes `PROXY`, including charting, screeners, explore-style pages,
  widgets, websocket/pushstream, and static assets.
- Wise, BIT/Bitget, OKX, Binance, BiyaPay, Robinhood, Plasma One Card, and
  iFAST GB go `PROXY` for route consistency through the same personal VPN exit.
- China domains and China IPs go `DIRECT`.
- Everything else goes `PROXY`.
- Common WebRTC/STUN/TURN domains are rejected as a generic privacy guard.

The final routing order is: explicit DIRECT exceptions, explicit PROXY
exceptions, `.cn/.hk` and `GEOIP,CN/HK` DIRECT, then `FINAL,PROXY`. Large lists
of ordinary foreign domains are deliberately omitted because `FINAL,PROXY`
already covers them. Keep the GeoLite database current; an old database can
misclassify newly allocated CN/HK address ranges.

## AI Traffic

Mainland China AI services are intentionally direct. This keeps domestic AI
traffic off the VPN node and avoids routing Chinese AI traffic through the
overseas exit.

Mainland China daily-use apps are also intentionally direct, especially payment,
messaging, video, shopping, delivery, and map services. These are domestic
traffic and should not consume the VPN node.

The profile explicitly forces common foreign AI tools through the selected proxy,
including OpenAI/ChatGPT, Claude, Gemini, Perplexity, Copilot, Cursor,
Hugging Face, OpenRouter, Mistral, Midjourney, Runway, and related service
domains. Gemini client support includes Gemini web/app/API traffic and Google
login/API dependencies such as `accounts.google.com`,
`oauth2.googleapis.com`, `googleapis.com`, `gstatic.com`,
`geministatic.com`, and `googleusercontent.com`.

Google ecosystem routing is intentionally broad. The rules include a
`DOMAIN-KEYWORD,google` catch-all plus common Google-owned domains that do not
contain the `google` string, such as `g.co`, `goo.gl`, `youtube.com`,
`youtu.be`, `ytimg.com`, `googlevideo.com`, `gvt1.com`, `ggpht.com`,
`doubleclick.net`, and `2mdn.net`.

Unknown non-China domains are still covered by `FINAL,PROXY`. This cannot prove
coverage of every future AI vendor domain, but the default route is proxy unless
a rule matches `DIRECT` first.

## Foreign Social, Messaging, and Developer Platforms

Foreign productivity and SaaS sites are intentionally forced through the proxy
so their auth, API, static assets, and websocket traffic stay on the same exit.
This includes Notion and common adjacent tools such as Figma, Canva, Dropbox,
Atlassian, Airtable, Miro, Linear, Vercel, Netlify, Docker Hub, npm, and PyPI.

Foreign social media, messaging, collaboration, and developer platforms are
intentionally forced through the proxy to avoid mixed direct/proxy resource
loading and to keep the overseas exit consistent. This includes X/Twitter and
its media/CDN/helper domains, Instagram, Facebook, Threads, WhatsApp, Signal,
Slack, Zoom, Skype, Teams, Telegram/Telegraph, Reddit, Discord, LinkedIn, GitHub,
GitLab, Bitbucket, and Stack Overflow.
X/Twitter coverage includes `x.com`, `twitter.com`, short links via `t.co`,
image/video media via `twimg.com`, and embedded article/widget traffic via
`publish.twitter.com` and `syndication.twitter.com`.

## Apple App Store

Apple domains are intentionally direct. This avoids App Store download failures
seen when all Apple CDN traffic goes through the VPN node.

This means some Apple traffic will not use the VPN while this profile is active.
That is expected for this rule set.

## TradingView

TradingView is intentionally forced through the proxy before CN/GEOIP rules.
This helps avoid partial page failures where the main page loads but resources
for charting, scanners, widgets, or explore-style pages are split across direct
and proxy paths.

## Financial Services, Brokerages, and Crypto Exchanges

Wise, BIT/Bitget, OKX, Binance, BiyaPay, Robinhood, Plasma One Card, and iFAST
GB are intentionally forced through the proxy so account sessions, API calls,
static resources, and websocket traffic use the same personal VPN exit. Hong
Kong brokerages are intentionally direct. BiyaPay also uses `hxdsmy.com` for
public static resources, so that domain is proxied with BiyaPay. Robinhood
support covers `robinhood.com` plus official Robinhood adjacent domains such as
`aboutrobinhood.com` and `sherwood.news`. BIT support covers `bit.com` plus
related Matrixport/Cactus domains, and Bitget support covers `bitget.com` plus
common app/static domains. Hong Kong brokerage app families include Longbridge,
Futu/Futubull, Moomoo, Tiger Brokers, Webull, uSMART, and SoFi HK.
Plasma One Card support covers `plasma.org`, `plasma.to`, app download links,
and the public infrastructure domains listed by Plasma for card/account
services, including Rain, Bridge, Privy, Ramp, Zerohash, Fireblocks, Aave, and
Whop.
iFAST GB support covers `ifastgb.com` plus its static/API subdomains.

Use this only for a stable fixed exit that you are allowed to use. Do not use it
to bypass service eligibility, regional restrictions, sanctions, KYC rules, or
platform risk controls.

## Import Steps

1. Keep the actual VLESS Reality node configured locally in Shadowrocket.
2. Add this file as a remote config/profile URL.
3. Enable rule mode.
4. Refresh the remote config after changes.
5. If App Store still fails, disable/re-enable Shadowrocket once so iOS clears
   the old connection state.

## Security Notes

- This repo is safe to keep public as long as it only contains rules.
- Do not commit VLESS URLs, UUIDs, Reality private keys, ntfy URLs, or generated
  client config files.
- The profile exposes the VPN server IP because it is needed for direct loop
  prevention. That IP is not an authentication secret, but it is metadata.
