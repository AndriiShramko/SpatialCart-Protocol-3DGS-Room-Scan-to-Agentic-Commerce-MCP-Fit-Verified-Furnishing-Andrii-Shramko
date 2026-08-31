# SpatialCart Protocol — An Open Specification for Turning a Phone 3D Gaussian-Splatting Room Scan into a Measured Spatial Database that AI Agents Read via MCP to Design, Fit-Check, and Order Real Furniture Across Stores (Authored by Andrii Shramko)

> A proposed open protocol + data-layer spec that aims to bridge phone-captured 3D Gaussian-splat room scans to agentic commerce — so that an AI agent could measure your real space, verify that each real SKU fits the room **and** the delivery doorway, and place the order across multiple stores via MCP / ACP / UCP. **This is a concept and specification seeking a financing/build partner — there is no working code yet.**

---

## In one breath

Imagine you point your phone around a room, the way you already can with apps like Scaniverse or Polycam, and a few minutes later there is a 3D Gaussian-splat copy of that space. Now imagine you simply tell your AI agent: *"furnish this living room, budget X, Scandinavian, keep the walkway clear"* — and the agent **reads your scanned room as measured data**, lays out a design, and then checks every real product against your actual walls, floor area, and ceiling before it buys anything.

SpatialCart Protocol is a proposal for the missing data layer that *would* make that possible: a specification for how a room scan becomes a measurable spatial database an agent can query over **MCP**, and how that agent could confirm a sofa fits the floor space **and** fits through your front door before placing the order across multiple stores via agentic-commerce rails like **ACP** and **UCP**.

The whole point is **fit verification** — the two signals the furniture industry openly admits are missing. Will this washing machine actually clear the bathroom doorway and the turn in the hallway? Does enough decking exist to cover the measured terrace, and will the boxes fit up the stairwell? Today an AI agent shopping for you is blind to physical space. SpatialCart is a blueprint for giving it eyes and a tape measure.

## В двух словах

Вы сканируете комнату телефоном — получается её точная 3D-копия (Gaussian splatting). SpatialCart Protocol — это предлагаемая открытая спецификация, которая призвана превратить такой скан в **измеримую базу данных о пространстве**, доступную ИИ-агенту через MCP. По задумке агент сам обмеряет комнату, проектирует интерьер, проверяет, что каждый реальный товар **влезает и в комнату, и в дверной проём при доставке**, и оформляет заказ сразу в нескольких магазинах через протоколы агентной коммерции (ACP, UCP). Это концепция и спецификация авторства Andrii Shramko — **рабочего кода пока нет**, ищется партнёр для финансирования MVP.

---

## The problem & the shift: from human-browsed sites to agent-operated services

The web is quietly changing who it serves. For thirty years pages were built for **humans to browse**; now a fast-growing layer of the web is being rebuilt for **AI agents to operate** — ChatGPT Instant Checkout, Google AI Mode, Amazon's "Buy for Me," Perplexity's agentic shopping. Commerce is being re-plumbed around protocols designed for software, not eyeballs: the **Agentic Commerce Protocol (ACP)** from OpenAI and Stripe ([github.com/agentic-commerce-protocol](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol), [openai.com](https://openai.com/index/buy-it-in-chatgpt/)), the **Universal Commerce Protocol (UCP)** from Google and Shopify ([developers.googleblog.com](https://developers.googleblog.com/under-the-hood-universal-commerce-protocol-ucp/)), and the **Model Context Protocol (MCP)** underneath both as the agent-to-data layer.

This is no longer theory. In June 2026 **InPost** — Poland's largest parcel-locker / last-mile carrier — began rolling out an in-app AI shopping assistant ("Von Halski") to a ~17-million-user base, wiring **stores + an AI agent + its own delivery** into a single "just say what you want" flow across ~4,000 merchants ([bloomberg.com](https://www.bloomberg.com/news/articles/2026-03-16/inpost-readies-ai-shopping-assistant-to-take-on-marketplaces), [xyz.pl](https://xyz.pl/poland-unpacked/inpost-launches-ai-powered-shopping-assistant-in-mobile-app-3213/)), while **Allegro** (Poland's largest marketplace) partnered with OpenAI for its own assistant ([ecommercenews.eu](https://ecommercenews.eu/allegro-enters-partnership-with-openai/)). The trajectory is clear: more such store ↔ agent ↔ delivery links, converging toward **one agent you can simply tell "just do it"** — and have it done right, end to end, without follow-up questions.

But there is a blind spot. These agents can read a catalog, negotiate a cart, and tokenize a payment — yet they have **no idea what your room looks like or how big it is**. They cannot tell whether the bed fits the alcove, whether the wardrobe clears the ceiling, or whether the fridge survives the turn at the top of the stairs. Industry sources flag the same gap: *room-fit and dimensional accuracy is described as a key differentiation in AI furniture shopping*, and most catalogs are said to **lack room-size, ceiling, stair, and doorway-clearance signals** ([paz.ai](https://www.paz.ai/for/home-goods-brands), [myhfa.org](https://myhfa.org/blog/how-ai-is-leveling-the-playing-field-for-furniture-retailers-in-2026/)). *(These are vendor/industry-blog claims, directional rather than peer-reviewed — but they point at exactly this gap.)*

At the same time, a **3D scan is becoming a new context layer** — something you capture once and agents could reuse silently, again and again, the way an agent reuses your calendar or your email. Phone-based 3D Gaussian splatting is described as the fastest-growing 3D reconstruction method of 2025–2026, moving capture from expensive scanners onto ordinary smartphones ([thefuture3d.com](https://www.thefuture3d.com/blog/state-of-gaussian-splatting-2026/), [kiriengine.app](https://www.kiriengine.app/blog/3DGSvsPhotogrammetryvsLiDAR)). Niantic's *Into the Scaniverse* already hosts 50,000+ phone-captured splats from 120 countries ([nianticlabs.com](https://nianticlabs.com/news/scaniverse4)). The scan exists. The commerce rails exist. **Based on current public research, nobody has wired the two together with a fit-verification layer in between.** That bridge is what SpatialCart Protocol proposes.

---

## Why now — the physical-AI tailwind

The timing isn't accidental. The most influential voices in AI have converged on one message: the next frontier is **understanding physical space**. Jensen Huang (NVIDIA) calls it *"physical AI… AI is now beginning to understand the laws of physics"* ([CES 2025](https://blogs.nvidia.com/blog/ces-2025-jensen-huang/)); Fei-Fei Li argues that spatially intelligent AI needs *"world models,"* and her World Labs' *Marble* model already **exports 3D Gaussian splats** ([worldlabs.ai](https://www.worldlabs.ai/)). A scanned room is the consumer on-ramp to exactly that spatial context.

The plumbing arrived just as fast. **MCP went from an Anthropic-only launch (Nov 2024) to an industry standard in ~4 months** — adopted by OpenAI (Mar 2025; *"people love MCP"* — Sam Altman) and Google DeepMind (Apr 2025; *"rapidly becoming an open standard for the AI agentic era"* — Demis Hassabis), embedded into Windows by Microsoft, and **donated to the Linux Foundation in Dec 2025** (~10,000+ public servers by 2026). And the demand is real, not hypothetical: Adobe measured **generative-AI retail traffic up ~693% YoY** over the 2025 holidays, with AI-referred shoppers converting 31% higher ([Adobe](https://business.adobe.com/blog/generative-ai-powered-shopping-rises-with-traffic-to-retail-sites)); Salesforce found AI **influenced ~20% of all global orders (~$67B)** in a single Cyber Week ([Salesforce](https://www.salesforce.com/news/press-releases/2025/12/05/cyber-week-ai-agents-sales/)); McKinsey projects agentic commerce could orchestrate **up to $3–5 trillion** globally by 2030 ([McKinsey](https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-agentic-commerce-opportunity-how-ai-agents-are-ushering-in-a-new-era-for-consumers-and-merchants)).

The closest **live precedent** to SpatialCart is already running: a third-party MCP wrapper ([Composio](https://composio.dev/toolkits/matterport)) now exposes **Matterport's measured 3D scenes to AI agents over MCP** (query models, list room classifications), and Matterport's Cortex AI auto-computes floor area and ceiling height. That proves the "room-as-agent-data" pattern works — and pinpoints SpatialCart's delta: the **fit + delivery-clearance layer** on top, plus cross-store ordering, standardized as a schema.

---

## How it would work — the loop

SpatialCart describes a clean, vendor-neutral loop. It is designed to **not** replace your scanner or your checkout protocol; it standardizes the data that flows between them. (Every step below is a design intent for the spec, not shipped behavior.)

1. **Capture** — A user scans the room with a phone (Scaniverse, Polycam, KIRI, Luma) or a pro device (Matterport, XGRIDS), or via a guided flow like Apple RoomPlan. LiDAR helps but is not required for 3DGS capture.
2. **Publish** — The scan is published as a spatial asset the user controls and an agent can be granted access to.
3. **Segment into a spatial database** — A processing service turns the raw splat into a *measured* scene: floor area, wall lengths, ceiling height, and segmented architectural elements — **doors, windows, radiators, openings** — with metric dimensions and tolerance bands. *(Honest note: reliable semantic segmentation of these elements from a phone scan is active research, not a shipping commodity — see Status.)*
4. **Expose via MCP** — An **MCP server** exposes that measurable scene to AI agents: query room geometry, get element dimensions, request the delivery-path profile.
5. **Agent measures + checks fit** — The agent reads the real dimensions and verifies **two** things the industry says are missing: **in-room fit** (does the piece fit the measured floor/wall space and ceiling) and **delivery-path clearance** (does it fit through the doorway, stairwell, or elevator, using diagonal-depth and narrowest-path logic). *(The delivery path must be scanned or entered — it's a defined input, not magic.)*
6. **Query stores via agentic-commerce protocols** — The agent reconciles fit-verified requirements against real SKUs and dimensional feeds across multiple stores through **ACP / UCP**, riding the existing **MCP** data layer.
7. **Present a finished design** — The agent shows the user a complete, fit-checked design with real, in-stock products and prices.
8. **User confirms** — The human stays in control and approves.
9. **Agent orders** — With confirmation, the agent places the orders across stores and arranges delivery and installation through the same agentic-commerce rails.

SpatialCart aims to standardize the schema at every hop: **room geometry + segmented architectural elements + per-SKU dimensional fit envelope + delivery-path constraints.** It is designed to sit explicitly **between MCP (data) and ACP/UCP (orchestration/checkout)** — complementary, never competing.

**Closing the loop (draft extension):** the same pre-scan can also serve as a *visual localization map*, so before confirming, the user's phone relocalizes in the scan's coordinate frame — **no GPS** — and shows the chosen product in AR at exactly the pose the agent planned. *Plan in the scan, see it in the room.* See **[docs/AR-PREVIEW-LOOP.md](docs/AR-PREVIEW-LOOP.md)**.

---

## Who this is for — and why contact Andrii

### For retailers and marketplaces

Furniture's biggest hidden cost is **fit-failure returns** — sofas that don't fit the room, appliances that won't clear the door. The scale is concrete: industry data puts **size/space mismatch as the single largest cause of furniture returns (~58%)** — ahead of colour/material (~44%) or transit damage (~31%) — and "wrong size" is the #1 reason for online returns overall; US retail returns reached **~$890B in 2024 (16.9% of sales)**, and a single large-item return costs a retailer **~$55–90+** and can erase **50–100% of that item's gross margin** ([First Chair](https://www.firstchair.app/blog/furniture-mismatch-cost-statistics); [NRF + Happy Returns](https://nrf.com/media-center/press-releases/consumer-returns-retail-industry-total-890-billion-2024); [EightX](https://www.eightx.co/)). SpatialCart is designed to confirm fit **before** the agent places the order, with the goal of cutting returns and lifting agentic-checkout conversion on exactly the high-intent, large-format goods where fit is a top return driver. It is meant to plug into the **UCP/ACP** rails you're already adopting — Wayfair (a UCP co-developer), Ashley Furniture (live on Stripe's Agentic Commerce Suite), The Home Depot, Best Buy, Target, and Walmart are already in that ecosystem ([blog.google](https://blog.google/products/ads-commerce/agentic-commerce-ai-tools-protocol-retailers-platforms/), [stripe.com](https://stripe.com/newsroom/news/stripe-openai-instant-checkout)) — so you would add the **one signal the industry says is missing** rather than swap out your stack. Agentic commerce is here; today it is blind to physical space. The opportunity Andrii is pitching: whoever helps build the **fit-verified spatial-commerce layer** for furniture is positioned for the lowest-return, highest-intent segment. Andrii has authored the spec — the ask is to finance the MVP and help co-own the standard.

### For 3DGS-software and scanner makers

Right now your scans end at a pretty picture or a mesh. SpatialCart proposes turning every scan into a **monetizable, agent-shoppable spatial database**: the moment an AI agent can read, measure, and **order** against your output, your free scanner becomes a commerce funnel. The pitch is to be the first scanner whose output is **natively fit-verified and shoppable across stores**. And a defensive note — RakuAI markets a "phone scan → splat → MCP" product today (though its published tools read as game/simulation metrics, with no documented building-element segmentation), and Kujiale/Coohom (the team behind the InteriorGS 3DGS dataset) operates a design-plus-commerce platform; both are circling this space. Adopting an **open, neutral, independently authored** fit-verification standard could let you lead the category instead of being locked into a rival's walled garden. Whether you're Scaniverse/Niantic, Polycam, Matterport, XGRIDS, KIRI, Luma, or building on Apple RoomPlan — there is a clear hook to expose a fit-verification feed.

---

## Why adoption pulls — the network effect

A standard wins by gravity, not elegance, and SpatialCart is designed so the gravity comes from **demand-pull, not solo evangelism**. The hard, novel part — turning a raw scan into a measured, agent-queryable, fit-verification signal — is meant to be solved **once and in the open**. Everything else is a thin adapter each party writes on their own side:

- Once an **open, agent-readable Spatial MCP for scans** exists, a furniture or building retailer that sees agents can already *measure a customer's real room* has a direct incentive to have **its own developers write a small store-side MCP/feed** so their SKUs become fit-matchable — otherwise a fit-aware competitor wins that sale.
- Scanner and 3DGS-software makers have the mirror incentive: emit the schema so their scans plug into the buying side.
- Carrier- and marketplace-led agents already racing to fulfill "just order it" (InPost's Von Halski, Allegro × OpenAI) make **spatial fit a competitive necessity** for physical goods — pulling integrations in from both ends.

In other words: publish the missing middle as an open standard, and the two sides have every reason to wire themselves to it. That is the adoption thesis — and the reason this is published openly under Andrii Shramko's authorship rather than kept on a shelf.

---

## Status — honest and explicit

This is a **concept + protocol specification + pitch**, authored by Andrii Shramko. **There is no working code, no MCP server, and no benchmarked accuracy yet — the MVP is not built.** The value today is the integrated design, the standardized fit-verification schema, and Andrii's authorship and timestamped priority over the full closed loop.

Be clear about what is unproven:

- **Segmentation is research, not a commodity.** Reliably detecting doors/windows/radiators and computing true metric floor area from a phone 3DGS scan is active research (e.g. SegSplat, GaussianCut, 3DGraphLLM; InteriorGS provides a 3DGS dataset with occupancy maps). Metric scale from non-LiDAR phones is imperfect. The spec defines the schema and tolerance bands; **achieving the accuracy is the central MVP risk.**
- **Fit verification needs real inputs.** Delivery-path clearance requires the user to scan or enter the path — it's a defined input, not magic. "Guaranteed fit" is a goal bounded by stated tolerances, not a promise the current concept can make.
- **Novelty is narrow — and two granted patents already touch in-room fit.** No product was found closing the *full* loop, but a spot patent check surfaced real prior art on the "does it fit the room" step: **Snap's US 12,327,277 B2** ("Home based augmented reality shopping," granted Jun 2025) claims scanning a room to a 3D mesh and determining whether an object *"can physically fit within the dimensions of the room,"* and **Amazon's US 12,141,929 B1** ("Augmented reality furniture layout recommendation," granted Nov 2024) encodes door/window location + size to place furniture without wall overlap. **Neither claims delivery-path clearance (moving the piece through the doorway / stair / elevator), cross-store agentic ordering, or a vendor-neutral fit-schema** — which is precisely SpatialCart's narrow, defensible delta. This is a spot check, **not** a full USPTO / Espacenet / Google Patents freedom-to-operate search; do one before any public novelty claim or patent filing. (Kujiale/Coohom, RakuAI, and Roomform.ai are the closest active players and could be moving privately.)
- **Competitive claims are directional.** Several details about RakuAI, live-vs-announced ACP/UCP merchant lists, and IKEA's agent feeds came from search snippets rather than first-party reads — re-verify before relying on them in a pitch.
- **It complements ACP/UCP — it does not compete with them.** SpatialCart depends on those rails; it is not a rival checkout protocol.

(See the spec and whitespace docs in this repo for the full evidence trail and source list.)

---

## Related project — the execution leg: Agentic Shopping Skills

SpatialCart gives an AI agent *eyes and a tape measure* — a measured room it can query before buying. The same author's sibling project, **[Agentic Shopping Skills](https://github.com/AndriiShramko/Agentic-Shopping-Skills-AI-Agents-Buy-and-Pay-Online-Autonomously-Allegro-OLX-Andrii-Shramko)**, gives it *hands and a wallet*: an open skill layer so user-owned agents (Claude Code, Codex) can search, buy, and **pay autonomously** on real online stores — per-site skills, mandate-based delegated payments with hard limits, and a community skill registry, starting with Allegro.pl and OLX.pl. Together they close the full loop: *"furnish this room, budget X"* → measured, fit-checked, bought, delivered.

---

## Get involved — partner, finance, or license

Andrii is **passionate about building this** and is looking for one scanner vendor and one retailer to co-pilot the standard, plus a financing partner for the MVP. If you make 3DGS/scanner software, run a furniture retailer or marketplace, or want to back the spatial-commerce layer, here's how to reach the author.

**Author / contact:**

- **Author:** Andrii Shramko
- **LinkedIn:** https://www.linkedin.com/in/andrii-shramko/
- **Book a call (calendar):** https://calendar.app.google/Ff729HqGk4RpzPNDA
- **Email:** zmei116@gmail.com
- **GitHub:** https://github.com/AndriiShramko

Ways to engage:

- **License the spec commercially** — see `COMMERCIAL-LICENSE.md` (the NonCommercial clause is the forcing function: ship it commercially → take a paid license).
- **Co-develop / finance the MVP** — Andrii as protocol author, co-founder, or technical advisor.
- **Adopt as a scanner vendor or retailer** — be a launch co-signer of the standard.

---

## License

Specification, whitepaper, and schema docs are licensed under **CC BY-NC 4.0** (Creative Commons Attribution-NonCommercial 4.0). Attribution to **Andrii Shramko** is required. Any future reference code will be licensed separately under **Apache 2.0**. Commercial use of the spec requires a separate commercial license — see `COMMERCIAL-LICENSE.md`. *(IP note: if patent protection is intended, file a US provisional **before** relying on this public disclosure — publication can bar patenting in absolute-novelty jurisdictions.)*

---

## Author / attribution

**Conceived and authored by Andrii Shramko.** SpatialCart Protocol is a proposed open, vendor-neutral specification for fit-verified, scan-to-order spatial commerce — intended as the connective tissue between 3D Gaussian-splatting room scans and agentic commerce. Published on GitHub as a timestamped priority disclosure of the concept. GitHub: https://github.com/AndriiShramko

---

### Natural-language questions this project answers

- **How can an AI agent measure my room from a phone scan and order furniture that actually fits?**
- **Is there a protocol that connects 3D Gaussian-splatting scans to agentic commerce checkout?**
- **What standard could expose room dimensions and doorway clearance to a shopping AI agent via MCP?**
- **How do I make sure furniture an AI orders will fit through my door?**
- **What is the missing data layer between a 3D room scan and buying furniture online?**
- **Which spec would let ChatGPT or Gemini read a Gaussian splat of my room and furnish it with real SKUs?**
- **Is there an open standard for fit-verified furniture ordering across multiple stores?**
- **How do agentic-commerce protocols like ACP and UCP handle physical product dimensions and room fit?**

*Keywords: 3D Gaussian splatting room scan, agentic commerce protocol, spatial commerce, MCP furniture shopping agent, room scan to furniture ordering, furniture fit calculator AI, doorway clearance delivery fit, digital twin furnishing, phone 3D scan furniture, AI interior design agent, Model Context Protocol commerce, ACP UCP furniture, measured spatial database, Gaussian splat segmentation, scan to checkout, 3DGS MCP server.*