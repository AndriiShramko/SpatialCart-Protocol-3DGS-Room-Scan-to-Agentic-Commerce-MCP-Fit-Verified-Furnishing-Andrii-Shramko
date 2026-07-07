# Market Landscape: From 3D Gaussian-Splatting Room Scans to Fit-Verified Agentic Commerce

> **Author:** Andrii Shramko
> **Document status:** Market homework for the **SpatialCart Protocol** — an open specification at the **concept / spec stage, with no MVP yet**. This is a grounded survey, not a product claim.
> **Honesty note:** SpatialCart Protocol is a *designed specification and priority disclosure*. It is **not built, benchmarked, or production-ready**. Nothing here should be read as a claim that the integrated loop already works in code — only that the surrounding building blocks exist and the connective layer does not.

This document answers the one question that decides whether **SpatialCart Protocol** is worth building: **how can an AI agent measure my room from a phone scan and order furniture that actually fits** — and does the full loop (real 3D room scan → persistent measured spatial database → an AI agent that designs, fit-checks, and orders real furniture across multiple stores) already exist anywhere?

Short answer, evidence-backed below: **No. Every link in the chain exists in isolation; nobody has wired them together, and the two signals that matter most — in-room fit and delivery-path (doorway / stair / elevator) clearance — are acknowledged industry-wide as missing.**

To keep this useful for a financing partner, every section separates **what is web-verified** from **what could not be verified** (including gaps where vendor pages returned HTTP 403 and facts came from search snippets rather than first-party reads). A formal patent / freedom-to-operate search has **not** been performed — see the caveats.

---

## 1. State of agentic commerce — which agents and protocols actually have agent/MCP commerce endpoints?

Agentic commerce stopped being a demo in late 2025. Two open protocols and several walled gardens now define the "ordering" half of the loop. The relevance to SpatialCart is direct: **the checkout rails already exist, so the missing piece is not how an agent pays — it is whether the agent knows the product physically fits the room and fits through the door.** This is exactly the question *how do agentic commerce protocols like ACP and UCP handle physical product dimensions and room fit?*

### The demand is already here — not a forecast

The behavioural shift is measurable, which is what makes the missing fit-signal urgent rather than academic:

- **Adobe Analytics:** US online spend hit a record **$257.8B** over the 2025 holidays, with **generative-AI retail traffic up ~693% YoY** — and AI-referred shoppers converting **31% higher** and generating 254% more revenue per visit. <https://business.adobe.com/blog/generative-ai-powered-shopping-rises-with-traffic-to-retail-sites>
- **Salesforce:** during Cyber Week 2025, AI and agents **influenced ~20% of all global orders (~$67B)** as global spend hit a record $336.6B. <https://www.salesforce.com/news/press-releases/2025/12/05/cyber-week-ai-agents-sales/>
- **Visa:** cited a **4,700%+ surge** in AI-driven traffic to US retail sites in a single year. <https://investor.visa.com/news/news-details/2025/Visa-Introduces-Trusted-Agent-Protocol-An-Ecosystem-Led-Framework-for-AI-Commerce/default.aspx>
- **Forward estimates:** McKinsey — agentic commerce could orchestrate **up to $3–5T** globally / up to $1T of US retail by 2030 (<https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-agentic-commerce-opportunity-how-ai-agents-are-ushering-in-a-new-era-for-consumers-and-merchants>); Gartner — **90% of B2B buying AI-agent-intermediated, ~$15T**, by 2028 (<https://www.gartner.com/en/newsroom/press-releases/2025-10-21-gartner-unveils-top-predictions-for-it-organizations-and-users-in-2026-and-beyond>).

**Why this matters for SpatialCart:** the money and traffic already flow through agents, so the marginal value now shifts from *access* to *quality of outcome* — and for large physical goods, outcome quality **is** fit. A spatially-blind agent just scales the wrong-size mistake (and its return) faster.

### The orchestration / checkout layer

- **ACP (Agentic Commerce Protocol)** — open standard co-developed by **OpenAI + Stripe**, launched **2025-09-29** alongside ChatGPT "Instant Checkout." Apache-2.0 licensed (a precedent worth matching for any future SpatialCart reference code). It defines an Agentic Checkout API, a Delegate Payment API (Stripe **Shared Payment Token**, scoped to a single merchant + cart total, never exposing card credentials), and a **Product Feed**. Per the repo, the spec evolved to a **2026-04-17** version adding cart, feed, orders, authentication, and **MCP integration**. Repo: <https://github.com/agentic-commerce-protocol/agentic-commerce-protocol>; launch posts: <https://openai.com/index/buy-it-in-chatgpt/>, <https://stripe.com/newsroom/news/stripe-openai-instant-checkout>.
- **UCP (Universal Commerce Protocol)** — competing/complementary open standard co-developed by **Google + Shopify**, announced **2026-01-11** at NRF 2026. Co-developers/partners include **Shopify, Etsy, Wayfair, Target, Walmart**; endorsed by 20+ including **Visa, Mastercard, Amex, Adyen, Stripe, Best Buy, Macy's, The Home Depot, Zalando, Flipkart**. An agent fetches a merchant **capability profile** from a well-known discovery URL; transports include REST, GraphQL, JSON-RPC, A2A, and **MCP**. Positioned explicitly as the **orchestration layer that runs over MCP** (MCP = data access, UCP = commerce orchestration). Sources: <https://developers.googleblog.com/under-the-hood-universal-commerce-protocol-ucp/>, <https://blog.google/products/ads-commerce/agentic-commerce-ai-tools-protocol-retailers-platforms/>, <https://shopify.engineering/UCP>, <https://www.axios.com/2026/01/11/google-shopify-ai-shopping-standard-nrf-2026>.
- **MCP (Model Context Protocol)** — built by Anthropic, **donated to the Linux Foundation in December 2025**, backed by Block, OpenAI, Google, Microsoft, AWS. Stripe, Shopify, and WooCommerce run MCP servers. **MCP is the de-facto agent-to-tool data layer underneath both ACP and UCP** — which is exactly the slot SpatialCart is designed to occupy as its data layer. Source: <https://en.wikipedia.org/wiki/Agentic_commerce>.

**Why this matters for SpatialCart:** SpatialCart is designed to **sit between MCP (data) and ACP/UCP (checkout)**, not to compete with them. The fit-verification schema would be a new *signal*, consumed by existing rails — not a rival checkout protocol. So *what is the missing data layer between a 3D room scan and buying furniture online?* — it is a standardized fit-verification signal, not another payment standard.

### Distribution is already wired into Shopify

- Every Shopify store is **automatically assigned a Storefront MCP server endpoint** (product search, cart operations, policy Q&A) with no extra merchant work. Shopify's "Agentic Storefronts" surface merchant data across ChatGPT, Microsoft Copilot, Google AI Mode, and Gemini, and the Storefront Catalog MCP is migrating to UCP. Sources: <https://shopify.dev/docs/agents>, <https://shopify.dev/docs/agents/catalog/storefront-mcp>, <https://www.shopify.com/news/ai-commerce-at-scale>. This is the **long-tail distribution channel** for any furniture-fit signal: 1M+ merchants already addressable by agents.

### Live validation: delivery carriers are now launching the agent ↔ stores ↔ delivery loop (Poland / EU, June 2026)

The most telling 2026 signal is that the **shift to agent-operated commerce is now being led by a logistics/delivery company, not only by the AI labs** — exactly the convergence SpatialCart is designed for:

- **InPost** (Poland's largest parcel-locker / last-mile carrier) launched an in-app **AI shopping assistant, "Von Halski,"** as a beta **rolling out from 2026-06-16** to the InPost Mobile base (**~17 million users**). A user describes what they want in text or voice; the assistant searches thousands of offers from **~4,000 registered merchants** (including footwear retailer Modivo) and returns a product carousel — **no per-sale commission to merchants; the carrier monetizes via delivery fees**. This is a carrier wiring **stores + an AI agent + its own delivery** into one "just say what you want" flow. Sources: <https://www.bloomberg.com/news/articles/2026-03-16/inpost-readies-ai-shopping-assistant-to-take-on-marketplaces>, <https://xyz.pl/poland-unpacked/inpost-launches-ai-powered-shopping-assistant-in-mobile-app-3213/>, <https://brandsit.pl/en/inpost-launches-an-ai-shopping-assistant/>.
- **Allegro** (Poland's largest marketplace) has **partnered with OpenAI** and launched its own AI assistant, expanding agent-driven shopping + delivery options in parallel. Source: <https://ecommercenews.eu/allegro-enters-partnership-with-openai/>.

**Why this matters for SpatialCart:** these agents already chain *discover → order → deliver* — but they remain **spatially blind**. Von Halski can put a wardrobe in your carousel, yet neither it nor the carrier knows whether that wardrobe fits the alcove or clears your front door — and the carrier eats the cost of the failed delivery. A delivery-integrated agent is precisely where **fit-verification (in-room fit + delivery-path clearance)** has the highest, most immediate ROI: fewer failed deliveries, fewer returns, higher "just order it" completion. The "*one agent you can tell 'just do it' and it's done right, no follow-up questions*" future is now visibly forming — SpatialCart supplies the missing spatial input that makes "done right" true for physical goods.

### Do the rails already carry product dimensions? (Critical for fit)

- **Yes — partially.** The **ACP Product Feed** (~67 fields across 15 groups) supports **optional length/width/height with a required `dimensions_unit` (in/cm)** plus optional weight. Dimensions are tightly coupled (all-three-plus-unit). Google Merchant Center likewise carries schema.org `Product` width/height/depth. Sources: <https://developers.openai.com/commerce/product-feeds/spec?fields=required>, <https://www.aishoppingfeeds.com/blog/openai-acp-product-feed-spec-walkthrough/>.
- **But** these feeds carry *raw product dimensions only*. They do **not** carry the derived signals SpatialCart proposes to standardize: a **per-SKU fit envelope reconciled against a measured room**, or **delivery-path clearance** (narrowest doorway, stair turn, elevator, ceiling). That gap is the wedge — and the answer to *how do I make sure furniture an AI orders will fit through my door?*
- **The formal standards bodies stop at raw dimensions too.** GS1's **Global Data Model** structures width/height/depth, and Khronos's **3D Commerce Asset Creation Guidelines 2.0** (SIGGRAPH 2025) standardize real-world scale/units in glTF (with `KHR_xmp_json_ld` for embedded metadata) — but **neither defines a "minimum delivery clearance / narrowest-passage" attribute** or a room-reconciled fit envelope. The fit/clearance contract is genuinely unclaimed *standards* whitespace, not just an unbuilt product. Sources: <https://www.gs1.org/standards/gdm>, <https://www.khronos.org/news/>.

### Payments are solved

- **Visa Intelligent Commerce** (2025-04-30) and **"Intelligent Commerce Connect"** offer one integration spanning four agent protocols (Trusted Agent Protocol, Machine Payments Protocol, ACP, UCP). **Mastercard "Agent Pay"** (April 2025) reported live authenticated agentic transactions (Hong Kong, Thailand) via Payment Passkeys. Sources: <https://corporate.visa.com/en/sites/visa-perspectives/newsroom/visa-partners-complete-secure-agentic-transactions.html>, <https://techinformed.com/visa-opens-one-integration-for-ai-agent-payments/>, <https://www.digitalcommerce360.com/2025/10/16/visa-mastercard-both-launch-agentic-ai-payments-tools/>.

### Walled gardens (not ACP/UCP)

- **Amazon** — "Buy for Me" (announced April 2025) extends Rufus to purchase from external sites, routing payment/returns through Amazon; **reported** to cover **100M+ products from 400,000+ merchants** by March 2026 (figures from secondary analyst write-ups, not a first-party Amazon page). On **2026-05-13** Amazon renamed Rufus to **"Alexa for Shopping"** in the US. Sources: <https://www.aboutamazon.com/news/retail/alexa-for-shopping-ai-assistant>, <https://www.cnbc.com/2026/05/13/amazon-ditches-rufus-ai-chatbot-in-favor-of-alexa-shopping-agent.html>.
- **Perplexity** — free agentic shopping with PayPal across 5,000+ merchants (November 2025). Source: <https://www.cnbc.com/2025/11/19/perplexity-ai-online-shopping-paypal.html>.

### Furniture retailers specifically

- **Wayfair** — UCP co-developer and a named early agentic-checkout partner on Google AI Mode (Feb 2026). **Home Depot** — expanded its Google Cloud agentic-AI partnership at NRF 2026 and endorsed UCP. **Ashley Furniture** — named UCP launch partner and reported live on Stripe's Agentic Commerce Suite. These are precisely the **large/bulky-goods** retailers where doorway/stair fit is the #1 return driver. Sources: <https://corporate.homedepot.com/news/partnerships/home-depot-and-google-cloud-launch-agentic-ai-tools-help-customers-and-associates>, <https://www.retaildive.com/news/home-depot-wayfair-agentic-ai-customer-experience/809510/>.
- **IKEA** — has an AI Assistant (GPT Store) that uses room dimensions/style/budget and LiDAR room scanning, but **no confirmed first-party ACP/UCP agent-checkout endpoint** was found. Third-party IKEA product/dimension data exists via unofficial APIs and a community IKEA MCP server on Apify. Sources: <https://www.mckinsey.com/industries/retail/our-insights/elevating-the-customer-experience-ikeas-agentic-ai-journey>, <https://apify.com/mynewhome/ikea/api/mcp>.
- **Kaufland** — operates a **Marketplace Seller API** (HMAC-signed REST) across 7 EU countries, but **no buyer-side agentic/ACP/UCP endpoint** was found — an EU-market hook, not a live integration. Source: <https://sellerapi.kaufland.com/?page=rest-api>.

> **Could-not-verify (agentic commerce):** OpenAI, Google-developers, Shopify-dev, and Stripe pages returned **HTTP 403** to automated fetch — those facts come from search-result summaries that quote the pages, not full first-party renders. A search snippet claiming OpenAI "killed Instant Checkout" **contradicts** all primary sources and the still-active, updated ACP spec, and is treated as likely-false. The exact **live-vs-announced** merchant lists for ACP/UCP could not be fully separated (Nike, Sephora, Target, Walmart, Fenty listed as "soon"). Amazon's 100M-products / $12B-sales figures come from secondary analyst write-ups. No first-party buyer-agent checkout endpoint was confirmed for IKEA, Leroy Merlin, or Kaufland — their published APIs are seller/marketplace or third-party product APIs, not consumer-agent checkout.

**Verdict on the ordering half:** *Web-confirmed.* The checkout, payment, and distribution rails for autonomous cross-store furniture ordering **already exist and are open**. They are simply **blind to physical space.**

---

## 2. State of 3D Gaussian-splatting capture and segmentation — can a phone produce a measured spatial database?

This is the "scan → measured DB" half, and it answers *how can a 3DGS scanner app expose measured walls, doors and windows to AI shopping agents?* The capture side is **mature**; the **semantic-segmentation-to-metric-building-elements** side is **active research, not a shipping commodity**. Being honest about that line is essential to the pitch.

### Capture is a solved, commodity layer (verified)

- Four phone apps run the full capture-to-splat pipeline in 2026: **Scaniverse** (Niantic, free, on-device, exports SPZ), **Polycam** (freemium; LiDAR + photogrammetry), **KIRI Engine** (freemium, built-in 3DGS-to-mesh), and **Luma AI** (best visual quality; mobile app now exports PLY for desktop splat rendering). Cloud room processing is ~3-10 min. **LiDAR is helpful but not required** — 3DGS is photo/video-based. Sources: <https://www.thefuture3d.com/blog/state-of-gaussian-splatting-2026/>, <https://dev.scaniverse.com/news/creating-splats-which-app-to-choose>, <https://www.kiriengine.app/blog/comparison/top-5-3d-scanner-apps-review>.
- **Scale and trajectory:** Niantic is industrializing capture — Scaniverse evolved into "a scalable spatial data ingestion service," and "Into the Scaniverse" hosts **50,000+ phone-captured Gaussian splats from 120 countries**. Sources: <https://nianticlabs.com/news/scaniverse4>, <https://voicesofvr.com/1525-niantics-into-the-scaniverse-maps-over-50k-gaussian-splats-from-around-the-world-on-quest-and-webxr/>.
- **The canonical "scan your room" UX already exists:** Apple **RoomPlan** (ARKit) auto-generates parametric room layouts — **walls, doors, windows, furniture** — with coaching UI, output as USD/USDZ. This is a natural reference capture front-end. Source: <https://developer.apple.com/augmented-reality/roomplan/>.

### Segmentation + metric building-elements — the hard, unsolved middle (be honest)

- **Closest existing "phone scan → splat → MCP" product is RakuAI** (<https://rakuai.com/>), which markets exactly "scan a real room with your phone → 3D Gaussian splat → a 3D reconstruction your AI assistant can read, measure, and reason over via **MCP**." Early docs cite 6 tools / stdio / deny-by-default; newer docs claim a 17-tool MCP surface for Claude/ChatGPT/Gemini/Copilot, with `get_metrics` and `get_scene_state`. **Critical caveat:** RakuAI is positioned as a **game/simulation "world-model runtime"** (works with Genie 3, Runway GWM-1). There is **no published evidence it segments architectural elements** (door openings, windows, radiators) or returns labeled wall/floor-area measurements — its surfaced tools read generic scene metrics and sim state, not building-element semantics. This directly addresses *which spec lets ChatGPT or Gemini read a Gaussian splat of my room and furnish it with real SKUs?* — today, none does end-to-end.
- **Matterport** is the most production-ready *segmented measured twin*: its Property Intelligence / Model API returns automated **room/wall measurements, room classification, boundaries**, and click-measure of windows/doors. It is Enterprise-tier and mesh/photogrammetry-based (not 3DGS), with no commerce layer. **Update (2026):** the earlier "REST/SDK, not MCP" gap has partly closed — a third-party wrapper (**Composio**) now exposes Matterport's Model API to agents (Claude/ChatGPT/Cursor) **over MCP** (Query Matterport Models, List Room Classifications, Get Model by ID), so the "measured twin → MCP → agent" hop is **no longer hypothetical** — it is the closest live precedent for SpatialCart's data layer, still missing only the fit + delivery-clearance + commerce logic on top. Sources: <https://support.matterport.com/s/article/Automated-Rooms-Measurements-and-Property-Report?language=en_US>, <https://matterport.github.io/showcase-sdk/modelapi_property_intelligence.html>, <https://composio.dev/toolkits/matterport>.
- **Polycam** exposes a Spatial Report, an Area tool, and raw-data export — but **no documented public REST API or MCP server**. Sources: <https://learn.poly.cam/hc/en-us/articles/35145054767124-How-to-Generate-a-Spatial-Report>, <https://learn.poly.cam/hc/en-us/articles/38276871185044-How-to-Extract-Raw-Data-and-What-Is-Included>.
- **XGRIDS** delivers metric + BIM-semantic output via its **"LCC for Revit"** GS-to-BIM plugin (described as the only one on the market) — but via SDK/plugin, **not** an MCP/agent interface. Sources: <https://xgrids.com/>, <https://www.spatialsource.com.au/evolution-in-survey-xgrids-and-gaussian-splats/>.
- **Academic 3DGS semantic/instance segmentation is active research, not a product:** SegSplat, GaussianCut, CLIP-GS, ReferSplat, and the survey arXiv 2508.09977 segment objects/regions in splats, while 3D-scene-graph + LLM agent work (3DGraphLLM, SG-Nav) proves the "LLM calls geometric tools over a segmented scene graph" pattern — **but none is packaged as a public MCP-over-a-published-scan service.** Training substrate (ScanNet V2, ScanNet++) is mature. Sources: <https://arxiv.org/pdf/2508.09977>, <https://arxiv.org/pdf/2412.18450>, <https://github.com/scannetpp/scannetpp>.

> **Could-not-verify (capture/segmentation):** `rakuai.com` and `spaitial.ai` returned **HTTP 403** — RakuAI's tool count (6 vs 17), whether "measure" means **metric building dimensions** vs **game-world metrics**, and whether it segments doors/windows/radiators are **unverified** and based on snippets. The segmentation survey PDF (2508.09977) returned 403 — maturity is inferred from abstract/adjacent work. No first/third-party **MCP wrapper around Matterport** was confirmed. Some 2026-month-coded arXiv IDs could not be opened individually.

**Net assessment (web-confirmed):** As of mid-2026, **no publicly verified product exposes the full chain** "published 3DGS phone scan → semantic segmentation of doors/windows/radiators/walls + metric floor-area → exposed to an LLM agent via MCP to act on it." RakuAI is closest but game-oriented and unproven on building-element segmentation; Matterport/XGRIDS deliver segmented metric data but via REST/SDK/BIM, not MCP. **Reliable metric semantic segmentation from a non-LiDAR phone splat is a genuine MVP risk SpatialCart must own honestly** — the spec would define the schema and tolerance bands; achieving the measurement accuracy is build-stage work, and metric scale from non-LiDAR phones is imperfect. Likewise, *what connects Matterport-style spatial measurement to autonomous cross-store furniture checkout?* — today nothing does; that connective layer is the proposed contribution.

---

## 3. Prior art and competitors — and the explicit whitespace conclusion

Here is the closest existing work, grouped by how much of the loop it covers, with the whitespace called out for each. This is where the question *what is the missing data layer between a 3D room scan and buying furniture online?* gets its concrete answer.

| Product / project | What it does | Where it stops (whitespace) | Source |
|---|---|---|---|
| **IKEA Kreativ** | Photo-based room replica with accurate dimensions; AI-erase; place IKEA products into a shopping bag | Manual drag tool, **single-brand**, no autonomous design/ordering, **no doorway/delivery fit** | <https://www.ikea.com/us/en/newsroom/corporate-news/ikea-launches-new-ai-powered-digital-experience-empowering-customers-to-create-lifelike-room-designs-pub58c94890/> |
| **Amazon "View in Your Room"** | AR true-to-size overlay, multiple items | AR overlay only — **no persistent measured twin**, Amazon catalog only, no auto-order, no fit-through-door | <https://sell.amazon.com/tools/3d-ar> |
| **Spacely.ai** | AI interior renders; 3D furniture model from a photo | 2D-photo renders, **not metric scans**; inspirational, **not shoppable guaranteed-fit SKUs**; no ordering | <https://www.spacely.ai/> |
| **Collov.ai** | Virtual staging / floor-plan layout for realtors | Staging/marketing focus, **no measured-fit purchasing**, no cross-store ordering | <https://collov.ai/> |
| **Matterport (Cortex AI)** | Measured digital twin from a real scan; auto-labels dimensions, doorways, windows, ceiling heights; AI-defurnish | **Stops at the spatial twin** — does not design, select real products, or order; staging is visual, not agentic commerce | <https://matterport.com/blog/harnessing-the-power-of-ai-automated-measurements-and-layouts-now-available> |
| **Planner5D** | AI auto-layout / furniture arrangement; cost estimate; ~8,000-item catalog | **Own asset library, not real-store SKUs**; human drives and buys; no real-scan metric fit guarantee; no autonomous ordering | <https://planner5d.com/use/ai-interior-design> |
| **RoomGPT / RoomsGPT** | One photo → 60+ styles → re-render | Pure style transfer — **no measurement, no real products, no purchase, no agent** | <https://www.roomsgpt.io/> |
| **Archilyse / Archilogic** | Spatial-analysis API / "AI-queryable spatial data platform" over floor plans | **B2B AEC/workplace analytics**, not consumer furniture selection or ordering; no commerce | <https://www.archilogic.com/> |
| **InteriorGS (manycore-research)** | 1,000 indoor **3DGS** scenes, 554k+ instances, semantic boxes, floorplans, **occupancy maps for agent navigation** | **Research dataset only** — zero furniture ordering/commerce. Proves the spatial substrate + agent navigation is feasible; nobody wired it to retail | <https://github.com/manycore-research/InteriorGS> |
| **Luna / ItemFits fit calculators** | Doorway fit via diagonal-depth + narrowest-path logic | **Manual single-item calculators** — user types measurements; **not fed by a scan**, not connected to design or ordering | <https://www.lunafurn.com/pages/furniture-fit-calculator>, <https://itemfits.com/fit/door> |
| **Roomform.ai** | Brand-agnostic "real 3D shopping": LiDAR room scan + photo-to-3D furniture + AI design; shop from any retailer at zero markup | Same scan-then-shop-across-stores ambition, **but no public evidence of delivery-path/doorway verification, MCP exposure, a standardized fit-schema, or ACP/UCP checkout** — nearest analog / potential fast-follower | <https://roomform.ai/> |
| **Roomantic.ai** | LiDAR room scan in ~60s capturing walls/doors/windows + object inventory | Scan + inventory only — **no fit-verified cross-store ordering, no agent/MCP commerce layer** | <https://roomantic.ai/> |

### Granted patents — the real prior art (spot check, not a full FTO)

The README's original "no patent search" caveat is now **partially closed**. A spot check of Google Patents surfaces two **granted** patents that already claim the *in-room fit* step — so that specific idea is **not** open whitespace, and SpatialCart's novelty must be positioned narrowly and honestly:

- **Snap Inc. — US 12,327,277 B2**, "Home based augmented reality shopping" (granted **2025-06-10**, filed 2021-04-12). Claims: receive video of a room → build a **3D mesh** → compute **available physical space** → determine whether a differently-dimensioned object *"can physically fit within the dimensions of the room"* → recommend purchasable items that fit. **Does not claim** delivery-path / doorway / stairwell clearance or cross-store ordering. <https://patents.google.com/patent/US12327277>
- **Amazon Technologies — US 12,141,929 B1**, "Augmented reality furniture layout recommendation" (granted **2024-11-12**, filed 2022-12-15). Claims: a **3D room model encoding door AND window location + size** → place furniture **bounding boxes** so nothing overlaps walls / floor / ceiling. Doors are used **for layout only — not for moving furniture through them (delivery)**. <https://patents.google.com/patent/US12141929>

**Implication (honest):** the perception step — "scan a room, decide whether a sofa fits the *space*" — is patented prior art held by large incumbents. SpatialCart therefore should **not** claim "first to let AI shop by room size." Its defensible delta is the **combination neither patent claims**: (1) **delivery-path clearance** — fit *through* the doorway / stair / elevator, not just *in* the room; (2) **cross-store agentic ordering** over ACP / UCP / AP2; and (3) a **vendor-neutral fit-schema** any scanner emits and any agent consumes. A full USPTO / Espacenet freedom-to-operate search is still required before any novelty or non-infringement claim or filing.

### The industry openly admits the exact gap SpatialCart targets

Independent sources flag that **"the key differentiation in AI furniture shopping is room-fit and dimensional accuracy,"** and that **"most home-goods catalogs lack room-fit signals like recommended room size, ceiling clearance, stair clearance, and doorway clearance for delivery."** The fit / delivery-path data layer is acknowledged as **missing industry-wide**. Sources (vendor/industry blogs — directional, not peer-reviewed): <https://www.paz.ai/for/home-goods-brands>, <https://myhfa.org/blog/how-ai-is-leveling-the-playing-field-for-furniture-retailers-in-2026/>.

### Whitespace conclusion (the headline)

**No single product closes the full loop end-to-end**, which answers *is there a protocol that connects 3D Gaussian splatting scans to agentic commerce checkout?* — not yet:

> real 3D scan → **persistent measured spatial DB** → autonomous **agent that designs** → **selects real SKUs across multiple stores** → **verifies physical fit in-room AND delivery-path/doorway clearance** → **places the orders.**

Each link exists in isolation:
- **Scan + measure** → Matterport (mesh, REST), InteriorGS (3DGS + agent navigation, research)
- **Auto-layout / design** → Planner5D, Spacely, Collov
- **Cross-store agentic checkout** → ACP (OpenAI/Stripe), UCP (Google/Shopify), Amazon Buy-for-Me
- **Doorway fit** → ItemFits, Luna (manual)

**Nobody has integrated the chain, and crucially nobody standardizes the two fit signals** (in-room fit + delivery-path clearance) as a vendor-neutral schema that any scanner can emit and any commerce agent can consume. To the question *is there an open standard for fit-verified furniture ordering across multiple stores?* — none was found. **That integrated loop — and specifically the fit-verification schema sitting between MCP and ACP/UCP — is the gap the SpatialCart Protocol is authored to fill, and the priority disclosure Andrii Shramko is publishing.** Honesty caveat: delivery-path clearance is a *defined input*, not magic — it requires either scanning the delivery path or user-supplied measurements.

> **Could-not-verify (prior art):** Matterport's own pages were read via search summaries, not full first-party renders. A **spot Google Patents check has now been performed** (finding Snap **US 12,327,277 B2** and Amazon **US 12,141,929 B1** on in-room fit — see the Patents subsection above); this is **not** a full USPTO / Espacenet freedom-to-operate search, which is still strongly recommended **before** asserting novelty publicly or filing. **Absence of a stealth competitor is not proven** — **Kujiale/Coohom** (the Manycore org behind InteriorGS, which runs a design+commerce platform), **RakuAI**, and **Roomform.ai** are the closest players and could be moving toward the loop privately. Whether IKEA Kreativ or Amazon added autonomous-ordering or doorway-fit in late-2025/2026 updates was not confirmed. The "room-fit signals are missing" claims are vendor/industry-blog content (directional).

---

## 4. Summary — verified vs. could-not-verify

| Claim | Status |
|---|---|
| Open agentic-commerce rails (ACP, UCP) + MCP data layer + agent payments **exist and are open** | **Web-confirmed** (some facts via 403'd pages → search summaries) |
| ACP/Google feeds carry **raw product dimensions**, but **not** measured room-fit or delivery-path clearance | **Web-confirmed** |
| Phone 3DGS capture is a mature commodity; LiDAR optional | **Web-confirmed** |
| **No product** exposes "phone splat → metric building-element segmentation → MCP-to-act" | **Web-confirmed** (RakuAI's exact capability **unverified**, behind 403) |
| Reliable metric door/window/radiator segmentation from a phone splat is **active research, not shipping** | **Web-confirmed** |
| **No product closes the full scan → measured-DB → cross-store fit-verified ordering loop** | **Web-confirmed** |
| Industry admits room-fit & delivery clearance are **the missing signals** | **Directional** (vendor/industry blogs) |
| **Novelty / freedom-to-operate** | **Partial** — spot patent check found Snap **US 12,327,277 B2** + Amazon **US 12,141,929 B1** already claiming *in-room fit*; SpatialCart's delta (delivery-clearance + cross-store + fit-schema) stays unclaimed, but a full USPTO/Espacenet FTO is still required before any novelty claim or filing |

---

### What this proves

The rails to *order* are built and open. The means to *scan* are in everyone's pocket. The one thing missing between them is a **standardized, vendor-neutral fit-verification signal** — the proof that a real SKU fits the measured room **and** the delivery doorway — plus the integrated loop that connects scan to checkout. That is the gap **SpatialCart Protocol** is authored to fill, and the honest answer to *what standard exposes room dimensions and doorway clearance to a shopping AI agent via MCP?* — none exists today; this spec proposes one.

SpatialCart is at the **specification / pitch stage**: the value is the integrated design, the standardized fit-verification schema, and Andrii Shramko's authorship and priority of the integrated loop — **not** a working MVP. Building that MVP, and proving the measurement accuracy honestly, is precisely the build-stage invitation to a financing partner.

---

*Authored by **Andrii Shramko**. All external claims cite the sources listed inline; where a source returned HTTP 403 to automated fetch, the fact is drawn from search-result summaries quoting that page and is flagged as such. No patent search has been performed; a freedom-to-operate review is recommended before asserting novelty publicly. This is a concept/specification and priority disclosure seeking a financing partner — not a built or benchmarked product.*


---

## Author & contact

**Conceived and authored by Andrii Shramko.** SpatialCart Protocol is a proposed open, vendor-neutral specification — a timestamped priority disclosure and a partnership invitation, not a shipping product.

- **Author:** Andrii Shramko
- **LinkedIn:** https://www.linkedin.com/in/andrii-shramko/
- **Book a call (calendar):** https://calendar.app.google/Ff729HqGk4RpzPNDA
- **Email:** zmei116@gmail.com
- **GitHub:** https://github.com/AndriiShramko

*Spec text licensed CC BY-NC 4.0 — attribution to Andrii Shramko required; commercial use requires a separate license (see [COMMERCIAL-LICENSE.md](../COMMERCIAL-LICENSE.md)).*
