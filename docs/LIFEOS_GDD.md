# LIFEOS — Game Design Document

**Version 1.0 · June 2026 · Design: Husnain + Claude (architect)**
**Format: single source of truth for Claude Code. Build nothing that contradicts this document. When ambiguous, follow the design pillars in §1.2.**

-----

## TABLE OF CONTENTS

1. Vision & Pillars
1. Core Loops
1. Time & Simulation Model
1. The OS Shell (window manager, dock, menu bar)
1. App Catalog (every app, fully specced)
1. Economy Design (sources, sinks, formulas, tuning tables)
1. Markets Engine (stocks, crypto — exact math)
1. Mining & Hardware
1. Real Estate
1. Grey Market & Exposure System
1. Life Layer (energy, stress, lifestyle, skills)
1. Events, News & Email Engine
1. Progression, Achievements & Endless Retention
1. UI Design System (tokens, wireframes, motion, sound)
1. Technical Architecture (stack, state, loop, save, perf, tests)
1. Content Tables (all data, ready to paste)
1. Build Plan for Claude Code (phases with Definition of Done)
1. Risks & Cut List

-----

# 1. VISION & PILLARS

## 1.1 Logline

**LifeOS is a life sim played entirely inside a fake desktop operating system.** You start broke with a budget laptop. Every app on the desktop is a system: a bank, a gig platform, a stock broker, a crypto exchange, a mining rig manager, a real estate portal, an inbox full of opportunity and scams — and, if you go looking, a darker corner of the net where the money is faster and the consequences are real. There is no avatar. There is no world outside the screen. **The OS is the game.**

## 1.2 Design Pillars (tiebreakers for every decision)

1. **The OS is the world.** Everything is diegetic. No external HUD, no game menus that break the fiction — settings are a Settings app, saving is a System feature, the tutorial is a Setup Wizard. (Uplink’s core lesson: it didn’t feel like a game, it felt like a tool. That immersion is the product.)
1. **Numbers feel alive.** Markets move every game-minute via a real stochastic engine, not scripted curves. Rent is due. Power bills track your mining draw. The simulation runs whether you watch it or not.
1. **Risk literacy is the theme.** The boring strategy (index fund auto-invest, steady gigs) is mathematically solid and genuinely wins long-term. The exciting strategies (meme coins, leverage, insider tips) are tempting, occasionally spectacular, and honestly dangerous. The game never lies about expected value — players discover it.
1. **Respect the player’s time.** Endless sandbox, idle-friendly: passive streams accrue while away (capped), sessions of 15 minutes or 4 hours both feel rewarding. No forced resets, no prestige wall, no energy-gate monetization (there is no monetization — energy is a *simulation* mechanic, not a paywall).
1. **Dry humor, real stakes.** Tone of emails, news, and scams is deadpan-funny. Consequences (audits, crashes, burnout) are mechanical and fair, never cruel or random-feeling.

## 1.3 Player Fantasy

“I built an empire from a $500 laptop without leaving my desk.” The fantasy of the self-made internet hustler — gig work to index funds to mining rigs to property to (maybe) the grey market — compressed into a desktop you actually click around in.

## 1.4 Positioning & Inspirations

The fake-OS genre exists (Hypnospace Outlaw, Orwell, Needy Streamer Overload, Kingsway) but is almost entirely **narrative** games. The economic-sandbox slot in this genre is **empty**. LifeOS = the immersion of **Uplink/Hacknet** + the systems depth of **Capitalism/Victoria 3 (scaled down)** + the compulsion loop math of **AdVenture Capitalist** + the money layer of **The Sims** — with none of their 3D/art costs.

|Inspiration                          |What we take                                                          |What we leave                                        |
|-------------------------------------|----------------------------------------------------------------------|-----------------------------------------------------|
|Uplink / Hacknet                     |Diegetic immersion, trace/heat tension, “tool not game” feel          |Mission-only structure, story focus                  |
|AdVenture Capitalist / Cookie Clicker|Exponential cost curves (×1.07–1.15), idle accrual, milestone dopamine|Prestige resets, infinite zeros                      |
|Mad Games Tycoon 2 / Capitalism      |Multi-system economy, meaningful tradeoffs                            |2D world map / staff micromanagement                 |
|Victoria 3                           |Speed controls, regime-driven markets, “watch the line go up”         |Everything else (scope!)                             |
|The Sims                             |Needs as pacing (energy/stress), lifestyle costs                      |Avatar, house, 3D                                    |
|Drug Wars / Dope Wars                |Arbitrage + heat risk loop                                            |Real-drug content (we use data/finance crime fiction)|

## 1.5 Platform, Audience, Scope

- **Platform:** Browser first (Vite build → itch.io / static host). Electron/Steam later (architecture must keep that door open — see §15.8). Desktop-only layout (min 1280×800); mobile is out of scope v1.
- **Audience:** Tycoon/idle/strategy players, finance-curious players, fans of Hacknet-likes. Age 13+ (fictional crime themes, no explicit content).
- **Scope guardrail:** Solo dev + Claude Code. Everything in this doc ships with **zero drawn art assets** — the entire game is CSS, SVG icons (Lucide), canvas charts, and typography. That is a hard constraint and a stylistic identity.

-----

# 2. CORE LOOPS

## 2.1 Minute Loop (moment-to-moment, ~30s–2min)

Check notification → open app → make a decision (accept gig / place order / pay bill / read email) → see a number change → queue the next passive thing. **Every interaction must change a visible number or state within 2 seconds.**

## 2.2 Session Loop (~15–60 min)

1. Catch up: offline earnings toast + overnight news digest.
1. Maintain: energy, bills, tenant issues, inbox.
1. Advance: complete 2–5 gigs / rebalance portfolio / buy hardware / take a course.
1. Set up: queue passive income (mining on, auto-invest set, term deposit, rig upgrade) so the *next* session opens with a payoff.

## 2.3 Day/Week Loop (in game time)

- **Daily:** sleep window restores energy; lifestyle cost deducts; mining revenue posts at 00:00; exposure decays.
- **Weekly:** rent/wages cycle (rent monthly, salary fortnightly, see §6); market regime may transition (Markov step, §7.4); The Feed publishes a weekly wrap.
- **Quarterly:** dividends. **Yearly (game July 1):** Tax Season event (§12.6).

## 2.4 Macro Loop (the empire arc)

```
Gigs ($) → Hardware (capability) → Mining + Markets (passive $)
   ↓                                        ↓
Skills (better gigs)               Real Estate (big passive $)
   ↓                                        ↓
 [optional] Grey Market (fast $, Exposure risk) → Laundering → back to clean assets
                       ↓
         Net Worth Ladder → late-game deals (PE rounds, apartment blocks)
```

Each system feeds the next; nothing is mandatory after Phase-2 content (a pure gig-and-index-fund run must be viable and pleasant — Pillar 3).

## 2.5 The Three Currencies

|Currency                                                             |Type   |Sources                                      |Sinks                                               |
|---------------------------------------------------------------------|-------|---------------------------------------------|----------------------------------------------------|
|**Cash ($)**                                                         |Primary|Gigs, salary, trading, mining, rent, grey ops|Bills, lifestyle, hardware, assets, fees, fines, tax|
|**Energy (0–100)**                                                   |Pacing |Sleep, coffee, lifestyle quality             |Gigs, courses, gym, overwork                        |
|**Exposure (0–100)**                                                 |Risk   |Grey acts                                    |Time decay, laying low, VPN                         |
|Skills (§11.4) are a fourth, slow “currency” of permanent capability.|       |                                             |                                                    |

-----

# 3. TIME & SIMULATION MODEL

## 3.1 Clock

- **1 tick = 1 game minute.** Game starts **Monday, January 1, 2029, 08:00**.
- Speeds: **⏸ Pause · 1× · 4× · 12×** → at 1×, 3 ticks/real-second (**1 game day = 8 real minutes**); 4× = 2 min/day; 12× = 40 sec/day.
- Speed controls live in the menu bar (click or keys `space`, `1`, `2`, `3`). Certain modals (order ticket, tax wizard) auto-pause; closing resumes prior speed.
- Date/time drives: market hours (§7.2), sleep, bill schedules, event scheduler.

## 3.2 Fixed-Timestep Loop

`requestAnimationFrame` → accumulate real ms → convert by speed → run whole ticks through the **tick pipeline** (ordered, deterministic):

```
1. clock.advance()           5. markets.step()        9. exposure.decay()
2. needs.step()  (energy)    6. bills.scheduler()    10. notifications.flush()
3. gigs.progress()           7. events.scheduler()   11. stats.sample() (1/game-hour)
4. mining.accrue()           8. contacts.tick()      12. autosave.maybe() (30s real)
```

Budget: a 12×-speed frame may process ≤36 ticks; full pipeline ≤ **4ms/frame** (see §15.6).

## 3.3 Determinism & RNG

- Seeded **mulberry32**; the save stores one master seed + per-system stream counters (`rng.market`, `rng.events`, `rng.grey`, `rng.misc`). **`Math.random()` is banned** in `/engine`.
- Same seed + same inputs ⇒ identical world. This makes golden-master tests possible (§15.7) and lets players share seeds.

## 3.4 Offline Progress (idle rules)

On load: `elapsed = now − lastSaved`, convert at 1× rate, **cap at 2 game-days**.

- **Accrues offline:** mining (at last rates), staking, rent, salary, savings interest, term deposits, energy (refills via sleep schedule), exposure decay, gig-in-progress completion.
- **Frozen offline:** market prices (prevents off-screen ruin; markets resume their stochastic walk on return), events, news. Design intent: idle rewards setup, never punishes absence.
- Present as a “**While you were away**” report window (earnings breakdown + 1-button collect).

-----

# 4. THE OS SHELL

## 4.1 Boot & Login

Cold open: 1.5s black → LifeOS glyph (Instrument Serif “L”) + thin progress bar → soft chime → login card (avatar dot + player name) → desktop fades in. First run instead enters the **Setup Wizard** (§12.7). Subsequent runs: boot ≤3s, straight to desktop. Konami-style: holding `V` during boot shows fake POST text (pure flavor).

## 4.2 Desktop

- Wallpaper: 3 built-in CSS gradients — **“Terracotta Dawn”** (default; burnt orange → deep olive, subtle grain), “Graphite”, “Aurora”. Selectable in Settings.
- No desktop icons by default (apps live in the dock); files dragged out of Mail/Umbra can sit on the desktop as draggable icons (max 12).
- Right-click desktop → context menu: Change Wallpaper / Clean Up / About LifeOS (contains the **TradingView Lightweight-Charts attribution + link — legally required**, see §15.1).

## 4.3 Menu Bar (top, 28px, translucent blur)

Left → right:
`◉ LifeOS` (About, Save Slots, Export Save, Settings, Restart) · **active app name + its menus** (each app may register 1–3 menus) ─── right side: `⚡84` energy pill · `👁 32` exposure pill (**hidden until first grey act**) · `$12,840` cash (animates on change, click → Bank) · `▶ 1×` speed control · `Mon 14 Mar 2029 · 10:42` clock (click → Calendar popover) · `🔔` notification center.

## 4.4 Dock (bottom, 60px, glass)

**Signature element of the whole UI: live dock icons.** Each icon is a 44×44 live mini-canvas, not a static image:

- **Vantage** icon = real sparkline of IX500 (green/red tint by day P&L)
- **HashForge** icon = pulsing core, glow intensity ∝ current hashrate
- **Mail** = envelope + unread badge; **Meridian** = balance trend arrow; **Helix** = BTR 24h sparkline; **Pulse** = tiny energy ring; **Keys** = occupancy dots.
  Hover: 1.15 magnify + label. Bounce on notification. Right-click: app quick actions (“Collect mining revenue”, “Pause auto-invest”). Pinned order user-draggable. Apps not yet unlocked don’t appear at all (discovery is part of progression).

## 4.5 Window Manager (the core UI tech — built custom, §15.3)

- Windows: drag (title bar), resize (8 handles, min sizes per app), focus-to-front (z-order array), minimize (genie-lite 180ms to dock), maximize (fills desktop minus bars), close. Snap: drag to screen edge → half-tile; corner → quarter (subtle 80ms preview).
- Chrome: 36px title bar; **three neutral 10px dots top-left** (monochrome; glyphs `×  –  +` fade in on hover — deliberately *not* Apple’s red/yellow/green); centered title in 13px Inter Medium; optional toolbar row below.
- **RAM = max concurrent open windows** (hardware-gated: 4 → 6 → 9 → 14, §8.4). Opening one too many: gentle shake + toast “Out of memory — close a window or buy RAM at PartsBin.” This makes hardware upgrades *felt* in the hands every minute. At 32GB RAM, unlock **Spaces** (2 virtual desktops, swipe via `ctrl+←/→`).
- State (position/size/open) persists per app in the save.

## 4.6 Notification System

Toast top-right (340px, 200ms slide, auto-dismiss 6s, max 3 stacked; overflow goes straight to center). Types: `money+` (green tick, plays cha-ching if sounds on), `money−`, `alert` (amber), `event` (accent), `grey` (purple, only when Umbra unlocked). Every toast deep-links to its app. Notification Center: chronological list, “Clear all”, per-app mute toggles. **Rule: anything that changes player money > $50 must emit a notification.**

## 4.7 System Apps (always installed)

Settings · Calendar · Files · Notes · Trophies · Stats. Specs in §5.

-----

# 5. APP CATALOG

> Unlock column: how the app appears in the dock. “Start” = available after Setup Wizard.
> Every app below gets: purpose, unlock, layout, interactions, and the systems it touches.

|# |App                                |Brand        |Unlock                             |One-liner                                     |
|--|-----------------------------------|-------------|-----------------------------------|----------------------------------------------|
|1 |Mail                               |Mail         |Start                              |Quest, scam & story engine                    |
|2 |Bank                               |**Meridian** |Start                              |Accounts, bills, loans, credit score          |
|3 |Gigs                               |**HustleHub**|Start                              |Active income; skill-gated jobs board         |
|4 |Stocks                             |**Vantage**  |Start (tutorial email)             |Broker: charts, orders, portfolio, auto-invest|
|5 |Crypto                             |**Helix**    |Email at $2k net worth             |Exchange + staking + (later) tumbler          |
|6 |Mining                             |**HashForge**|Bundled with first GPU             |Rig manager, hashrate, power draw             |
|7 |Hardware                           |**PartsBin** |Start                              |Buy CPU/GPU/RAM/SSD/case/cooling              |
|8 |Real Estate                        |**Keys**     |Email at $25k net worth            |Listings, mortgages, tenants                  |
|9 |News                               |**The Feed** |Start                              |Procedural market-moving news                 |
|10|Messages                           |**Ping**     |Start                              |5 contacts, favors, opportunities             |
|11|Courses                            |**Mentora**  |Start                              |Skill training (time + money)                 |
|12|Wellness                           |**Pulse**    |Start                              |Energy, sleep schedule, stress, gym           |
|13|Browser                            |**Compass**  |Start                              |In-game web: shop fronts, bank site, wiki     |
|14|Dark browser                       |**Umbra**    |Invite email ≥$10k NW *or* SocEng 2|Grey market storefront                        |
|15|Terminal                           |Terminal     |CPU Tier 3+                        |Power-user shortcuts; grey ops console        |
|16|Trophies                           |Trophies     |Start                              |40 achievements                               |
|17|Stats                              |Stats        |Start                              |Net-worth chart, income/expense breakdown     |
|18|Settings / Calendar / Files / Notes|—            |Start                              |System utilities                              |

## 5.1 Mail — the quest engine

**Layout (720×520 default):** left rail 200px (Inbox `12`, Starred, Spam `3`, Archive) · message list (sender, subject, snippet, time) · reading pane with **action buttons rendered inside emails** (“Accept gig”, “View listing”, “Claim $200 voucher”, “Report scam”).
Email is how the game talks to the player: onboarding steps, app unlock invites, event notices (audit! tax!), story beats from contacts, newsletters, and **scams**.

- **Scams** (≈1/game-week): fake invoice, “GPU clearance — 70% off”, phishing “Meridian security check”. Clicking a scam link: lose $80–$400 + 1 stress… *unless* Social Engineering ≥3, which reveals a **“Reverse it”** button → 3-choice mini-dialog → success pays a $150–$600 “bounty” (flavor: you reported the operation / strung them along), fail wastes 30 game-min. Teaches inbox skepticism mechanically.
- **Newsletters** (“Margin Call Weekly”, free): 1 stock tip/week — **60% signal, 40% noise**, and the game tracks your acted-on-tip P&L in Stats. (Pillar 3: discover that tips are mid.)
- 5% of **Spam** is secretly real (genuine discount codes for PartsBin). Inbox Zero is an achievement.
  Full template grammar + 12 ready templates: §16.7.

## 5.2 Meridian (Bank)

**Layout (680×500):** header with total balance · tabs: **Accounts · Bills · Loans · Cards · Statements**.

- **Accounts:** Everyday (transactional) · Saver **2.8% APY** (daily compounding, instant transfers) · Term deposits 30/90/365 game-days @ **3.5 / 4.2 / 5.0%** (early break forfeits interest).
- **Bills:** list of recurring obligations (rent, power, internet, lifestyle, subscriptions, mortgage) with next-due dates; **Autopay toggle per bill** (on by default after tutorial). Missed bill: $15 late fee + credit −10 + stress +5; 3 consecutive missed rent → forced downgrade event.
- **Loans:** Personal loan limit = `creditScore × $40` at **12% APR** (weekly repayments, payout schedule shown before confirm). **Credit score 300–850, starts 550**: +1/on-time week (cap 800 via loans), −10 missed bill, −40 overdraft, +20 paying off a loan, mortgage history +. Score gates mortgages (§9.3).
- **Cards:** virtual card skin showing player name — pure flavor + spend this month.
- **Statements:** monthly ledger (also feeds the tax wizard).
  Overdraft allowed to −$500 ($15 fee + score hit); below that, purchases bounce.

## 5.3 HustleHub (Gigs)

**Layout (760×540):** left filter rail (skill, pay, duration) · gig cards grid: title, payout, duration, energy cost, required skill chip, “Accept”. Top tabs: **Board · Active (2 slots) · History · Jobs**.

- **Gig flow:** Accept → moves to Active with a progress bar advancing in game-time → completes → payout to Everyday + toast + skill XP. **Focus bonus:** +10% payout if its window is open when it completes (rewards presence without demanding it). Max **2 concurrent** gigs (3 with Hustle skill 6).
- **Payout formula:** `base × (1 + 0.08·(skillLevel − required)) × stressMod × eventMod`, `stressMod = 1 − max(0, stress−70)·0.005`.
- **Jobs tab (salaried, optional):** 3 jobs (§16.2). Taking one auto-blocks weekdays 09:00–17:00 (energy −40/day, can’t do gigs in that window) for a fortnightly salary. Steady floor vs gig freedom — both fully viable. Quit anytime (1 game-week notice); fired after 3 energy-bankrupt days (re-hireable after 30 days).
- Board refreshes daily 06:00; 8–12 gigs drawn from the table weighted by skills; rare **golden gigs** (×3 pay, 2h expiry) ~2/game-week create log-in delight.

## 5.4 Vantage (Stocks) — flagship app

**Layout (920×600 default, the biggest app):**

```
┌──────────────────────────────────────────────────────────────┐
│ ⬤ ⬤ ⬤   VANTAGE                         Cash: $4,210  ⚙    │
├──────────┬───────────────────────────────────┬───────────────┤
│ WATCHLIST│   NVA · Novara Systems            │  ORDER TICKET │
│ IX500 ▲  │   $142.18  ▲ +2.4%  (candles,     │  Buy ◉ Sell ○ │
│ NVA  ▲   │    lightweight-charts canvas,     │  Qty [ 10 ]   │
│ BTR  ▼   │    1D/1W/1M/1Y/5Y ranges)         │  ◉ Market     │
│ GENO ▼   │                                   │  ○ Limit $──  │
│ + add    │   vol bars · news flags on chart  │  Fee $3.00    │
├──────────┴───────────────────────────────────┤  Total $1,425 │
│ PORTFOLIO  value $18,402 · day ▲$214 (+1.2%) │  [ BUY NVA ]  │
│ pos rows: qty · avg · last · P&L$ · P&L% ·   │  Est. after:  │
│ alloc bar  ·  [Auto-Invest: $200/wk → IX500] │  $2,785 cash  │
└──────────────────────────────────────────────┴───────────────┘
```

- **Order types:** Market, Limit (Trading 1) · Stop-loss (Trading 3) · Short sell (Trading 5; borrow fee 6% APR; forced cover if equity <30% of short value) · Margin loan (Trading 5; borrow up to 50% of portfolio @ 9% APR; **margin call** at equity <35%: 24 game-hour warning, then auto-liquidation — brutal, fair, telegraphed).
- **Fees:** $3 flat per trade. Friction is a feature: it kills hyperactive churn and makes DCA shine.
- **Auto-Invest:** set $/week into any ETF — fires Mondays 10:05, fee-free (deliberate +EV nudge, Pillar 3). One-click from the tutorial email.
- **Dividends** quarterly (per §16.4 yields): cash or DRIP toggle per holding.
- Market hours weekdays **10:00–16:00** game time; outside hours the ticket queues orders for open. (Crypto in Helix is 24/7 — rhythm contrast.)
- News flags render on charts at event timestamps (hover = headline). The chart should make players *feel* regimes.

## 5.5 Helix (Crypto)

**Layout (760×540):** Markets list (5 coins: 24h%, sparkline) · coin view (chart + buy/sell, 0.25% taker fee) · **Wallet** (holdings + staking: lock ATH 4.5% / SOLV 7.0% APY, 7-day unlock) · **Earn** tab (staking) · after Umbra unlock, a **Tumbler** tab quietly appears (§10.5).
24/7 markets, jumpier math (§7.6), halving countdown widget for BTR (every 2 game-years; mining yields halve, historical-drift bump follows — a telegraphed macro event players plan around).

## 5.6 HashForge (Mining)

**Layout (720×520):** Rig view — case silhouette (pure CSS) with GPU slot cards (model, GH/s, temp bar, wattage) · big numbers: **Total GH/s · $/day est. · Power cost/day · Net/day** · coin selector (BTR or ATH; auto-switch toggle at Hardware 5) · uptime toggle (Off / On / Smart [pauses when net/day < 0]) · room **Heat meter** (§8.3).
Revenue posts daily 00:00 with a receipt notification. Yield decays over time + halvings (§8.2) — mining is a strong *early-mid* engine that naturally sunsets into trading/property capital, by design.

## 5.7 PartsBin (Hardware)

**Layout (720×540):** category tabs (GPU · CPU · RAM · SSD · Case · Cooling) · product cards (specs, price, owned badge) · **Your Rig** side panel with install/uninstall slots. Purchases are instant install (no shipping wait v1 — keep friction where it’s meaningful, not annoying). Sell-back at 55% of price. Full part tables + what each stat does: §8.4 / §16.5. Occasional sale events (−20–30%, announced via The Feed/Mail). Grey-market GPUs exist in Umbra: −40% price, 8% DOA risk, +6 Exposure (§10.4).

## 5.8 Keys (Real Estate)

**Layout (820×560):** Listings (cards: photo placeholder = CSS gradient + map dot, price, est. rent, yield%, suburb index sparkline) · property page (mortgage calculator inline: deposit slider, term, repayment preview) · **Owned** tab (tenant card: name, rent, lease months left, late-payment flag; maintenance log; “List for sale”).
Buy flow: offer (list ±5% slider; lowball success = f(market regime)) → if mortgage: instant approval check (credit ≥620, deposit ≥20%, repayments ≤45% of trailing-90-day income) → settlement 3 game-days. Tenants/vacancy/maintenance/value drift: §9. **You can live in one property** (zero rent, +comfort = −2 stress/day) or rent it out — not both.

## 5.9 The Feed (News)

**Layout (560×620, single scroll):** timestamped headlines with category chips (Markets · Tech · Property · Crime · World) and small impact arrows *after* the move has happened (never before). Weekly Wrap card Sundays (regime hints in prose). **Feed+ sub $15/mo:** headlines arrive **30 game-min early** — a real but modest edge worth real money (and a taste of paying for information). Headline grammar + 40 seeded headlines with effect tags: §16.6.

## 5.10 Ping (Messages)

**Layout (520×560):** contact list + chat threads (choice chips for replies, 2–3 options, no free text).
Contacts (favor 0–5 each; favors rise from choices/help, decay 1/month if ignored):

- **Riya** (recruiter): job offers; favor 3+ → salary +10% negotiated versions.
- **Marcus** (realtor): off-market listings at favor 3+ (−5% price), settlement tips.
- **Dee** (hardware nerd): PartsBin discount codes (favor×3%), overclock tutorial (unlocks OC toggle).
- **Sam** (friend): weekly hangout invite (2h game-time, −15 stress); occasionally asks to borrow $200–$2k (repaid 80% of the time at favor ≥3 — a soft trust/EV puzzle with feelings).
- **“Vex”** (fixer): appears only after Umbra; grey contracts, warnings when Exposure >40 (“you’re hot. sit down for a week.”) — Vex is the grey market’s *risk-management voice*, which is the twist: the criminal teaches caution.

## 5.11 Mentora (Courses)

Course cards: skill, levels granted, cost, study time (game-hours, consumes energy 5/h, scheduled into the day in 1–2h blocks via Calendar). Completing: skill +1 (×2 levels for bootcamps), framed cert in app (flavor), unlock toasts (“Limit orders unlocked”). Table: §16.3. Skills also grind slowly from use (gig XP) — courses are the fast lane.

## 5.12 Pulse (Wellness)

**Layout (520×500):** Energy ring (big) · Sleep schedule editor (bed/wake time; **consistency streak bonus**: 7 days same wake time → energy cap 105, regen +10% — the game’s love letter to anchored wake times) · Stress bar with contributors listed honestly (“debt pressure +12, Exposure +6, overwork +4”) · Actions: **Gym** ($15, 1h, −10 stress, 3×/wk cap, streak grants +2 energy cap) · **Coffee** (+20 energy now, −15 in 2h, 3/day soft cap then ‘jitters’: gig payouts −10%) · **Day off** (skip-to-tomorrow button; full restore; gigs paused).
Burnout: energy hits 0 → next day cap 50 + payouts −20%; clears after a full sleep. Pulse exists to *pace* the player, never to nag: one notification max/day from this app.

## 5.13 Compass / Umbra (Browsers)

**Compass:** bookmark grid of in-game “sites” that are skinned views into systems (Meridian web, PartsBin store, “Wikinomics” — a 12-article in-fiction wiki that doubles as the game’s help/codex, suburb pages for Keys). URL bar accepts site names; 404 page has a hidden achievement.
**Umbra:** dark-styled sibling, purple accent, no history kept (flavor). Storefront: §10. Opening Umbra the first time triggers Vex’s intro DM and reveals the Exposure pill in the menu bar.

## 5.14 Terminal (CPU Tier 3+)

Fake shell, 14 commands: `status` (one-screen empire summary), `pay rent`, `buy IX500 500`, `mine on/off`, `sleep`, `themes`, `neofetch` (flavor), `seed` (shows world seed)… Power-user sugar + where grey ops scripts run (§10.3 trace mini-moment). Commands map 1:1 to existing actions — no Terminal-exclusive power, just speed + style.

## 5.15 Stats, Trophies, Settings, Calendar, Files, Notes

- **Stats:** the emotional core — **Net Worth line chart** (full history, regime bands shaded), income vs expense stacked monthly bars, allocation donut, “tips P&L” tracker, time played, biggest win/loss cards.
- **Trophies:** 40 achievements grid (§16.8), locked = silhouette + hint.
- **Settings:** wallpaper, light/dark/auto, sounds, reduced motion, number format ($1.2M vs full), autosave slot, export/import save (JSON download/file pick), credits + attributions, Reset.
- **Calendar:** month grid with bill dots, events, study blocks, lease endings.
- **Files:** desktop files + grey “data packs” inventory + saved statements. **Notes:** free text, persisted (players keep their own strategies — zero-effort feature, high love).

-----

# 6. ECONOMY DESIGN

## 6.1 Philosophy

Layered economy with **exponential costs against roughly linear-per-tier income** (the proven idle-game backbone: cost multipliers between ×1.07–×1.15 per step keep upgrade pacing satisfying). But LifeOS is *grounded*: numbers stay within real-world plausibility ($500 → ~$100M endgame, never 1e300). The grounding **is** the fantasy.

## 6.2 Starting State (Standard difficulty)

|Field                                                                                                                                        |Value                                                                                     |
|---------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
|Cash                                                                                                                                         |$500 (Everyday account)                                                                   |
|Home                                                                                                                                         |Parents’ place — rent $0, but: energy cap 90, “move out” story pressure via Mail at $8k NW|
|Hardware                                                                                                                                     |“Starter Laptop”: CPU T0, iGPU 0.5 GH, 8GB RAM (4 windows), 256GB SSD                     |
|Skills                                                                                                                                       |All 0 except Hustle 1                                                                     |
|Bills                                                                                                                                        |Internet $60/mo · Lifestyle (Balanced) $25/day · Phone $30/mo                             |
|Credit score                                                                                                                                 |550                                                                                       |
|Difficulty variants: **Hard** ($50 cash + $2,000 loan pre-attached at 12%) · **Sandbox** ($50,000, all apps unlocked, achievements disabled).|                                                                                          |

## 6.3 Money Sources (with expected $/game-day at competent play)

|Phase     |Primary engine                              |Net $/game-day|
|----------|--------------------------------------------|--------------|
|Day 1–7   |Low gigs                                    |$60–120       |
|Week 2–6  |Skilled gigs (+ first GPU mining)           |$200–450      |
|Month 2–4 |Gigs + mining (2–3 GPUs) + IX500 compounding|$500–1,200    |
|Month 4–10|+ first property + portfolio                |$1,500–4,000  |
|Year 1+   |Property portfolio + markets + (opt.) grey  |$5k–25k       |
|Late      |PE deals, apartment blocks, whale positions |open-ended    |

## 6.4 Money Sinks

Recurring: rent/mortgage, power (`kWh × $0.22`; mining dominates this), internet ($60/$90/$140 tiers — top tier +2% mining yield via “better pool latency”), lifestyle ($/day by tier: Noodles 12 · Balanced 25 · Premium 45 · Lavish 120), phone $30, subscriptions (Feed+ 15/mo, VPN 40/mo).
One-off: hardware, courses, property deposits + 2% buy costs, maintenance events, fees ($3 trades, 0.25% crypto, loan interest), fines (audits), **tax** (§12.6), gym, coffee ($6).
**Design rule:** at every phase, recurring sinks should consume **35–55%** of competent income — pressure without despair. Tuning levers: lifestyle costs and gig board quality.

## 6.5 The Honest-Math Pillar in numbers

- IX500 auto-invest $200/wk from week 2, touched by nothing else → **~$68k–$80k value by end of game-year 5** (8.5% drift, the sim genuinely delivers it ±σ). The Stats app shows a “what if you’d only auto-invested” ghost line vs your actual net worth. That single chart is the game’s thesis.
- Meme coin MOON: zero drift, σ140%, jump-prone — over any year, ~35% of seeds produce a +100% window and ~50% end −60% or worse. Real lottery shape, honestly simulated.
- Newsletter tips: 60/40 → after fees, long-run ≈ breakeven-minus. The Stats tips-tracker reveals it.

## 6.6 Number formatting

`formatMoney()`: < $10k → `$8,420.50`; ≥$10k → `$84.2k`; ≥$1M → `$8.42M`; ≥$1B → `$8.42B`. Settings toggle “full numbers”. All money in **integer cents** internally (no float drift). All figures rendered in JetBrains Mono `tabular-nums`.

-----

# 7. MARKETS ENGINE (exact math — implement precisely)

## 7.1 Universe

14 stocks + 3 ETFs (§16.4) and 5 crypto assets (§16.4b). Each asset: `{ticker, name, sector, S0, mu, sigma, divYield?, beta_m, beta_s}`.

## 7.2 Sessions

Stocks: weekdays 10:00–16:00 game time (360 ticks/day, 252 trading days/yr ⇒ `dt = 1/90720` per tick). Crypto: 24/7 (`dt = 1/525600`). Outside hours stock prices hold; queued orders fill at next open ±0.2% gap noise.

## 7.3 Per-tick price update (GBM + factor model)

```
z_m            ~ N(0,1)                  // market factor (one draw/tick)
z_s[sector]    ~ N(0,1)                  // sector factors
z_i            ~ N(0,1)                  // idiosyncratic
z = 0.40·β_m·z_m + 0.45·β_s·z_s + 0.80·z_i        (then normalize: z /= sqrt(0.40²β_m²+0.45²β_s²+0.80²))
P ← P · exp( (μ_eff − σ_eff²/2)·dt + σ_eff·sqrt(dt)·z )
```

Use Box–Muller on the seeded RNG stream `rng.market`. μ_eff/σ_eff = base × regime multipliers (§7.4) × active event modifiers (§7.5).

## 7.4 Regimes (weekly Markov step, Sunday 00:00, stream `rng.market`)

|From\To                                                                                                                                                                                                                                             |BULL|NORMAL|BEAR|CRISIS|
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----|------|----|------|
|**BULL**                                                                                                                                                                                                                                            |.70 |.25   |.05 |.00   |
|**NORMAL**                                                                                                                                                                                                                                          |.15 |.70   |.13 |.02   |
|**BEAR**                                                                                                                                                                                                                                            |.10 |.30   |.55 |.05   |
|**CRISIS**                                                                                                                                                                                                                                          |.05 |.15   |.50 |.30   |
|Multipliers (μ×, σ×): BULL (1.5, 0.8) · NORMAL (1.0, 1.0) · BEAR (−0.5, 1.4) · CRISIS (−3.0, 2.2). Start NORMAL. Regime is never named outright in UI — The Feed hints in prose (“Rate cut cheers traders”). Stats shades historical bands post-hoc.|    |      |    |      |

## 7.5 Event jumps

Events (§12) may apply to an asset: instant jump `P ← P·(1+J)` (J from the event’s range, e.g. earnings beat +4–9%) and/or temp modifiers `{muAdd, sigmaMult, durationDays}`. Biotech binaries (GENO, PRMS): scheduled “trial readout” events every 120–200 days, 45% pass (+40–80%) / 55% fail (−30–60%). Telegraphed 10 days ahead in The Feed — players can bet, straddle-by-sizing, or stay out.

## 7.6 Crypto extras (jump diffusion + momentum)

On top of GBM: Poisson jumps, `λ = 2/yr` per coin (MOON: 6/yr), size `lognormal(0, 0.35)` signed ±. MOON gets a **hype state** h∈[−1,1]: news/social events push h; `μ_eff += 1.2·h·|h|`; h decays ×0.97/day — pumps that *feel* like pumps and die like them. BTR halving every 2 game-years: mining yield ÷2 (§8.2) + `muAdd +6%` for 90 days after. USDS: pegged 1.00, noise σ0.3%; one rare CRISIS-only depeg event (−8%, recovers in days) as a lesson.

## 7.7 Dividends, shorts, margin

Dividends: quarterly, `qty × price × yield/4`, ex-date drop of the same %. Shorts: proceeds held as collateral, borrow fee 6% APR daily; forced cover at equity <30%. Margin loan: ≤50% portfolio LVR @9% APR; **call** at equity<35% → 24h warning → liquidate worst P&L positions until 50%. All thresholds shown in the order ticket before confirm — no fine print.

## 7.8 Performance & history

Prices computed per tick but **stored as 1-min bars in ring buffers** (last 7 game-days fine-grained) + downsampled 1-day bars forever (≤ 5 game-years window). lightweight-charts gets bars via adapter; never feed it the raw tick stream.

-----

# 8. MINING & HARDWARE

## 8.1 Mining revenue

`net$/day = GH_total × yield(t,coin) × 0.98(pool) × uptime − Σwatts/1000 × 24 × $0.22 − throttlePenalty`
Posts daily 00:00 in coin units at spot (auto-sell toggle, or keep coins — speculation knob).

## 8.2 Yield decay (difficulty)

`yield(t) = Y0 × 0.985^(weeks since start) × 0.5^(halvings)` with `Y0`: BTR $2.10/GH/day-equivalent, ATH $1.85. Mining ROI on a GPU should run **40–70 game-days** early, drifting to 90+ later — a strong engine that gracefully sunsets (intended: capital migrates to property/markets; don’t “fix” this in tuning).

## 8.3 Heat & power

Each part lists watts. Room heat = `Σwatts/10` (0–100 scale). >70: GPUs throttle −5% GH; >85: −15% + warning. Cooling items (§16.5) subtract heat. Power bills monthly via Meridian — the first $300 power bill is a rite of passage and a Feed headline (“Local man discovers electricity costs money”).

## 8.4 What hardware does (every stat must matter)

|Part                                                                                                |Gameplay effect                                                                                       |
|----------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
|GPU (slots per case)                                                                                |Hashrate (GH) + watts                                                                                 |
|RAM 8/16/32/64GB                                                                                    |**Max open windows 4/6/9/14**; 32GB+ unlocks Spaces                                                   |
|CPU T0–T4                                                                                           |App “snappiness” (open animation 240→90ms), T3 unlocks Terminal, T4 +1 gig slot (“automation scripts”)|
|SSD 256GB–4TB                                                                                       |Save slots 1/2/3 + grey data-pack inventory cap 2/6/12/30                                             |
|Case T0–T3                                                                                          |GPU slots 1/2/4/6                                                                                     |
|Cooling                                                                                             |−heat (fan −10, AIO −25, custom loop −45)                                                             |
|Full price/stat table: §16.5. Overclock toggle (after Dee’s tutorial): +12% GH, +20% watts, +8 heat.|                                                                                                      |

-----

# 9. REAL ESTATE

## 9.1 Listings & value model

8 archetype properties (§16.9), each tied to a **Suburb Index** `SI` (GBM: μ4.5%, σ8%/yr, monthly step + city events ±). `price = basePrice × SI`. New listings rotate fortnightly (2–4 live); Marcus (favor 3+) surfaces off-market at −5%.

## 9.2 Rental operations

Tenant quality roll on listing (Good 60% / Average 30% / Risky 10% — screening fee $200 re-rolls once): late-rent chance 2%/8%/25% per month, damage event chance scales similarly. Vacancy between tenants: 0–3 weeks (regime-influenced). Maintenance events: $200–$4,000, 0.8/property/year average (table §16.9). Property manager toggle: 7% of rent, removes late-rent and halves your event interactions (the “make it idle” button).

## 9.3 Mortgages

20% min deposit · 6.1% APR · 15/30y terms · approval: credit ≥620 AND repayment ≤45% of trailing-90-day average income. Repayments are just another Bill. Extra-repayment button (recalcs payoff date — satisfying). Refinance after 1 game-year.

## 9.4 Selling & tax

Sale: 2% agent fee, 3 game-day settlement; capital gains join the tax ledger (§12.6) at 15% flat on net gains (declared automatically — property is too visible to hide; contrast with grey income, §10.6).

-----

# 10. GREY MARKET & EXPOSURE

> **Content rule (binding for all phases):** every illegal activity is *fictional and abstracted* — invented UI, invented techniques, dialog and flavor only. No real-world hacking methods, tool names, or procedures, ever. Victims occasionally surface in The Feed/Mail as consequence flavor. Tone: dry, noir-adjacent, never glamorizing; the system’s honest EV math (below) is itself the editorial.

## 10.1 Unlock & framing

At $10k NW (or Social Engineering 2) an email arrives: an “invite-only marketplace” link installs **Umbra**. First launch → Vex DMs in Ping. The Exposure pill (👁) appears in the menu bar — the moment the game’s mood shifts slightly.

## 10.2 Exposure (0–100)

- **Gains:** see ops table §16.10 (each op lists +Exposure). Spending undeclared cash on big-ticket items (property deposits, T5+ hardware) adds +1 per $5k spent (“conspicuous consumption”).
- **Decay:** −1.0/game-day; −1.5 while **Laying Low** (zero grey acts for 7 days, Vex confirms); VPN sub ($40/mo) reduces all gains 25%.
- **Burner laptop** ($1,200, Umbra-only): grey ops route through it; its own Exposure pool; consequences at thresholds hit *only* grey assets/inventory. The deluxe risk-management purchase.

## 10.3 Thresholds (telegraphed in Umbra’s sidebar, always visible)

|Exposure                                                                                                                                                                                |Consequence                                                                      |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
|25                                                                                                                                                                                      |“FinWatch” warning email (flavor, free)                                          |
|50                                                                                                                                                                                      |**Audit:** accounts frozen 2 game-days, fine 8% of cash, −30 credit              |
|75                                                                                                                                                                                      |**Raid:** grey inventory + burner seized, fine 20% of cash, Exposure resets to 40|
|100                                                                                                                                                                                     |**Monitored (30 days):** Umbra blocked, bank fees ×2, all grey contacts silent   |
|No prison, no game over (Pillar 4) — consequences are financial, recoverable, and *remembered* (audit history is visible to the player in Stats; repeat audits escalate fines +4% each).|                                                                                 |

## 10.4 The ops (full table §16.10 — highlights)

Data-pack flipping (buy leaked DB $400–2k → resell window, ±, +4/+6 exp) · Tumbling contracts (clean a client’s coins for 8% fee, +8) · **Insider tips** (Vex sells for $1,500: 70% accurate; trading >$5k on tip-day doubles its +12 exposure — pattern detection) · Pump group invites (ride MOON pumps with 30s-early signal; 20% chance the group exit-scams *you*) · Fake-review gig batches (+5, small steady) · Grey GPUs (−40% price, 8% DOA, +6) · Botnet-lite “compute lease, no questions” (rent your rig: 2× mining revenue, +9/day while active — the devil’s idle income).
**EV design:** at Exposure <30 with skills, grey ops run ~+15–25% over legit equivalents. Past 50, expected fines eat the edge; past 70 it’s strictly −EV. The system teaches position sizing better than any tooltip.

## 10.5 Laundering

Undeclared cash (grey income + undeclared mining) sits in a separate ledger (”**Unbanked**” shown only in Umbra/Terminal). Cleaning: (a) **Invoice routing** through your gig reputation — capacity `= 2× last-month legit gig income`, fee 12%, +0 exposure (the quiet way; rewards keeping a legit hustle alive); (b) **Helix Tumbler** — instant, 8% fee, +6 exposure. Unbanked cash can buy Umbra goods directly.

## 10.6 Tax interaction

Declared income is taxed (§12.6); Unbanked is not — but every Unbanked dollar *spent* on visible assets feeds the conspicuous-consumption exposure rule. The clean-vs-fast tension is the whole minigame.

-----

# 11. LIFE LAYER

## 11.1 Energy (0–100, cap modifiable)

Drains: gigs (per table), courses 5/h, gym 12, job days 40, awake past 01:00 −5/h. Restores: sleep (full if ≥7h in schedule window; else proportional), coffee (+20 now, −15 after 2h, soft cap 3/day → ‘jitters’ −10% gig pay), day-off button (full).
**Sleep consistency streak:** identical wake time (±30min) 7 days running → cap 105 & regen +10%; broken streak just resets the bonus (never punishes below baseline).
Energy 0 → **Burnout**: next day cap 50, payouts −20%, clears after one full sleep. Burnout 3× in 30 days → Sam intervention message (flavor + free −20 stress hangout).

## 11.2 Stress (0–100)

Contributors (live-listed in Pulse, always honest): debt repayments >40% income +8 · cash < 7 days of bills +12 · Exposure >40 +6 · >12h worked/day +4 · vacancy on mortgaged property +5. Reducers: gym −10 · Sam hangout −15 · day off −20 · Lavish lifestyle −5/day · paying off a loan −10 one-time.
Effects: >70 → gig payouts −15% & coffee stops working; >85 → forced Burnout day. Stress is a *dashboard of life balance*, not a nag — zero popups below 85.

## 11.3 Lifestyle tiers (daily auto-spend, set in Pulse)

Noodles $12 (regen −10%, +1 stress/day) · Balanced $25 (baseline) · Premium $45 (regen +5%, −2 stress/day) · Lavish $120 (regen +10%, −5 stress/day, Marcus/Riya favor +1/month “you look successful”). Pure tradeoff dial; no correct answer.

## 11.4 Skills (0–10): Coding · Design · Writing · Trading · Hardware · Social Engineering · Hustle

Sources: Mentora courses (+1–2/course), gig XP (every 4 completed gigs of a skill = +1 until level 5; slower after). Gates: gig requirements, order types (Trading 1/3/5), Terminal (via CPU but SocEng colors grey dialog options), grey ops (SocEng tiers), Hustle 6 → 3rd gig slot. Skill-up toast lists exactly what unlocked (always concrete).

-----

# 12. EVENTS, NEWS & EMAIL ENGINE

## 12.1 Architecture

One **event scheduler** (priority queue keyed by tick) + **weighted random pool** rolled daily at 06:00 (0–2 events/day; weights condition on regime, NW tier, flags like `hasMortgage`, `miningActive`, `exposure>0`). Every event = `{id, conditions, weight, payload: {emails?, headlines?, marketEffects?, stateChanges?}, cooldownDays}`. 30+ seeded events in §16.11.

## 12.2 Market events

Earnings (each stock, quarterly ±) · rate decisions (8/yr: cut→BULL bias headline, hike→BEAR) · sector shocks (“Chip export rules tighten”: TECH σ×1.5 7d, NVA −6%) · biotech readouts (§7.5) · crypto exchange-hack scare (−12% all coins, 1/yr max) · **IPO events** 2/yr: new ticker lists with hype premium that usually fades (subscribe via Vantage; allocation lottery 30%; listing pop +0–40% then mean-reverts −drift 60 days — the honest IPO experience).

## 12.3 Personal events

PC part failure (no cooling-tier upgrade in 6mo: 5%/mo per GPU — repair = 30% of part price) · landlord rent review (+4–8%/yr) · “old friend wedding” ($300 gift or −1 Sam favor) · power price change ±15% (annual) · PartsBin flash sale · golden gig drops.

## 12.4 Grey events (only if Exposure ever >0)

FinWatch sweep week (decay paused 7d, telegraphed by Vex) · marketplace exit-scam (any escrowed Umbra funds lost — keep balances low, another lesson) · “too-good” GPU pallet (50% DOA instead of 8% — Vex warns at favor 3+).

## 12.5 Email templates

12 templates in §16.7 with merge fields `{firstName} {ticker} {amount} {deadline}` covering: ONBOARD ×4, OFFER, EVENT-NOTICE, SCAM ×3, NEWSLETTER, STORY, INVITE (Umbra/Keys/Helix unlocks). Scam tells are *learnable* (sender domain off by one letter, urgency language, too-round numbers) — consistent rules so skepticism is a skill, not a dice roll.

## 12.6 Tax Season (every game-year, July 1–14)

Mail: “TaxTime is open.” A 4-step wizard window: (1) auto-filled income summary from ledgers (salary, gigs, realized gains, rent, declared mining), (2) deductions checklist (courses, % power if mining declared, property maintenance, Feed+ if trading-active — each a checkbox with plain-language eligibility), (3) result: refund or bill (brackets: 0% to $18k · 19% to $45k · 30% to $135k · 37% to $190k · 45% over), (4) pay/receive in 3 days. Skipping past July 14: auto-filed with zero deductions + $250 fine. Declaring mining halves its exposure contribution. The wizard is deliberately *pleasant* — a yearly financial-literacy ritual that takes 90 seconds.

## 12.7 Onboarding (diegetic Setup Wizard, target ≤8 min)

Boot → “Welcome to LifeOS” wizard: name, accent color, difficulty → desktop fades in with **exactly one** notification: a Mail from “Mum” (“rent’s free, fridge isn’t — get earning x”). Mail’s first message contains button → opens HustleHub with a highlighted starter gig → completing it triggers Meridian tour toast (balance went up!) → second email: Vantage invite with **$100 brokerage credit** + one-click “Auto-invest $20/wk” → third: set your sleep schedule in Pulse. Tutorial state machine = 5 flags; every step skippable; no overlay arrows — highlight rings + emails only (Pillar 1: even the tutorial is diegetic, Uplink-style).

-----

# 13. PROGRESSION, ACHIEVEMENTS & ENDLESS RETENTION

## 13.1 Net Worth Ladder (log10 milestones)

$1k Side Hustler · $10k Operator · $100k Portfolio Person · $1M Self-Made · $10M Magnate · $100M Untouchable. Each: full-screen-toast moment (2.5s, skippable), wallpaper variant unlock, Stats banner. NW = cash + savings + portfolio + coins + property equity + hardware resale − debts (Unbanked counts only after laundering — dirty money isn’t *worth* anything yet, thematically and mechanically).

## 13.2 Late-game systems (keep the sandbox alive)

≥$1M: **PE deal emails** (1/quarter: buy 10–25% of a private company — fictional dossier PDF-style window, 1-year lockup, outcome −60% to +300% with quality tells for high SocEng/Trading) · ≥$3M: apartment block & commercial listings (vacancy-risk yield plays) · ≥$10M: “Family Office” Stats skin + philanthropic sink (“endow a scholarship” — pure status, names a Feed article after you). Endless = horizontal breadth + ladder, never number-resets.

## 13.3 Achievements (full list §16.8 — flavor of the 40)

First Dollar · **Boring Wins** (5 game-years uninterrupted auto-invest) · Diamond Hands (hold a −40% position back to green) · Paper Hands (sell the exact weekly bottom — joke trophy) · Rite of Passage (first $300 power bill) · Landlord · Full Stack (all skills ≥5) · Clean Slate (Exposure 100→0) · Ghost (reach $1M with zero grey acts) · Vex’s Respect (decline 5 ops at >40 exposure) · Inbox Zero · Streaker (30-day wake streak)…

## 13.4 Why players return tomorrow (the checklist this design must satisfy)

Offline report worth opening (capped accrual) · daily gig board refresh + golden gigs · weekly regime/news cadence · quarterly dividends · annual tax + halving countdowns · one slow-burn arc always pending (Vex/contacts/Mum thread) · the Stats net-worth line itself.

-----

# 14. UI DESIGN SYSTEM

## 14.1 Aesthetic thesis

“**A real OS you wish existed**, with money as the protagonist.” Modern-minimal credibility (the player chose macOS-like), but ours: warmer, earthier, and typographically obsessed with numbers. The risk we take: **every numeral in the game is JetBrains Mono tabular** — chrome, emails, charts, everywhere. Numbers feel like *instrument readings*, instantly distinguishing LifeOS from both real OSes and other games. (Display face appears only at boot/milestones: Instrument Serif.)

## 14.2 Tokens (light theme / dark theme)

```
--desktop:        gradient Terracotta Dawn (#C96F4A → #5B6B4A, 8% noise)  /  (#2A1F1B → #1E241C)
--window-bg:      rgba(255,255,255,0.86) blur(20px)   /  rgba(34,34,38,0.82) blur(20px)
--surface:        #FFFFFF                              /  #2A2A2E
--ink:            #1D1D1F                              /  #F2F2F4
--ink-2:          #6E6E73                              /  #9A9AA2
--hairline:       rgba(0,0,0,0.08)                     /  rgba(255,255,255,0.10)
--accent:         #2E7D5B  (Verdant)                   /  #3FA97C
--gain:           #2E7D5B + ▲                          /  #3FA97C + ▲
--loss:           #C4452A (terracotta red) + ▼         /  #E06B4F + ▼      // arrows: colorblind-safe
--warn:           #B98300        --grey-accent (Umbra/exposure): #6B4FA0
--radius:         window 12 · card 10 · control 8 · button 6
--shadow-window:  0 8px 30px rgba(0,0,0,0.18) (focused), 0 4px 14px (blurred)
--type:           UI: Inter (13 base · 11 caption · 15 title · 20 stat · 28 hero-stat)
                  Numerals: "JetBrains Mono", tabular-nums everywhere
                  Display: "Instrument Serif" (boot, milestones only)
--spacing:        4pt grid; window padding 16; menu bar 28px; dock 60px
```

Umbra inverts to near-black with the purple accent regardless of theme — entering it should feel like a different room.

## 14.3 Motion (reduced-motion: all → 0ms fades)

Window open 160ms scale(.97→1)+fade · minimize 180ms translate-to-dock-icon · dock hover magnify 1.15 (120ms) · toast slide 200ms · cash counter: rolling digits 300ms (the money-feel workhorse) · chart crosshair instant. Nothing loops or bounces idly — calm OS, alive numbers.

## 14.4 Sound (Howler, master toggle + per-category, default ON at 40%)

boot chime · soft cha-ching (income >$50) · notify pop · error thud · trade execute click · milestone swell · optional ambience: “rig hum” loop only while HashForge focused. Eight files, all synthesizable (e.g. jsfxr-style) — **no licensed audio**.

## 14.5 Iconography & “art”

Lucide icons throughout. App identities = icon + 2-color gradient tile. Property “photos” = generated CSS gradient + suburb-seeded geometric pattern. Contact avatars = initial + color. **Zero raster images in the repo** (constraint from §1.5; it is also the look).

## 14.6 Key wireframes (build-to spec; Vantage already in §5.4)

```
DESKTOP                                MERIDIAN (680×500)
┌──────────── menu bar ─────────────┐  ┌ ⬤⬤⬤  MERIDIAN ──────────────┐
│ ◉ LifeOS  Vantage ▾   ⚡84 $12.8k │  │ Total       $12,840.22        │
│                    ▶1× Mon 10:42 🔔│  │ [Accounts][Bills][Loans][...]│
│                                   │  │ Everyday   $2,840.22   →move │
│        (windows live here)        │  │ Saver      $10,000  2.8% APY │
│                                   │  │ Term 90d   $0     4.2%  open │
│                                   │  │ ─ upcoming ─                  │
│                                   │  │ Rent  $950   due 3d  [auto✓] │
└┐ 📬 🏦 💼 📈 ⛏ 🛒 🔑 📰 ⚙ ┌──────┘  │ Power $61.40 due 9d  [auto✓] │
 └────────── dock (live icons) ─────┘  └──────────────────────────────┘

HASHFORGE (720×520)                    UMBRA (760×560, always dark)
┌ ⬤⬤⬤  HASHFORGE ─────────────────┐  ┌ ●●●  UMBRA ▒ no history ▒───┐
│ ▣ VX 5070   9.0 GH  62°C  250W   │  │ exposure ▓▓▓▓░░░░░░ 38/100  │
│ ▣ Spark460  2.0 GH  55°C  130W   │  │ thresholds: 50 audit·75 raid│
│ ▣ + empty slot (case T2: 0 left) │  │ ─ listings ─                 │
│ TOTAL 11.0 GH   est $18.40/day   │  │ data pack «retail-7k» $1,400 │
│ power −$5.81/day   NET +$12.59   │  │ GPU pallet (no Qs)  −40% ⚠8% │
│ coin ◉BTR ○ATH   [Smart uptime ✓]│  │ contract: tumble 2.1 BTR  8% │
│ room heat ▓▓▓▓▓░░░ 48            │  │ [Vex: "you're warm. slow."]  │
└──────────────────────────────────┘  └──────────────────────────────┘
```

## 14.7 Writing style (interface copy)

Sentence case, plain verbs, no exclamation marks except milestones. Buttons say what happens: “Buy 10 NVA — $1,425”, “Accept gig”, “Pay $950 rent”. Errors state cause + fix: “Order failed — market opens 10:00. It’ll queue if you confirm.” Empty states invite action: Portfolio empty → “Nothing yet. The boring move is the green button.” The OS’s voice: competent, dry, slightly on your side.

-----

# 15. TECHNICAL ARCHITECTURE

## 15.1 Stack (final — do not substitute without updating this doc)

- **Vite + React 18+ + TypeScript `strict`** · **Zustand** (+ Immer middleware, + `persist` via a storage adapter) · **Tailwind CSS** · **lightweight-charts** (TradingView, Apache-2.0 — ship the NOTICE attribution + tradingview.com link in About panel: license requirement) · **Howler** (sound) · **Lucide-react** (icons) · **Vitest** (tests). No router, no backend, no CSS-in-JS, no Redux.
- Node 20+, single `pnpm` workspace, single SPA.

## 15.2 Repo layout

```
lifeos/
  CLAUDE.md                  // working agreement for Claude Code (separate file, provided)
  docs/LIFEOS_GDD.md         // this document
  src/
    engine/                  // PURE TS. No React imports. 100% unit-testable.
      clock.ts  rng.ts  economy/ (bank.ts tax.ts lifestyle.ts)
      market/ (gbm.ts regimes.ts events.ts assets.ts)
      mining.ts hardware.ts realestate.ts grey.ts skills.ts
      gigs.ts energy.ts events/ (scheduler.ts pool.ts) save/ (schema.ts migrate.ts)
      tick.ts                // the ordered pipeline (§3.2)
    state/                   // zustand stores: gameStore.ts uiStore.ts selectors.ts
    os/                      // shell: Desktop MenuBar Dock WindowManager Window Notifications
    apps/                    // one folder per app: vantage/ meridian/ hustlehub/ ...
    ui/                      // primitives: Button Stat Money Sparkline Table Tabs
    content/                 // ALL data tables from §16 as typed const files
    audio/ styles/ utils/ (format.ts)
  tests/  (engine unit + golden sim)
```

**Hard boundary:** `engine/` is pure functions `(state, input) → state'` and may not import React, Zustand, or DOM. Apps read via selectors and dispatch engine actions. This boundary is what makes the project vibe-codable at scale — Claude Code can test economy logic headlessly.

## 15.3 Window manager (custom, ~300 lines — do not pull a library)

`uiStore.windows: WindowState[]` `{appId, x, y, w, h, z, minimized, maximized}`. Pointer events on title bar/handles (`setPointerCapture`), z = max+1 on focus, snap zones computed on drag near edges. RAM cap check in `openWindow()`. Persist geometry in save. (Custom beats react-rnd here: full control of snap/minimize-to-dock/live-icon coupling, no dependency quirks.)

## 15.4 State shape (top level — full interfaces live in `engine/save/schema.ts`)

```ts
interface GameState {
  meta: { version: number; seed: number; createdAt: number; lastSavedAt: number; difficulty: 'standard'|'hard'|'sandbox' }
  clock: { tick: number; speed: 0|1|4|12 }
  rngStreams: Record<'market'|'events'|'grey'|'misc', number>   // call counters
  player: { name: string; accent: string; netWorthHistory: Sample[]; flags: Record<string, boolean> }
  energy: { value: number; cap: number; sleepStart: number; wakeTime: number; streak: number; stress: number; lifestyle: LifestyleTier }
  skills: Record<SkillId, { level: number; xp: number }>
  bank: { everydayCents: number; saverCents: number; terms: TermDeposit[]; loans: Loan[]; creditScore: number; bills: Bill[]; unbankedCents: number }
  gigs: { board: GigOffer[]; active: ActiveGig[]; job?: Job; history: GigRecord[] }
  market: { regime: Regime; assets: Record<Ticker, AssetState>; bars: BarStore; pendingOrders: Order[] }
  portfolio: { positions: Position[]; autoInvest?: { ticker: Ticker; centsPerWeek: number }; marginLoanCents: number; shorts: Short[] }
  crypto: { wallets: Record<Coin, number>; staked: Stake[] }
  mining: { gpus: OwnedPart[]; uptimeMode: 'off'|'on'|'smart'; coin: 'BTR'|'ATH'; heat: number }
  hardware: { cpu: Tier; ramGb: 8|16|32|64; ssdGb: number; case: Tier; cooling: CoolingId[] }
  properties: OwnedProperty[]; listings: Listing[]
  grey: { unlocked: boolean; exposure: number; burner?: { exposure: number }; inventory: DataPack[]; vexFavor: number; auditCount: number; layLowUntil?: number }
  contacts: Record<ContactId, { favor: number; thread: Msg[] }>
  inbox: Email[]; eventQueue: ScheduledEvent[]; achievements: Record<AchId, number|undefined>
  settings: { theme: 'light'|'dark'|'auto'; sounds: boolean; reducedMotion: boolean; fullNumbers: boolean }
}
```

All money **integer cents**. `Sample = [tick, value]`.

## 15.5 Save system

Autosave every 30 real seconds + on tab blur, to the storage adapter (`localStorage` now; interface allows Electron file/IndexedDB later). 3 slots + Export (JSON file download) / Import (file picker, validated). `meta.version` int from day 1; `migrate.ts` holds an ordered list of pure migrations — **every schema change in any phase MUST add one**. Corrupt save → quarantine + offer export of the broken blob (never silently wipe).

## 15.6 Performance budget

Tick pipeline ≤4ms (typical frame at 12× = 36 ticks → batch market math in typed arrays per sector draw, not per-asset object churn). Bars in ring buffers (§7.8). React: stores expose narrow selectors; `Money` and chart components subscribe individually; no app re-renders on ticks it doesn’t display. Offline catch-up: chunk 2,000 ticks per macrotask with a progress bar. Target: 60fps with 6 windows open on a 2019 laptop.

## 15.7 Testing (non-negotiable, see CLAUDE.md)

- Unit (Vitest): every `engine/` function (tax brackets, GBM step stats, mortgage approval, exposure thresholds, payout formula, format utils).
- **Golden sim:** seed 42, headless `runYears(1)` with a scripted median-player bot → assert: no NaN/negative-cent states, net worth within [Xlo, Xhi] band per quarter, ≥20 events fired, tax fired once, mining ROI within ±5% of §8.2 table. Runs in CI/pre-commit; a phase is not done while red.
- Determinism test: two runs same seed ⇒ deep-equal final state.

## 15.8 Electron-later checklist (build now, benefit later)

No `window.localStorage` direct calls outside the storage adapter · no remote URLs at runtime (fonts self-hosted) · all paths relative · feature flags in one file. Then Electron = wrap + file-storage adapter + Steam page.

-----

# 16. CONTENT TABLES (initial data — `src/content/*.ts`)

## 16.1 Gigs (HustleHub board pool)

|Gig                       |Skill req   |Energy|Duration|Base pay|Notes                   |
|--------------------------|------------|------|--------|--------|------------------------|
|Data entry sprint         |—           |10    |60m     |$18     |tutorial gig            |
|Mystery shopper report    |—           |8     |45m     |$25     |                        |
|Flyer design              |Design 1    |12    |90m     |$35     |                        |
|Blog post (800w)          |Writing 1   |12    |90m     |$40     |                        |
|Tutoring session          |any skill ≥4|10    |60m     |$45     |                        |
|Bug-fix bounty            |Coding 2    |15    |120m    |$60     |                        |
|Landing page              |Coding 3    |18    |240m    |$140    |                        |
|PC build for client       |Hardware 3  |20    |180m    |$120    |+Dee favor chance       |
|Brand package             |Design 5    |25    |600m    |$520    |                        |
|Website build             |Coding 4    |25    |480m    |$320    |                        |
|App prototype             |Coding 6    |30    |720m    |$700    |                        |
|Trading-desk research memo|Trading 4   |18    |240m    |$260    |                        |
|Copy audit                |Writing 4   |15    |180m    |$170    |                        |
|Golden gig (any)          |varies      |×1    |×1      |×3      |2h expiry, sparkle style|

## 16.2 Jobs (salaried; fortnightly pay; weekdays 9–17 blocked; −40 energy/workday)

|Job             |Req      |Salary/yr|Quirk            |
|----------------|---------|---------|-----------------|
|Support Agent   |—        |$50k     |+1 SocEng XP/week|
|Junior Developer|Coding 4 |$70k     |+1 Coding XP/week|
|Market Analyst  |Trading 4|$77k     |free Feed+       |

## 16.3 Mentora courses

|Course                          |Skill      |$   |Study time|
|--------------------------------|-----------|----|----------|
|Touch Typing & Tools            |Hustle +1  |$60 |4h        |
|Intro to Code                   |Coding +1  |$120|8h        |
|Web Dev Bootcamp                |Coding +2  |$900|30h       |
|Design Fundamentals             |Design +1  |$120|8h        |
|Brand Studio Masterclass        |Design +2  |$850|28h       |
|Writing for the Internet        |Writing +1 |$90 |6h        |
|Markets 101                     |Trading +1 |$150|8h        |
|Technical Analysis (skeptically)|Trading +2 |$700|24h       |
|PC Building & Tuning            |Hardware +1|$100|6h        |
|Advanced Cooling & OC           |Hardware +2|$600|20h       |
|Persuasion & Pretexting         |SocEng +1  |$200|10h       |
|Human Firewalls                 |SocEng +2  |$950|30h       |

## 16.4 Stocks & ETFs `{S0, μ%/yr, σ%/yr, div%, sector, β_m, β_s}`

|Ticker                                                                                                 |Name              |S0 |μ      |σ     |Div|Sector                  |
|-------------------------------------------------------------------------------------------------------|------------------|---|-------|------|---|------------------------|
|NVA                                                                                                    |Novara Systems    |142|11     |38    |0  |TECH                    |
|CLD                                                                                                    |Cloudline         |88 |9      |32    |0  |TECH                    |
|QBT                                                                                                    |Qubit Dynamics    |24 |14     |60    |0  |TECH                    |
|SOLR                                                                                                   |Solarius Energy   |31 |8      |30    |1.0|ENERGY                  |
|PETR                                                                                                   |Petrana Corp      |54 |5      |24    |4.0|ENERGY                  |
|MERB                                                                                                   |Meridian Bank Corp|47 |6      |22    |3.5|FIN                     |
|VNTG                                                                                                   |Vantage Group     |63 |7      |26    |2.0|FIN                     |
|CART                                                                                                   |Cartful           |95 |7      |25    |1.0|CONS                    |
|FZZY                                                                                                   |Fizzy Co          |38 |5      |18    |3.0|CONS                    |
|LUXE                                                                                                   |Luxe Holdings     |120|6      |28    |1.5|CONS                    |
|GENO                                                                                                   |Genova Labs       |18 |9      |45    |0  |HEALTH (binary events)  |
|MEDI                                                                                                   |Medlane           |71 |6      |20    |2.5|HEALTH                  |
|RKT                                                                                                    |RocketRetail      |7.4|2      |70    |0  |CONS (meme)             |
|PRMS                                                                                                   |Promise Bio       |3.1|0      |80    |0  |HEALTH (binary, lottery)|
|**IX500**                                                                                              |Index 500 ETF     |100|**8.5**|**15**|1.8|ETF (β_m 1.0)           |
|DIVY                                                                                                   |High Yield ETF    |50 |6      |13    |5.0|ETF                     |
|BNDS                                                                                                   |Bond Fund         |25 |3.5    |5     |3.0|ETF                     |
|β defaults: β_m 1.0, β_s 1.0; meme/lottery names β_m 0.6 (idio-dominated). ETFs skip the sector factor.|                  |   |       |      |   |                        |

## 16.4b Crypto `{P0, μ, σ, stake%, λjumps/yr}`

|Coin|Name     |P0     |μ |σ  |Stake|λ               |
|----|---------|-------|--|---|-----|----------------|
|BTR |Bitra    |$42,000|25|75 |—    |2               |
|ATH |Aethel   |$2,400 |20|85 |4.5% |2               |
|SOLV|Solvana  |$98    |18|95 |7.0% |3               |
|MOON|Mooncoin |$0.042 |0 |140|—    |6 + hype        |
|USDS|StableUSD|$1.00  |0 |0.3|2.0% |depeg event only|

## 16.5 Hardware (PartsBin)

|Part                       |Price                |Stat                                   |Watts |
|---------------------------|---------------------|---------------------------------------|------|
|GPU T1 “Spark 460”         |$450                 |2.0 GH                                 |130   |
|GPU T2 “Volt 570”          |$900                 |4.5 GH                                 |200   |
|GPU T3 “VX 5070”           |$1,800               |9.0 GH                                 |250   |
|GPU T4 “VX 5070 Ti”        |$2,600               |13.0 GH                                |285   |
|GPU T5 “Titanis 90”        |$4,800               |24 GH                                  |450   |
|GPU T6 “Quantum Q1”        |$12,000              |60 GH                                  |600   |
|CPU T1→T4                  |$180/$420/$950/$2,200|snappiness; T3 Terminal; T4 +1 gig slot|65–170|
|RAM 16/32/64GB             |$110/$260/$640       |windows 6/9/14; 32+ Spaces             |—     |
|SSD 1/2/4TB                |$130/$320/$780       |slots 2/3/3 + packs 6/12/30            |—     |
|Case T1/T2/T3              |$120/$340/$900       |GPU slots 2/4/6                        |—     |
|Fan kit / AIO / Custom loop|$60/$240/$900        |heat −10/−25/−45                       |—     |

## 16.6 Headline grammar + sample effects (The Feed)

`[{co} smashes quarterly forecasts]` → `{ticker, jump:+4..9%}` · `[{co} misses badly; CFO "disappointed"]` → `−5..−12%` · `[Rates cut 25bp]` → regime→BULL bias headline (no direct jump) · `[Chip export rules tighten]` → TECH σ×1.5 7d, NVA −6% · `[{coin} ETF rumor]` → coin +8..18% · `[Exchange hack scare]` → all coins −12% · `[Suburb {x} rezoned for transit]` → SI +6% · `[FinWatch announces sweep week]` → grey decay paused · 40 seeded variants ship in `content/headlines.ts`, each `{text, weight, conditions, effects[]}`.

## 16.7 Email templates (merge-field summaries)

ONBOARD-1 Mum “fridge isn’t free” → button:HustleHub · ONBOARD-2 Vantage invite + $100 credit + auto-invest CTA · ONBOARD-3 Pulse sleep setup · ONBOARD-4 bills autopay nudge · OFFER-GIG golden gig alert · INVITE-HELIX at $2k NW · INVITE-KEYS at $25k (from Marcus) · INVITE-UMBRA at $10k (anonymous; SocEng flavor variant) · EVENT-AUDIT (FinWatch, formal-cold tone) · TAXTIME open/reminder/fine sequence · SCAM-A invoice (domain `meridlan.com`) · SCAM-B GPU clearance (urgency + too-round prices) · SCAM-C bank “security check” (asks you to ‘verify’ by sending $1 — never do banks ask this; consistent tell) · NEWSLETTER weekly tip (tracked) · STORY-SAM / STORY-VEX beats.

## 16.8 Achievements (40)

First Dollar · Hundredaire · $1k/$10k/$100k/$1M/$10M/$100M ladder (7) · Boring Wins · Ghost · Diamond Hands · Paper Hands · Rite of Passage · Landlord · Mogul (5 properties) · Block Party (apartment block) · Hash Slinger (1,000 GH-days) · Halving Survivor · Full Stack · Specialist (any skill 10) · Streaker · Well Slept (30 nights ≥7h) · Inbox Zero · Scam-proof (reverse 3 scams) · Clean Slate · Vex’s Respect · Cold Feet (close Umbra within 60s of first open) · Audit Me Once · Margin of Error (survive a margin call) · Short Story (profitable short) · Dividend Dynasty ($10k lifetime divs) · IPO Lottery Winner · Bag Holder (hold MOON −80%) · Tax Nerd (claim every eligible deduction) · Minimalist ($100k on Noodles lifestyle) · High Roller (Lavish 1 year) · Terminal Velocity (50 terminal commands) · Hidden: 404 page · Hidden: `neofetch` · Hidden: name a property after the dev.

## 16.9 Properties (base prices; × SuburbIndex)

|Property                                                                                                            |Price|Rent/mo |Maint events         |
|--------------------------------------------------------------------------------------------------------------------|-----|--------|---------------------|
|Northside Studio                                                                                                    |$185k|$1,150  |low                  |
|Lakeview 1BR                                                                                                        |$290k|$1,750  |low                  |
|Garden Townhouse                                                                                                    |$460k|$2,600  |med                  |
|CBD 2BR                                                                                                             |$620k|$3,400  |med                  |
|Suburban House                                                                                                      |$750k|$3,900  |med                  |
|Brick Duplex                                                                                                        |$980k|2×$2,400|med                  |
|Small Office                                                                                                        |$1.4M|$8,200  |high vacancy risk    |
|Apartment Block (6)                                                                                                 |$3.2M|6×$1,900|high, mgr recommended|
|Maintenance table: leak $200–600 · appliance $400–900 · roof $2.5–4k · “tenant’s exotic pet incident” $800 (flavor).|     |        |                     |

## 16.10 Grey ops (Umbra)

|Op                            |Cost/req  |Return                              |+Exposure              |
|------------------------------|----------|------------------------------------|-----------------------|
|Data pack flip (small)        |$400      |resell $520–760 in 1–3d window      |+4 buy / +6 sell       |
|Data pack flip (large)        |$2,000    |$2.4k–4.2k, SSD slot req            |+6/+9                  |
|Tumbling contract             |SocEng 3  |8% of 0.5–4 BTR                     |+8                     |
|Insider tip                   |$1,500    |70% accurate, 1 ticker, 5-day window|+12 (×2 if >$5k traded)|
|Pump group ride               |invite    |MOON signal 30s early; 20% exit-scam|+10                    |
|Fake review batch             |SocEng 2  |$90/batch, 30m                      |+5                     |
|Grey GPU pallet               |−40% price|8% DOA                              |+6                     |
|Compute lease (“no questions”)|rig idle  |mining ×2 revenue                   |+9/day active          |

## 16.11 Event pool (30 seeded — id, condition→effect summaries)

8× earnings (quarterly per covered ticker) · 8× rate decisions/yr · 2× sector shocks/yr · 2× biotech readouts/yr · 2× IPOs/yr · 1× exchange-hack scare cap/yr · halving (fixed schedule) · annual: tax season, power reprice, rent review (if renting), wedding invite · monthly-ish: flash sale, golden-gig burst, suburb rezoning (1/yr) · conditional: part failure (no cooling), audit/raid (exposure), sweep week, exit-scam (escrow>0), Sam loan ask (favor≥2) · CRISIS-only: USDS depeg.

-----

# 17. BUILD PLAN FOR CLAUDE CODE

> Work **one phase per session**. A phase is complete only when its **Definition of Done** passes (including tests) and the work is committed + tagged `phase-N-done`. Never start phase N+1 with N’s DoD red. The suggested prompts assume `CLAUDE.md` and this GDD are in the repo.

### Phase 0 — OS Shell *(the foundation; take the time to make it feel great)*

**Scope:** Vite/TS/Tailwind/Zustand scaffold · boot screen · desktop + wallpapers · menu bar (static stats) · dock with hover/bounce · custom window manager (open/close/drag/resize/min/max/focus/snap) · notifications · Settings app (theme, reduced motion) · 2 stub apps · design tokens from §14.2.
**DoD:** 5 windows open/drag/snap at 60fps · window geometry survives reload · light/dark/reduced-motion work · `pnpm test` green (format utils + window store).
**Prompt:** “Read CLAUDE.md, then GDD §3–4, §14, §15. Build Phase 0 exactly to spec. Start with the window manager store + one Window component; show me the desktop with two stub apps before polishing motion.”

### Phase 1 — Time & Persistence

**Scope:** tick engine + pipeline skeleton (§3.2) · speed controls in menu bar + hotkeys · calendar data · seeded RNG streams · save schema v1 + autosave/slots/export/import · offline catch-up (“While you were away” stub).
**DoD:** speeds correct (1 game-day = 8 real min at 1×) · reload resumes exact tick · two same-seed runs deep-equal · migration scaffold exists with test.

### Phase 2 — First Money Loop *(vertical slice — the game becomes a game here)*

**Scope:** Meridian (accounts, bills, autopay, credit) · energy/sleep/lifestyle (Pulse minimal) · HustleHub (board, 8 gigs, 2 slots, payout formula) · cash/energy in menu bar · Mail with ONBOARD sequence · Setup Wizard difficulty select.
**DoD:** a new player reaches first $500 earned inside 20 real minutes, guided only by emails · bills fire and autopay works · burnout triggers and clears per §11.1 · golden sim v0 (run 30 game-days, assert solvency band).
**⛳ Playtest gate:** Husnain plays 20 minutes. If the loop isn’t *fun-adjacent* yet (numbers feel good, gigs satisfying), tune §16.1 pays / §6.4 sinks **before** building markets.

### Phase 3 — Vantage & the Market Engine

**Scope:** GBM + factor model + regimes (§7.3–7.4) for 6 stocks + IX500 · bar ring buffers + lightweight-charts integration (attribution in About) · order ticket (market/limit) · portfolio + P&L · dividends · auto-invest · market hours/queueing · The Feed stub printing regime-flavored headlines.
**DoD:** golden sim 1 game-year: IX500 CAGR within 8.5%±6pts across 5 seeds, no NaN · charts pan/zoom smoothly with 1yr of daily bars · buy→hold→dividend→sell round-trips to the cent · Boring Wins ghost-line appears in a basic Stats app.

### Phase 4 — Helix, HashForge, PartsBin

**Scope:** 5 coins (jump diffusion + MOON hype) · staking · mining engine (§8) · heat/power · hardware effects (RAM→window cap live, CPU/SSD/case/cooling) · power bills into Meridian.
**DoD:** mining ROI matches §8.2 within ±5% · RAM upgrade visibly raises window cap mid-session · electricity charge reconciles with watt-hours to the cent.

### Phase 5 — Events, Mail full, News full, Tax

**Scope:** event scheduler + 30-event pool · email templates incl. scams + reverse-scam flow · newsletters with tracked tips · earnings/IPO/biotech events wired to market · Tax wizard (§12.6).
**DoD:** 1-year sim fires ≥20 events + exactly one tax season · scam tells consistent per §12.5 · tips tracker shows in Stats.

### Phase 6 — Keys (Real Estate)

**Scope:** listings/SI model · buy flow + mortgage approval/repayments · tenants, vacancy, maintenance, manager toggle · sell + CGT into tax ledger · Marcus integration.
**DoD:** full buy→rent→event→sell loop · mortgage math matches an amortization oracle test · NW includes property equity correctly.

### Phase 7 — Umbra (Grey Market)

**Scope:** exposure system + thresholds/consequences (§10.3) · 8 ops · laundering paths · burner · Vex thread + warnings · undeclared-income tax interaction.
**DoD:** sim-verified EV table (ops +EV at exposure<30, −EV past 70) · audit/raid/monitored states recoverable · content rule (§10) audited: zero real-technique text.

### Phase 8 — Life polish & retention

**Scope:** Ping full (5 contacts/favors) · Mentora · Trophies (40) · Stats full (net-worth chart with regime bands, ghost line, breakdowns) · late-game PE deals · sounds · onboarding final pass · balance sweep against §6.3 + §13.4 checklist.
**DoD:** fresh-save 10-minute new-player test clean · all 40 achievements triggerable (sim or manual script) · perf budget holds with 6 windows + charts · a 3-hour session produces zero console errors.

**MVP = Phases 0–3.** Ship that to itch.io as “LifeOS — Early Build” and get strangers’ feedback before building 4–8.

-----

# 18. RISKS & CUT LIST

|Risk                           |Mitigation                                                          |
|-------------------------------|--------------------------------------------------------------------|
|Market engine perf at 12×      |typed-array batch math; bars not ticks to charts; §15.6 budget test |
|Save corruption across phases  |versioned migrations from day 1; quarantine-not-wipe                |
|Scope creep (the classic)      |phases are locked; new ideas go to `docs/BACKLOG.md`, not the sprint|
|Economy feels grindy or trivial|Phase-2 playtest gate; tuning levers isolated in `content/` only    |
|“80% done” stall               |every phase has a binary DoD + tag; MVP ships publicly at Phase 3   |

**Cut order if needed (last → first):** PE deals · Ping favors depth · salaried jobs · commercial property · shorting/margin · sounds · Spaces · Terminal. **Never cut:** auto-invest, the ghost line, offline report, the live dock icons, exposure honesty.

-----

## Appendix A — Glossary

Tick (1 game-minute) · NW (net worth) · SI (Suburb Index) · GH (gigahash, abstract mining unit) · Exposure (grey heat 0–100) · Unbanked (undeclared cash ledger) · DoD (definition of done) · Golden sim (seeded headless year-long test).

## Appendix B — Legal/Distribution notes

All company/app/coin names are fictional; vet “LifeOS” trademark before any commercial release. lightweight-charts requires TradingView attribution (§15.1). Self-host fonts (Inter, JetBrains Mono, Instrument Serif — all OFL). itch.io build: `vite build`, upload `dist/`, “played in browser” + downloadable zip.

*— End of GDD v1.0. Change anything you like — but change it here first, then in code.*