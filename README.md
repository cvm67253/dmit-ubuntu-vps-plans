# ubuntu virtual server hosting: How to Pick a Plan, Install Ubuntu, and Avoid the Common VPS Traps

If you've been hunting for "ubuntu virtual server hosting," you're probably past the "what is a VPS" stage and into the "which one actually works" stage. Maybe you need a box for a side project, a relay for traffic that has to reach users in Asia, a small app backend, or just root on a Linux machine you control end to end. Ubuntu is the default pick for most of these jobs because the package ecosystem is huge, the documentation is everywhere, and the LTS releases don't surprise you.

What's harder is picking the host. Most VPS pages read the same — "blazing fast NVMe, 99.9% uptime, global network" — and then you spin one up and discover the routing bounces through three continents or the node is so oversold that your SSH session lags. This guide walks through what actually matters when choosing Ubuntu virtual server hosting, how to set it up once you have it, and where DMIT.io fits in if your traffic has any reason to touch the Asia-Pacific region.

## What "Ubuntu Virtual Server Hosting" Actually Means

A virtual server is a slice of a physical machine, virtualized (usually KVM or similar), that behaves like a dedicated server from your side — you get root, you pick the OS, you install what you want. Ubuntu is one of the most common OS choices because Canonical publishes Long Term Support releases every two years (24.04 LTS is the current one, supported until 2029), and most tutorials, control panels, and deployment scripts assume Ubuntu as the baseline.

When you buy "Ubuntu VPS hosting," what you're really buying is:

- A virtual machine with a guaranteed amount of CPU, RAM, and disk
- A network port at some speed (1Gbps, 2Gbps, 4Gbps, 10Gbps)
- A monthly transfer allowance (sometimes called "traffic" or "bandwidth")
- One or more IP addresses (IPv4 and usually IPv6)
- A control panel to reinstall, snapshot, reboot, and manage billing
- Root access over SSH

The Ubuntu part is just the template the provider installs. Almost every serious provider offers Ubuntu as a one-click install; the difference between providers is the hardware under it, the network in front of it, and the policies around it (refund windows, IP replacement, overage handling).

## The Three Things That Actually Differentiate Providers

Once you've confirmed a provider runs real hardware and isn't reselling decade-old Xeons, the differences come down to three things.

**Network routing.** This is the one most people underestimate. Two providers with identical specs can give you wildly different latency to the same user, because the path your packets take matters more than the speed of your port. If your users are in mainland China, generic international transit often means 250ms+ latency and packet loss during evening peak hours. Premium routing (CN2 GIA, CMIN2, direct peering with the three Chinese carriers) cuts that to 140–180ms from the US West Coast and stays flat through peak. If your users are all in North America or Western Europe, this matters less — but if any of your traffic touches Asia, it's the single biggest variable.

**Hardware generation.** AMD EPYC 7003 (Zen 3), 9004 (Zen 4), and 9005 (Zen 5) are all current-ish server platforms; older Intel Xeon E5 chips are not. NVMe SSDs vs SATA SSDs is a real difference for anything disk-heavy (databases, build servers, log-heavy apps). Disk I/O above 800MB/s is what you want to see; below 200MB/s is a sign of an oversold node.

**Policies.** Refund window (3 days vs 30 days), IP replacement policy (free every 15 days vs $15 each time), overage behavior (throttled vs cut off vs charged per GB), and whether the provider oversells. These don't show up in spec tables but they determine whether you'll actually be happy a month in.

## Why DMIT.io Comes Up When People Talk About Asia-Pacific VPS

DMIT is a hosting company founded in 2018 that operates its own infrastructure in three locations: Los Angeles, Hong Kong, and Tokyo. The thing that makes them different from Vultr, DigitalOcean, Linode, or the dozens of budget VPS brands is that they've built their whole product around routing quality to mainland China and the broader Asia-Pacific region, rather than treating it as an afterthought.

Their network is split into three tiers, and the tier matters as much as the plan size:

- **Premium (Pro)** — Uses China Telecom CN2 GIA (AS4809) plus China Unicom (AS9929) and China Mobile International (AS58453). This is the top tier for anything where latency and packet loss to mainland China actually matter. Expect 140–180ms from LA to China, sub-30ms from Hong Kong to Shenzhen, 60–90ms from Tokyo to Shanghai.
- **Eyeball (EB)** — Uses CMIN2/CMI and other Chinese eyeball ISPs with reasonable-effort routing. Better than generic transit for China-facing traffic, cheaper than Premium. Good for mixed China/global audiences.
- **Tier 1 (T1)** — Standard international transit, no China optimization. Cheapest tier, fine for workloads where Asia is one of many regions or where China isn't in the picture at all.

All three tiers run on AMD EPYC hardware (AN5/Zen 5 is the flagship, AN4/Zen 4 is the workhorse, AS3/Zen 3 is the value tier) with NVMe storage and KVM virtualization. Ubuntu is a one-click install on all of them.

This structure is honestly one of the cleaner ways a hosting provider has communicated what you're actually buying. Instead of vague "optimized routing" marketing, you pick a tier that matches your traffic pattern and a plan size that matches your workload.

## Ubuntu on DMIT: What the Setup Actually Looks Like

DMIT supports Ubuntu as a one-click install alongside Debian, CentOS, AlmaLinux, Rocky Linux, Fedora, openSUSE, Arch, Alpine, and others. You can also mount a custom ISO if you need something unusual. Once you order a plan, the instance is provisioned automatically and you get root access over SSH.

The standard first-hour setup on a fresh Ubuntu VPS looks like this:

1. **SSH in** with the key or password provided. DMIT defaults to SSH key authentication, which is the right call — disable password auth once you're in.
2. **Update the system:** `sudo apt update && sudo apt upgrade -y`
3. **Create a non-root user** with sudo privileges, then disable root login over SSH.
4. **Set up a firewall** — `ufw` is the easy default on Ubuntu. Allow 22 (or your custom SSH port), plus whatever your app needs (80, 443, etc.).
5. **Add your SSH public key** to the new user's `~/.ssh/authorized_keys`.
6. **Set up automated backups or snapshots** — DMIT includes snapshot capability, and automated backups are available as an add-on starting at $0.45/GB/month.
7. **Install what you actually came here for** — Nginx, Docker, PostgreSQL, Node, whatever your stack is.

If you're running a website, this is also the point where you'd add a Let's Encrypt cert (Certbot works the same as on any Ubuntu box), set up a reverse proxy, and point your DNS at the DMIT-assigned IP.

None of this is DMIT-specific — it's the same workflow on any Ubuntu VPS. The DMIT-specific parts are the network tier you picked (which determines how fast your users reach you) and the plan size (which determines how many users you can handle before the box breaks a sweat).

## DMIT Plan Comparison: All Current Plans Across Locations and Tiers

DMIT's plan lineup is large because every location (LAX, HKG, TYO) is offered in three network tiers (Premium, Eyeball, Tier 1), and each tier has multiple sizes. Below is the full set of plans currently shown on the official pricing and cloud-instance pages. Prices are monthly unless marked as annual.

### Los Angeles — Premium Network (CN2 GIA)

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| LAX.Pro.STARTER | 2 | 2GB DDR4 | 80GB | 3000GB BIDI | 10Gbps | $29.90/mo | [Get LAX.Pro.STARTER](https://bit.ly/DmiT) |
| LAX.Pro.MINI | 4 | 4GB DDR4 | 80GB | 5000GB BIDI | 10Gbps | $58.88/mo | [Get LAX.Pro.MINI](https://bit.ly/DmiT) |
| LAX.Pro.MICRO | 4 | 4GB DDR4 | 160GB | 7000GB BIDI | 10Gbps | $74.99/mo | [Get LAX.Pro.MICRO](https://bit.ly/DmiT) |

### Los Angeles — Eyeball Network (CMIN2)

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| LAX.EB.STARTER | 2 | 2GB DDR4 | 80GB | 5000GB BIDI | 10Gbps | $29.90/mo | [Get LAX.EB.STARTER](https://bit.ly/DmiT) |
| LAX.EB.MINI | 4 | 4GB DDR4 | 80GB | 10000GB BIDI | 10Gbps | $58.88/mo | [Get LAX.EB.MINI](https://bit.ly/DmiT) |
| LAX.EB.MICRO | 4 | 4GB DDR4 | 160GB | 14000GB BIDI | 10Gbps | $74.99/mo | [Get LAX.EB.MICRO](https://bit.ly/DmiT) |

### Los Angeles — Tier 1 Network (Standard International)

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| LAX.T1.STARTER | 1 | 2GB DDR4 | 40GB | 4000GB (IN, OUT) | Performance-based | $12.90/mo | [Get LAX.T1.STARTER](https://bit.ly/DmiT) |
| LAX.T1.MINI | 2 | 2GB DDR4 | 60GB | 8000GB (IN, OUT) | Performance-based | $21.90/mo | [Get LAX.T1.MINI](https://bit.ly/DmiT) |
| LAX.T1.MICRO | 4 | 4GB DDR4 | 80GB | 16000GB (IN, OUT) | Performance-based | $32.90/mo | [Get LAX.T1.MICRO](https://bit.ly/DmiT) |

### Hong Kong — Premium Network (CN2 GIA)

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HKG.Pro.STARTER | 1 | 2GB DDR4 | 40GB | 800GB BIDI | 1Gbps | $79.90/mo | [Get HKG.Pro.STARTER](https://bit.ly/DmiT) |
| HKG.Pro.MINI | 2 | 2GB DDR4 | 60GB | 1200GB BIDI | 1Gbps | $119.90/mo | [Get HKG.Pro.MINI](https://bit.ly/DmiT) |
| HKG.Pro.MICRO | 4 | 4GB DDR4 | 80GB | 1600GB BIDI | 1Gbps | $159.90/mo | [Get HKG.Pro.MICRO](https://bit.ly/DmiT) |

### Hong Kong — Eyeball Network (CMI)

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HKG.EB.STARTERv2 | 1 | 2GB DDR4 | 40GB | 2000GB BIDI | 2Gbps (no guarantee) | $59.90/mo | [Get HKG.EB.STARTERv2](https://bit.ly/DmiT) |
| HKG.EB.MINIv2 | 2 | 2GB DDR4 | 60GB | 3000GB BIDI | 2Gbps (no guarantee) | $89.90/mo | [Get HKG.EB.MINIv2](https://bit.ly/DmiT) |
| HKG.EB.MICROv2 | 4 | 4GB DDR4 | 80GB | 4000GB BIDI | 4Gbps (no guarantee) | $129.90/mo | [Get HKG.EB.MICROv2](https://bit.ly/DmiT) |

### Hong Kong — Tier 1 Network (Standard International)

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HKG.T1.STARTER | 1 | 2GB DDR4 | 40GB | 4000GB (IN, OUT) | Performance-based | $12.90/mo | [Get HKG.T1.STARTER](https://bit.ly/DmiT) |
| HKG.T1.MINI | 2 | 2GB DDR4 | 60GB | 8000GB (IN, OUT) | Performance-based | $21.90/mo | [Get HKG.T1.MINI](https://bit.ly/DmiT) |
| HKG.T1.MICRO | 4 | 4GB DDR4 | 80GB | 16000GB (IN, OUT) | Performance-based | $32.90/mo | [Get HKG.T1.MICRO](https://bit.ly/DmiT) |

### Tokyo — Premium Network (CN2 GIA)

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TYO.Pro.STARTER | 1 | 2GB DDR4 | 40GB | 500GB BIDI | 1Gbps | $39.90/mo | [Get TYO.Pro.STARTER](https://bit.ly/DmiT) |
| TYO.Pro.MINI | 2 | 2GB DDR4 | 60GB | 1000GB BIDI | 1Gbps | $79.90/mo | [Get TYO.Pro.MINI](https://bit.ly/DmiT) |
| TYO.Pro.MICRO | 4 | 4GB DDR4 | 80GB | 2000GB BIDI | 1Gbps | $159.90/mo | [Get TYO.Pro.MICRO](https://bit.ly/DmiT) |

### Tokyo — Eyeball Network (CMI)

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TYO.EB.STARTER | 1 | 2GB DDR4 | 40GB | 2000GB BIDI | 2Gbps (no guarantee) | $55.90/mo | [Get TYO.EB.STARTER](https://bit.ly/DmiT) |
| TYO.EB.MINI | 2 | 2GB DDR4 | 60GB | 3000GB BIDI | 2Gbps (no guarantee) | $85.90/mo | [Get TYO.EB.MINI](https://bit.ly/DmiT) |
| TYO.EB.MICRO | 4 | 4GB DDR4 | 80GB | 4000GB BIDI | 4Gbps (no guarantee) | $119.90/mo | [Get TYO.EB.MICRO](https://bit.ly/DmiT) |

### Tokyo — Tier 1 Network (Standard International)

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TYO.T1.STARTER | 1 | 2GB DDR4 | 40GB | 4000GB (IN, OUT) | Performance-based | $12.90/mo | [Get TYO.T1.STARTER](https://bit.ly/DmiT) |
| TYO.T1.MINI | 2 | 2GB DDR4 | 60GB | 8000GB (IN, OUT) | Performance-based | $21.90/mo | [Get TYO.T1.MINI](https://bit.ly/DmiT) |
| TYO.T1.MICRO | 4 | 4GB DDR4 | 80GB | 16000GB (IN, OUT) | Performance-based | $32.90/mo | [Get TYO.T1.MICRO](https://bit.ly/DmiT) |

### Los Angeles — Premium Annual Limited-Stock Plans

These are the limited-stock annual plans that show up periodically on the LAX Pro line. They sell out and restock unpredictably; if you see one available and it fits your use case, it's reasonable to move on it.

| Plan | vCPU | RAM | SSD | Traffic | Port | Price | Order |
| --- | --- | --- | --- | --- | --- | --- | --- |
| LAX.Pro.WEE | 1 | 1GB | 20GB | 500GB | 500Mbps | $36.9/yr | [Get LAX.Pro.WEE](https://bit.ly/DmiT) |
| LAX.Pro.MALIBU | 1 | 1GB | 20GB | 1TB | 1Gbps | $49.9/yr | [Get LAX.Pro.MALIBU](https://bit.ly/DmiT) |
| LAX.Pro.PalmSpring | 2 | 2GB | 40GB | 2TB | 2Gbps | $100/yr | [Get LAX.Pro.PalmSpring](https://bit.ly/DmiT) |

All plans above include 1 IPv4 and 1 IPv6 (/64 on Premium/Eyeball, /128 on Tier 1), basic DDoS protection, and free instant setup. Tier 1 IPs are not guaranteed to be reachable in all countries (notably China, Russia, and countries with national network censorship) — for guaranteed first connection in sensitive regions, the IP Guarantee+ add-on is required.

## Current Promo Codes (Verified Across Multiple Sources)

DMIT runs plan-specific promotions that change over time. The codes below are confirmed as active across multiple coupon-tracking sites and DMIT's own promotional pages as of late 2026. Promo codes are case-sensitive and apply at checkout.

| Code | Discount | Applies To |
| --- | --- | --- |
| `HKG-T1-ANNUALLY-45OFF-RECUR` | 45% off for life + doubled disk, 50%+ more RAM, higher I/O | HKG Tier 1, annual billing, STARTERv2 or higher |
| `LAX-T1-ANNUALLY-RECUR-30-OFF` | 30% off recurring | LAX Tier 1, annual billing, TINY and above |
| `2025-TYO-T1-HI-GSL-NON-MONTHLY-30OFF` | 30% off recurring | Tokyo Tier 1, quarterly or annual billing |
| `2025-TYO-T1-HI-GSL-MONTHLY-10OFF` | 10% off recurring | Tokyo Tier 1, monthly billing |
| `LAX-EB-LAUNCH-NON-MONTHLY-RECURRING-20OFF` | 20% off recurring | LAX Eyeball, quarterly or annual billing |
| `7L8O3PQTHNXCFS2TXPLP` | Additional 5% off (some packages get higher discounts on non-monthly billing) | Select packages |

The HKG Tier 1 annual code is the standout — 45% off for life plus upgraded specs is a substantial deal if your workload fits Tier 1 routing (i.e., you don't need China-optimized paths). The LAX T1 annual 30% off is similarly good value for non-China workloads that just want a solid LA box.

> Promo codes are time-sensitive and plan-specific. Always verify the code applies to your selected plan at checkout before completing the order. DMIT's terms note that discount codes are intended for new customers; misusing existing-customer codes can result in service suspension.

👉 [Browse current DMIT plans and apply promo codes](https://bit.ly/DmiT)

## How to Pick the Right Plan Without Overpaying

The mistake most people make is buying the plan with the biggest specs they can afford. Specs matter, but the tier and location matter more for most use cases. Here's how to think about it.

**If your users are in mainland China, Hong Kong, or Taiwan:** Pick Premium (Pro) tier. The routing difference is the whole point. For LA-based China traffic, the LAX.Pro.WEE at $36.9/year is the cheapest way to get full CN2 GIA routing — the specs are modest (1 vCPU, 1GB RAM, 500GB traffic) but the routing is identical to the $620/month GIANT plan. For Hong Kong, HKG.Pro.STARTER at $79.90/month gives you sub-30ms latency to Shenzhen and most of southern China. For Tokyo, TYO.Pro.STARTER at $39.90/month is the entry point.

**If your users are mixed China and global:** Eyeball (EB) tier is the value pick. You give up the CN2 GIA guarantee but keep reasonable-effort CMIN2 routing, which is still meaningfully better than generic transit for Chinese residential users. LAX.EB.STARTER at $29.90/month gives you 5TB of traffic (vs 3TB on the same-priced Pro plan) — the EB tier trades routing premium for more bandwidth.

**If your users are nowhere near China:** Tier 1 (T1) is the right call. You're not paying for routing you won't use. LAX.T1.STARTER at $12.90/month or HKG.T1.STARTER at $12.90/month gets you 1 vCPU, 2GB RAM, 40GB SSD, and 4TB of transfer on solid AMD EPYC hardware. With the annual promo codes, the effective price drops further.

**If you just want to test the network:** Start with a Tier 1 plan, validate that latency and routing behave the way you need, then upgrade to EB or Pro if the test confirms your use case needs it. DMIT's three-tier structure makes this progression natural — you're not locked into a tier.

**On plan size:** 1GB RAM is fine for proxies, simple sites, bots, and monitoring. 2GB RAM is the minimum for running Nginx + PHP + MySQL comfortably without swapping. 4GB RAM is where you can run a real app stack with a database and have headroom. The jump from 2GB to 4GB RAM (STARTER to MINI on most tiers) is the most cost-effective upgrade in the lineup.

## What DMIT Does and Doesn't Include

A few things worth knowing before you commit:

- **Unmanaged service.** DMIT provides the infrastructure; you manage the server. Support tickets have a 72-hour reply SLA, and the support team is geared toward infrastructure issues, not "how do I install WordPress" questions. If you need a managed control panel and hand-holding, this isn't the right provider.
- **No control panel included.** You get root over SSH. You can install cPanel, Plesk, CyberPanel, or whatever you want yourself.
- **Throttled overage, not cutoff.** When you exceed your monthly traffic allowance, the connection drops to a throttled speed (2–10Mbps depending on plan tier) rather than cutting off or charging overage fees. For light tasks this is workable; for production traffic it's a sign you should upgrade.
- **3-day refund window.** Full refund if you've used less than 30GB transfer and it's been under 3 days. Partial refund up to 30 days. After that, no refunds. Test what you need to test promptly.
- **IP replacement policy.** On Premium and Eyeball plans, free IP replacement every 15 days (or every 7 days with the IP Care+ add-on). On Tier 1, IP replacement is $5 per change every 7 days. If your IP gets blocked by the Great Firewall, this matters.
- **99% SLA.** If uptime drops below 99% in a month, you get half a month credited. Below 95%, a full month. Below 90%, two months. This is a real SLA, not a marketing number.
- **Payment methods.** PayPal, Alipay, credit/debit cards, and cryptocurrency on select plans. The Alipay option is a friction-free path if you're paying from China.
- **No Windows VPS.** DMIT is Linux-focused. If you need Windows, look elsewhere.

## Common Ubuntu VPS Setup Mistakes to Avoid

Once you have the box, the setup is the same as any Ubuntu VPS, but a few mistakes come up repeatedly:

- **Leaving root login and password auth on.** Disable both after you've added your SSH key. This is the single biggest security win and takes two minutes.
- **Not setting up a firewall.** `ufw allow 22 && ufw allow 80 && ufw allow 443 && ufw enable` covers most web server use cases. Don't skip this.
- **No automated updates.** Install `unattended-upgrades` and at least get security updates automatically. Manual patching is how you end up running a 6-month-old kernel with known CVEs.
- **No backups.** DMIT includes snapshot capability — use it. Automated backups are an add-on at $0.45/GB/month. The 3-day refund window doesn't cover data loss; you're responsible for your own backups per the terms.
- **Picking the wrong tier.** If you buy Premium and all your users are in the US, you've paid for routing you don't need. If you buy Tier 1 and your users are in China, you'll wonder why latency is 250ms. Match the tier to your traffic.

## Who DMIT Is a Good Fit For (and Who It Isn't)

DMIT is a strong fit if:

- Your users are in mainland China, Hong Kong, Taiwan, or the broader Asia-Pacific region and latency actually matters
- You've been burned by poor China routing from a cheaper provider and need something that reliably works
- You're a developer or technical user who wants to self-host with real performance guarantees
- You're running a game server, live streaming relay, or real-time application where 50ms vs 200ms is the difference between usable and broken
- You want a Hong Kong or Tokyo box with proper carrier peering, not just "located in Asia"

DMIT is probably overkill if:

- All your users are in North America or Western Europe with no Asia traffic — a generic provider like Vultr, DigitalOcean, or Hetzner will do the same job for less
- You're hosting a low-traffic personal blog and the premium routing is wasted on you
- You need Windows VPS
- You need managed hosting with a control panel and human support for app-level issues
- You want a 30-day money-back guarantee — DMIT's 3-day window is short

The honest framing: you're paying for routing quality. If that routing solves a real problem for you, the value is obvious. If it doesn't, you're paying a premium for something you won't use, and there are cheaper options that will serve you just as well.

## Getting Started

If you've read this far and DMIT fits your use case, the entry point is low enough to test without a major commitment. The LAX.Pro.WEE at $36.9/year gives you full CN2 GIA routing for less than the cost of two months on a generic mid-tier VPS. The Tier 1 plans at $12.90/month are the cheapest way to get a real box on DMIT's hardware if you don't need China optimization.

Pick the tier that matches your traffic, pick the plan size that matches your workload, apply a promo code if one fits, and you'll have an Ubuntu box up in a few minutes. From there, the setup is the same Ubuntu VPS workflow you'd run anywhere — the difference is what happens between your server and your users.

👉 [Browse all DMIT plans and current promotions](https://bit.ly/DmiT)

## FAQ

**Can I install Ubuntu on DMIT VPS?** Yes. Ubuntu is a one-click install on all DMIT plans, alongside Debian, CentOS, AlmaLinux, Rocky Linux, Fedora, openSUSE, Arch, and Alpine. You can also mount a custom ISO for unusual OS choices.

**Does DMIT include a control panel?** No. You get root access over SSH. You can install cPanel, Plesk, CyberPanel, or any panel yourself.

**What happens if I exceed my bandwidth limit?** DMIT throttles excess traffic to 2–10Mbps depending on plan tier, rather than cutting off your connection or charging overage fees. The connection stays up at reduced speed.

**Can I upgrade my plan later?** Yes, plan upgrades are available through the client portal. Downgrades may require reinitiating service.

**Is there a refund policy?** Full refund within 3 days if you've used less than 30GB transfer. Partial refund up to 30 days, calculated based on remaining transfer or remaining service time (whichever is lower). After 30 days, no refunds. Refunds are non-refundable in cases of DDoS targeting, IP geographic issues, or abuse.

**Does DMIT accept Alipay?** Yes. PayPal, Alipay, credit/debit cards, and cryptocurrency on select plans.

**What's the SLA?** 99% uptime guaranteed. Below 99% in a month: half-month credit. Below 95%: full month credit. Below 90%: two months credit.

**Are Tier 1 IPs reachable in China?** Not guaranteed. Tier 1 IPs may be unreachable in China, Russia, and countries with national network censorship. For guaranteed first connection in sensitive regions, the IP Guarantee+ add-on is required. Premium and Eyeball plans guarantee first connection in all countries (subject to force majeure).
