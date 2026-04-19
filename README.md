# Geder — Blocklists

**Geder** (גדר, Hebrew for "fence") — a protective boundary around your family's internet.

DNS blocklists served to Geder hardware filters (GL.iNet Brume 2) in customer homes. Each `.txt` file is a category that customers toggle on/off in their AdGuard Home UI.

## Files

| File | Category (customer-facing name) | What it blocks |
|------|-------------------------------|----------------|
| `social.txt` | Social Media | Facebook, Instagram, TikTok, Twitter/X, Reddit, Pinterest, Snapchat, Discord, Tumblr, Bluesky, Mastodon, plus WeChat/Weibo and other international social, plus porn-circumvention search engines (Bing, Yandex). Excludes LinkedIn. |
| `news.txt` | News | Major US national news outlets across the political spectrum, major regional papers, international outlets with heavy US readership, political/opinion outlets, tabloids. |
| `crazy.txt` | Shock & Gore Content | Anonymous imageboards (4chan, 8kun), harassment forums (Kiwi Farms), gore aggregators, violent/disturbing content sites, meme aggregators with crude humor, fringe conspiracy outlets. |
| `dangers.txt` | Kid Dangers (recommended on) | Platforms with documented predator/grooming risk: Roblox, VRChat, random-video-chat clones (Omegle successors), anonymous messaging apps, dating apps, teen-targeted platforms with weak moderation. |
| `gambling.txt` | Gambling | Sports betting (DraftKings, FanDuel, BetMGM), online casinos, poker, crypto gambling, daily fantasy with real money, state lottery apps, horse racing, prediction markets, skin gambling, social casino / sweepstakes gambling. |
| `games.txt` | Games & Gaming Platforms | Free browser game hubs (Miniclip, Y8, Poki, Coolmath Games), IO games, major gaming store platforms (Steam, Epic, Battle.net), console platforms, major specific games, game community/wiki sites. |

## How customers use these

Each file is served as a static URL via GitHub Pages. Each customer's Brume 2 subscribes to all four category URLs in AdGuard Home, with toggles preset at install time based on what the customer selected on their order form. Customer can later toggle any category on/off themselves from the AdGuard Home dashboard.

When we update a file here and push to `main`, every customer's Brume pulls the update within 24 hours automatically. No customer action required.

## Base layer — OpenDNS Family Shield (always on)

Before any of these lists even run, every Geder device uses **OpenDNS Family Shield** (`208.67.222.123` / `208.67.220.123`) as its upstream DNS. Family Shield provides a baseline filter that cannot be disabled by the customer:

- Pornography (comprehensive)
- Phishing sites
- Malware / known-malicious hosts
- Proxy / anonymizer / VPN-bypass services

The category lists in this repo intentionally do NOT duplicate Family Shield coverage. They handle everything Family Shield doesn't: social media, news, shock content, and kid-specific danger platforms.

## Kids Mode (optional $50 add-on)

Customers who want stricter control for a specific child can add **Geder Kids Mode**. This configures a kid's device (by MAC address or by joining a separate "Kids-WiFi" SSID on a dedicated VLAN) to use an **allowlist-only** profile — every domain blocked except a curated list of approved sites (Khan Academy, Duolingo, specific educational platforms, etc.).

See `kids-mode/` directory (TBD) for the approved-sites reference list and setup instructions.

## Format

AdGuard / AdBlock filter syntax:

- `||domain.com^` blocks `domain.com` and all subdomains automatically
- `! comment` for notes and section headers (AdGuard ignores lines starting with `!`)
- `@@||domain.com^` in custom per-household rules to whitelist an exception

## Change process

1. Edit the relevant `.txt` file
2. Commit with a clear message (e.g., `social: add new TikTok CDN domains`)
3. Push to `main`
4. GitHub Pages publishes within ~60 seconds
5. Customer Brumes pull the updated list on their next refresh (up to 24h)

**Always test on an internal Brume before pushing changes that affect customers.**

## Emergency rollback

```
git revert HEAD
git push
```

Pushes a new commit that undoes the last change. Customers' Brumes will pull the reverted version on their next refresh cycle.

## Per-household exceptions

If a specific customer needs a site unblocked that's on a global list (e.g., the Smith family's teen needs Discord for homework collaboration), do NOT edit the global list. Instead, remote into that customer's Brume via GoodCloud and add a whitelist exception to their local AdGuard Home:

```
@@||discord.com^
@@||discord.gg^
```

This keeps the global list clean while accommodating the individual household.
