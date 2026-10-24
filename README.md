# proxy server for scraping: what it actually costs, where the credit traps hide, and how to pick a setup that won't get blocked

A proxy server for scraping sits between your crawler and the target website. Your request goes to the proxy first, the proxy picks an IP from its pool and forwards the request, and the target site sees the proxy's IP — not your server's. That's the whole trick, and it's why the right proxy setup decides whether your scraper runs for months or dies on the first anti-bot wall.

Most people searching this term are really asking three things: what kind of proxy setup do I need, what will it actually cost me, and which provider won't waste my time. This article covers all three, with current prices verified against provider pricing pages, plus a few cost traps that most comparison posts skip — including a credit multiplier system that can turn a "$49 plan" into 1,333 pages instead of 100,000.

> **Quick answer:** if your scraper is already written and keeps getting blocked, a managed scraping API like ScraperAPI is the fastest fix — one endpoint handles IP rotation, retries, CAPTCHAs and JS rendering. Plans start at $49/month for 100,000 credits (yearly billing drops it to $44.10/month), and you can test it free with 5,000 credits for 7 days without a credit card. 👉 [Check ScraperAPI's current plans and trial](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## What a proxy server for scraping actually does

It hides your real IP and makes your requests look like they come from somewhere else. Every time your code hits the proxy, the proxy forwards the request using a different IP from its pool. The target website can't tell your machine's identity from the response, and if one IP gets rate-limited or banned, the next request goes out on a fresh one.

That's the basic version. In practice, sites like Amazon, Google and anything behind Cloudflare don't just count IPs — they fingerprint browsers, check TLS handshakes, and serve CAPTCHAs. This is where proxy setups split into three very different products, and choosing between them matters more than choosing between brands:

| Model | What you pay for | Entry price (list, Aug 2026) | Best for |
| --- | --- | --- | --- |
| Datacenter proxy pool | per IP / month | 100 IPs = $2.99/mo (Webshare) | Sites that don't aggressively block datacenter IPs |
| Residential proxy | per GB of traffic | ~$2.25–$3.75/GB at modest volume (Webshare, Decodo) | Geo-targeting, sites that block datacenter ranges |
| Managed scraping API | per successful request / 1k | from $0.13–$0.83/1k (Zyte, Firecrawl); ScraperAPI ~$0.49/1k | Proxies + retries + rendering + CAPTCHAs handled for you |

The pricing comparison worth internalizing: managed scraping APIs charge per successful response, raw proxies charge per GB or per IP. Pick the wrong model for your workload and the same task can cost 10–100× more. If your targets are lenient and you run your own crawler, a cheap datacenter pool does the job. If you need browser fingerprints and CAPTCHA solving handled, that's what a managed API is for.

## The ScraperAPI approach: one endpoint instead of a proxy stack

ScraperAPI packages the whole proxy layer into a single HTTP endpoint. You send a URL with your API key, and it routes the request through a rotating pool of 40M+ IPs across 50+ countries, retries with different IPs when one fails, renders JavaScript when you ask, and resolves CAPTCHAs without custom code on your end. You get HTML back, or structured JSON if you use one of their dedicated endpoints.

A basic call looks like this:

python
import requests

params = {
    "api_key": "YOUR_KEY",
    "render": "true",
    "country_code": "us",
    "url": "https://example.com/page",
}
html = requests.get("https://api.scraperapi.com", params=params, timeout=70).text


Two details from their docs that save people real debugging time: set your client timeout to 70 seconds (hard domains can take up to 60 to respond), and put ScraperAPI's own parameters before the `url` parameter so they don't get treated as part of the target address.

Every plan — including the free one — includes JS rendering, premium proxies, CAPTCHA and anti-bot handling, custom headers, session support, automatic retries, unlimited bandwidth and a 99.9% uptime guarantee. The company has been around since 2018, reports serving 10,000+ companies with roughly 36 billion API requests per month, and acquired Traject Data in 2026, adding structured endpoints like Rainforest API and SerpWow to the stack.

## Full plan comparison

Here's the complete plan lineup as shown on the official pricing page. Yearly billing takes a flat 10% off every tier.

| Plan | Credits | Monthly | Yearly (per mo) | Concurrent threads | Geo-targeting | Get it |
| --- | --- | --- | --- | --- | --- | --- |
| Free | 1,000/mo | $0 | — | 5 | US & EU only | [Start free](https://www.scraperapi.com/?fp_ref=coupons) |
| Hobby | 100,000 | $49 | $44.10 | 20 | US & EU only | [Get Hobby](https://www.scraperapi.com/?fp_ref=coupons) |
| Startup | 1,000,000 | $149 | $134.10 | 50 | US & EU only | [Get Startup](https://www.scraperapi.com/?fp_ref=coupons) |
| Business | 3,000,000 | $299 | $269.10 | 100 | Country-level | [Get Business](https://www.scraperapi.com/?fp_ref=coupons) |
| Scaling | 5,000,000 | $475 | $427.50 | 200 | Country-level | [Get Scaling](https://www.scraperapi.com/?fp_ref=coupons) |
| Professional | 10,500,000 | $975 | $877.50 | 300 | Country-level | [Get Professional](https://www.scraperapi.com/?fp_ref=coupons) |
| Advanced | 21,500,000 | $1,975 | $1,777.50 | 500 | Country-level | [Contact sales](https://www.scraperapi.com/?fp_ref=coupons) |
| Enterprise | 22M+ (custom) | Custom | — | 500+ | Custom | [Talk to sales](https://www.scraperapi.com/?fp_ref=coupons) |

New accounts also get a 7-day trial with 5,000 credits, no credit card required, and a 7-day no-questions-asked refund after paying — so there are effectively two free test windows before any money is committed. 👉 [Start the 7-day trial with 5,000 credits](https://www.scraperapi.com/?fp_ref=coupons)

## The credit multiplier trap most reviews gloss over

Here's the part that decides whether ScraperAPI is cheap or expensive for you, and it's buried deeper than the pricing page suggests: **one request does not always cost one credit.**

The cost of each request is determined by two things — the domain you're hitting and the parameters you enable:

- Standard page: 1 credit
- Amazon, eBay, Walmart: 5 credits
- Google and Bing (including subdomains): 25 credits
- LinkedIn: 30 credits
- Sites behind Cloudflare, DataDome or PerimeterX: +10 credits when bypassed
- `render=true` (JS rendering): +10 credits
- `premium=true`: +10 credits
- `screenshot=true`: +10 credits
- `ultra_premium=true`: +30 credits (paid plans only)

And the combinations stack worse than they look on paper. Premium plus rendering costs +25 in practice, not +20. Ultra premium plus rendering costs +75, not +40. One independent review put it bluntly: "The combination penalty is the trap."

What does that mean in real page counts? Take the Hobby plan's 100,000 credits:

| Workload | Actual pages you get |
| --- | --- |
| Plain HTML (news sites, blogs, no JS) | 100,000 |
| Amazon product pages | 20,000 |
| JS rendering only | 10,000 |
| Google SERP | 4,000 |
| Ultra-premium + JS (hard protected pages) | 1,333 |

That last row is the number to remember. "100,000 credits" on a $49 plan becomes 1,333 scrapes if every target sits behind Cloudflare with full rendering. For plain HTML, all 100,000 are genuinely yours.

The fix costs nothing: before buying, run your actual target URLs through the Domain Cost Estimator in the dashboard — it shows the exact credit cost for any URL — and set a `max_cost` cap per scrape so no single request blows past your budget.

## Running costs people don't budget for

Three behaviors of the billing system catch people off guard, and none of them are prominently displayed on the pricing page.

**Credits don't roll over.** They expire monthly. If your workload is spiky — one big crawl per quarter — you're paying for idle months, and a raw proxy billed per GB (IPRoyal, for instance, sells credits that never expire) may fit better.

**The Startup-to-Business cliff.** Overage handling only exists from the Scaling plan up. If your monthly need lands at 1.1M credits, you miss Startup's 1M cap by 10% — and since Hobby through Professional have no pay-as-you-go, your only option is jumping from $149 to $299. If you're anywhere near a plan boundary, trim rendering to only the pages that need it and set `max_cost`, because the difference between fitting in a plan and missing it by 10% is a doubled bill.

**Concurrency is enforced, not queued.** Exceed your plan's thread limit and you get a 429 response — the request is rejected outright, not held in a queue, though you're not charged for it. If your scraper fires 60 parallel requests on a 50-thread plan, some will simply bounce until you throttle.

For credits running out mid-month, you have options depending on tier: auto-upgrade (opt-in, available on Hobby through Professional) bumps you to the next plan automatically when credits run dry; pay-as-you-go (Scaling and up) keeps scraping at a fixed per-credit rate up to a monthly cap you set; and annual plans can renew early for a fresh batch of credits.

## How the pricing stacks up against raw proxies and other APIs

For the standard-HTML scenario, ScraperAPI's effective rate works out to about $0.49 per 1,000 pages on Hobby (based on list prices as of August 2026). Comparable managed APIs: Zyte starts at $0.13/1k for simple sites (rising to $16.08/1k for browser-rendered hard ones), Firecrawl's hosted tier runs $0.83/1k, and Bright Data's Web Unlocker is $1.50/1k with 5,000 free requests monthly.

Against raw residential proxies, the comparison shifts because you're buying different things. Bright Data's PAYG list price is $8.00/GB (about $4.00 with its 50% promotion), Oxylabs starts at 5GB/$30/month ($6.00/GB), IPRoyal at $7.35/GB, and Decodo (formerly Smartproxy) at $3.75/GB from a $11.25/3GB entry plan. Since 1GB moves roughly 1,000 average HTML pages, a $3.75/GB pool costs about $3.75 per 1,000 pages — but you're handling rotation, retries, CAPTCHAs and rendering yourself. Decodo also only offers a 3-day money-back guarantee, versus ScraperAPI's 7 days; Oxylabs requires contacting sales even for a trial.

The honest trade-off: raw proxies are cheaper per page at volume *if* your targets don't put up much of a fight and you have the engineering time. A managed API costs more per hard page but ships in an afternoon. One more ScraperAPI-specific limitation worth stating plainly: you control country, session and IP tier, but not specific IPs, ASNs or cities — if you need that granularity, raw proxies are the right tool.

## Where it performs well — and where it doesn't

Independent testing of the free trial gives a concrete picture of which targets work. Zillow hit a 100% success rate, Etsy 99% (~4.8s responses), Amazon 98% (~6.5s), LinkedIn 95%, Walmart 93% with a structured JSON endpoint available. Realtor.com, on the other hand, managed roughly 12% — essentially unreliable for that domain.

The Amazon structured endpoint in particular returns 18+ fields (price, reviews, BSR, variants, images, seller info) across 21 regional marketplaces, which is production-grade for e-commerce monitoring. Dedicated Google endpoints cover organic results, Shopping, Maps, News and Jobs.

Knowing the weak spots matters as much as the wins:

- **Country-level geo-targeting outside US & EU requires Business ($299) or higher.** Hobby and Startup are locked to US & EU regions only, and their analytics dashboard only reaches back 30 days.
- **Credits expire monthly** — bad fit for quarterly crawl cycles.
- **Hard domains get slow.** Protected pages can take up to 60 seconds; use the Async API for volume workloads.
- **If most of your targets are Google or LinkedIn**, those 25–30 credit hits will eat a plan fast. 4,000 Google SERP scrapes a month on Hobby may be exactly enough — or nowhere near enough, depending on your keyword list.

## Who should skip a managed API entirely

If your target sites are robots-friendly and you just need volume, a datacenter pool is dramatically cheaper — 100 IPs for $2.99/month covers a lot of gentle scraping. If you need residential IPs but have your own retry and rendering logic, Decodo or Webshare residential plans at $2.25–$3.75/GB will undercut any API on plain pages. And if you want ready-made scrapers with hosted runs and zero code, Apify's platform model fits better than an endpoint you wire into your own scripts.

ScraperAPI earns its keep when your existing scraper (Requests, Scrapy, Playwright) keeps getting blocked and you want proxies, retries, rendering and CAPTCHA handling behind one URL — especially for Amazon and e-commerce monitoring, where its structured endpoints and 5-credit multiplier are genuinely competitive.

## FAQ

**Does a proxy server for scraping guarantee I won't get blocked?**
No provider can promise that. What a good one does is rotate IPs, retry failures, and handle CAPTCHAs so blocks cost you credits instead of downtime. Real-world success rates vary wildly by domain — from 100% on Zillow to ~12% on Realtor.com in independent tests — which is exactly why testing your specific targets on the free trial matters more than any benchmark table.

**Do failed requests cost credits?**
No. ScraperAPI bills only successful responses, retrying with different IPs on its own. Failed attempts don't burn your allowance.

**What happens when I hit my concurrency limit?**
You get a 429 response and the request is rejected rather than queued. Lower your parallel request count to your plan's thread limit and retry — no charge for the rejected calls.

**Can I get a refund if it doesn't work for my sites?**
Yes. Paid plans come with a 7-day no-questions-asked refund, on top of the 7-day/5,000-credit trial that requires no credit card. Run your real target domains through both windows before committing to a yearly plan.

**Is there a free version?**
Yes — 1,000 credits per month with 5 concurrent connections, permanently. Enough to verify success rates on a handful of URLs, not enough to run anything sustained.

## Picking a setup before you pay

Match the tool to your targets, not to the marketing:

- Lenient HTML sites, you run your own crawler → a cheap datacenter pool (from $2.99/month) is all you need.
- Residential-level targets, you have retry/rendering code → a per-GB residential pool starting around $3.75/GB.
- Blocked repeatedly, want it fixed this afternoon → ScraperAPI Hobby at $49/month, billed yearly at $44.10/month if you're keeping it.
- Mostly Google/LinkedIn targets → ScraperAPI works but count the multipliers first; Business ($299) is also the floor for non-US/EU country targeting.
- Need specific IPs or ASN control → raw proxies, by design.

One last check before any plan: paste your three most-scraped URLs into the dashboard's Domain Cost Estimator and multiply out your real monthly credit burn. If the number lands within 10% of a plan's limit, either drop rendering on pages that don't need it or size up — the gap between Startup and Business is $150 a month, and it only takes a 10% credit overrun to force it. 👉 [Open ScraperAPI's plan picker and run the numbers](https://www.scraperapi.com/pricing/?fp_ref=coupons)
