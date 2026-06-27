# SpatialCart Protocol — The Concept

> **An open specification for turning a phone 3D Gaussian-splatting room scan into a measured spatial database that AI agents read via MCP to design, fit-check, and order real furniture across stores.**

*Authored by Andrii Shramko.*

---

## Status, in plain words

This document describes a **concept and a protocol specification at the design stage**. There is **no working MVP, no shipped code, and no benchmarked accuracy** behind it yet. Nothing here has been built or measured. What exists is an integrated *design*: a vision, a value chain, and a standardized fit-verification schema that wires together two technology stacks that already exist independently but have never been connected end-to-end.

I am writing this honestly because the value here is the *integration* and the *priority of the idea*, not a finished product. Where something is an assumption or an open research problem, this document says so explicitly. I am passionate about this, and I am looking for a partner — a scanner-software maker, a retailer, or a marketplace — to finance and build the MVP with me.

---

## The one-sentence vision

Your phone captures your real room once. That scan is intended to become a **persistent, measured spatial database** that lives in your context base. From then on, the goal is that any AI agent — ChatGPT, Gemini, Claude, Copilot — can **read your real space, design for it, verify that each real product fits both the room and the doorway it has to come through, and place the order across multiple stores**, without ever asking you to measure anything again.

That bridge — between a captured 3D scan and agent-operated commerce — is the missing layer. **SpatialCart Protocol** is proposed as the open spec for that layer.

---

## How can an AI agent measure my room from a phone scan and order furniture that actually fits?

That natural-language question is the one SpatialCart is designed to answer, and it is worth stating plainly because it is exactly the kind of question people are starting to ask LLMs. Today there is no single standard that connects a **3D Gaussian splatting room scan** to **agentic commerce** checkout. The pieces — phone room-scanning, spatial measurement, an **MCP furniture shopping agent**, and cross-store ordering — exist in separate silos. SpatialCart proposes the **measured spatial database** and the fit-verification schema that would let them talk.

---

## Why agent-operated commerce is coming (and is already here)

This part is not speculation about the future. The commerce rails for autonomous agents shipped in 2025–2026:

- **ACP (Agentic Commerce Protocol)** — co-developed by OpenAI + Stripe, launched alongside ChatGPT "Instant Checkout" on 2025-09-29, Apache 2.0 licensed, with a published spec that now includes cart, feed, orders, authentication, and MCP integration ([github.com/agentic-commerce-protocol](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol), [openai.com/index/buy-it-in-chatgpt](https://openai.com/index/buy-it-in-chatgpt/)).
- **UCP (Universal Commerce Protocol)** — co-developed by Google + Shopify, announced at NRF 2026 (2026-01-11), endorsed by 20+ players including Visa, Mastercard, Amex, Wayfair, Target, Walmart, The Home Depot, Best Buy ([developers.googleblog.com/under-the-hood-universal-commerce-protocol-ucp](https://developers.googleblog.com/under-the-hood-universal-commerce-protocol-ucp/)).
- **MCP (Model Context Protocol)** — built by Anthropic, donated to the Linux Foundation in December 2025, backed by OpenAI, Google, Microsoft, AWS, Block. It is the de-facto agent-to-tool **data layer** sitting underneath both ACP and UCP.
- **Payment rails** — Visa "Intelligent Commerce Connect" (one integration spanning four agent protocols), Mastercard "Agent Pay," and Stripe's Shared Payment Token make it possible for an agent to pay on your behalf with scoped, merchant-specific credentials ([corporate.visa.com](https://corporate.visa.com/en/sites/visa-perspectives/newsroom/visa-partners-complete-secure-agentic-transactions.html), [digitalcommerce360.com](https://www.digitalcommerce360.com/2025/10/16/visa-mastercard-both-launch-agentic-ai-payments-tools/)).
- **Walled-garden variants** — Amazon's "Buy for Me" / "Alexa for Shopping," reported by secondary coverage to reach 100M+ products from 400,000+ merchants ([aboutamazon.com](https://www.aboutamazon.com/news/retail/alexa-for-shopping-ai-assistant)).

The conclusion is simple: **autonomous cross-store checkout is now technically buildable.** Agents can find products, negotiate carts, and pay. The whole industry is racing to let an AI buy things for you.

**But every one of these rails is blind to physical space.** They know a sofa's price, its color, and — increasingly — its length/width/height (ACP's product-feed spec supports optional dimensions, [developers.openai.com/commerce/product-feeds/spec](https://developers.openai.com/commerce/product-feeds/spec?fields=required)). What they do *not* know is **your** space: how wide your living-room wall really is, whether the sofa clears your hallway turn, whether the fridge fits through the kitchen doorway once you account for the handle and the wall faucet sticking out. That **doorway clearance delivery fit** gap is what SpatialCart is designed to fill.

---

## Why a published 3D scan is the missing spatial-context layer

AI agents are getting good at reasoning over *documents* and *APIs*. They are still mostly blind to the *physical environment* you actually live in. This is the heart of **spatial commerce**: giving a shopping agent **room-fit dimensional data** about the real room, not a generic catalog.

Phone-based **3D Gaussian Splatting (3DGS)** changed the capture side. In 2025–2026 it became, by multiple accounts, the fastest-growing 3D reconstruction method, moving room capture off expensive dedicated scanners and onto ordinary smartphones:

- **Scaniverse** (Niantic, free, on-device, exports SPZ), **Polycam**, **KIRI Engine**, and **Luma AI** all run the full capture-to-splat pipeline; a room reportedly processes in roughly 3–10 minutes ([thefuture3d.com](https://www.thefuture3d.com/blog/state-of-gaussian-splatting-2026/), [dev.scaniverse.com](https://dev.scaniverse.com/news/creating-splats-which-app-to-choose)).
- **LiDAR is increasingly optional** — Scaniverse, Luma, and KIRI work on non-LiDAR phones via photogrammetry; LiDAR mainly helps with metric scale and textureless white walls ([kiriengine.app](https://www.kiriengine.app/blog/3DGSvsPhotogrammetryvsLiDAR)).
- **Apple RoomPlan** already ships the canonical guided "scan your room" UX, outputting parametric walls, doors, windows, and furniture as USD ([developer.apple.com/augmented-reality/roomplan](https://developer.apple.com/augmented-reality/roomplan/)).
- Niantic is industrializing this: Scaniverse is evolving from a consumer app into "a scalable spatial data ingestion service," and "Into the Scaniverse" already hosts 50,000+ phone-captured splats from 120 countries ([nianticlabs.com/news/scaniverse4](https://nianticlabs.com/news/scaniverse4)).

So the capture exists, at scale, in millions of pockets. **The problem is that the scan dies as a pretty picture.** Today a 3DGS scan ends as a mesh, a splat to spin around, or a real-estate fly-through. It is not a *queryable spatial database*, and it is not exposed to an agent in a way the agent can *act on*.

### Is there a protocol that connects 3D Gaussian splatting scans to agentic commerce checkout?

Based on the research behind this document, no — not end-to-end. A handful of players touch adjacent pieces, but none close the loop:

- **RakuAI** is the closest existing "phone scan → splat → MCP" product, but its published tool surface (`get_metrics`, `get_scene_state`, simulation control) appears game/world-model oriented; there is **no published evidence it segments architectural elements** like doorways, windows, or radiators ([rakuai.com](https://rakuai.com/)). *(This is drawn from search snippets, not a first-party read — see "Honest assumptions.")*
- **Matterport** produces the most production-ready *segmented, measured* twin — automated room/wall/door/window measurements via its Model API — but it is mesh/photogrammetry-based, Enterprise-tier, and exposed via **REST/SDK, not MCP**, with **no commerce layer** ([matterport.github.io/showcase-sdk](https://matterport.github.io/showcase-sdk/modelapi_property_intelligence.html)).
- **InteriorGS** (manycore-research) pairs 1,000 indoor 3DGS scenes with occupancy maps and agent navigation — but it is a **research dataset with zero commerce** ([github.com/manycore-research/InteriorGS](https://github.com/manycore-research/InteriorGS)).
- Robust **semantic segmentation of doors/windows/radiators + true metric floor area** from a phone splat is still **active research** (SegSplat, GaussianCut, 3DGraphLLM), not a shipping commodity.

The pieces are scattered across four silos: scanning, **Gaussian splat segmentation**, agent reasoning, and commerce. **Nobody, as far as this research found, has wired the chain.** SpatialCart is proposed as the connective tissue that would — a vendor-neutral schema any scanner could emit and any commerce agent could consume.

---

## The principle: "your scan lives in your context base, and agents reuse it without re-asking you"

This is the core of the idea, and it is worth stating as a principle.

Today, every agent interaction starts from zero about your physical world. If you ask an assistant to help you buy a rug, it asks you for the room's dimensions. If you ask a different assistant next week about a bookshelf, it asks again. The physical context is never *remembered*, never *shared*, never *reused*.

**SpatialCart is designed to treat your captured space as a first-class, persistent context object** — the way your calendar or your address book already are. You scan once. The scan is processed into a standardized **measured spatial database** (a **digital twin** for furnishing): room geometry, segmented architectural elements (walls, floor, doors, windows, fixed obstructions), metric dimensions with explicit tolerance bands, and a described delivery path.

From then on, the intended behavior is:

- **Any agent you authorize reads that database via MCP** instead of interrogating you.
- The agent already knows your wall is 3.4 m wide (± a stated tolerance), that the hallway has a 78 cm doorway with a turn, that the south-facing terrace gets afternoon sun.
- You are never asked to measure anything again. The scan is the source of truth, reused across stores, across agents, across sessions.

This mirrors the same shift MCP itself represents — from re-pasting context into every prompt to *exposing context once and letting tools read it*. SpatialCart applies that shift to **the room you actually live in**.

It is also intended to be privacy-respecting by design: the spatial database is *yours*. Agents would read it under scoped, revocable permission. A retailer's agent gets exactly the fit-relevant signals it needs to verify a purchase — not a raw video walk-through of your home.

---

## The full value chain

Here is the end-to-end loop SpatialCart is *designed* to enable. Each stage already exists somewhere in isolation; the protocol's job is to standardize the hand-offs so the chain runs. To be clear, this loop has not been built — it is the specification's target.

1. **Capture** — You scan your room with a phone (Scaniverse / Polycam / KIRI / Luma / RoomPlan). Roughly 3–10 minutes, no special hardware required.
2. **Process into a measured spatial database** — The splat is segmented into architectural elements and reduced to metric geometry with explicit tolerance bands. *(Honest note: reliable segmentation + metric scale from a non-LiDAR phone is the hardest, least-proven part of the chain — see "Honest assumptions" below.)*
3. **Publish to your context base** — The spatial database is stored as a persistent, permissioned context object that agents can read via **MCP** — the data layer.
4. **Agent designs** — An authorized agent reads your real space and proposes a design: a layout, a palette, products that suit the room's dimensions, light, and your stated budget and taste. This is the **AI interior design agent** role.
5. **Select real SKUs across stores** — The agent matches the design to real, in-stock products from multiple retailers, reading their dimensions from their product feeds (scan to checkout).
6. **Fit-verify — the wedge** — For every candidate SKU, the agent checks two signals the industry openly admits are often missing:
   - **In-room fit** — does the item physically fit the measured floor/wall space, with clearances?
   - **Delivery-path clearance** — does it fit through the doorway, the hallway turn, the stairwell, the elevator? (Using diagonal-depth and narrowest-path logic, as standalone fit calculators like ItemFits already do — [itemfits.com/fit/door](https://itemfits.com/fit/door).)
7. **Order across stores** — The agent places the order(s) over the existing checkout rails — **ACP / UCP / Buy-for-Me** — as the orchestration/checkout layer. SpatialCart does **not** replace these; it is meant to feed them the one signal they lack.
8. **The scan persists** — Next month, when you want a lamp or a new rug, the agent reuses the same spatial database. No re-scan. No re-measuring.

The strategic placement is deliberate: **SpatialCart is positioned between MCP (data layer) and ACP/UCP (orchestration/checkout layer).** It is *not* a competing checkout protocol. It is the fit-verification and spatial-context schema meant to make the existing rails physically aware.

---

## The concrete pains it is meant to remove

Abstract specs don't move people. Real frustrations do. Here are the specific pains this loop is designed to eliminate — the kind of thing that turns into a return, a wasted afternoon, or a purchase you regret.

### How do I make sure furniture an AI orders will fit through my door? ("It won't fit through the opening — because of the faucet.")

You order a dishwasher or a fridge. On paper it clears the doorway by two centimeters. In reality there's a **wall faucet, a radiator valve, or a protruding door frame** stealing exactly those centimeters, and the appliance gets stuck in the hallway — or worse, gets delivered, doesn't fit, and goes back as a bulky-goods return (the single most expensive kind). SpatialCart's spatial database is designed to **segment fixed obstructions and compute the true narrowest path with diagonal-depth logic**, so the agent could catch this *before* it places the order. This is the headline pain, and independent industry blogs describe it as exactly the kind of room-fit signal "most home-goods catalogs lack" ([myhfa.org](https://myhfa.org/blog/how-ai-is-leveling-the-playing-field-for-furniture-retailers-in-2026/), [paz.ai](https://www.paz.ai/for/home-goods-brands)). *(These are vendor/industry sources, directional rather than authoritative.)*

### "Which terrace flooring survives my sun and heat?"

You're choosing decking or tiles for a terrace. The right answer depends on **orientation**: a south-facing surface bakes in afternoon sun, so dark composite that warps or scorches is the wrong call; a shaded north-facing corner has different priorities (moss, drainage). A scan that captures the terrace's **orientation and sun exposure** could let the agent recommend flooring by real environmental conditions — not by a generic "this looks nice" render. *(Honest note: deriving reliable sun/heat orientation from a scan is an enrichment we'd validate in the MVP, not a guaranteed capability today.)*

### "Stop showing me one product at a time — furnish the whole space."

You buy a pergola. Two weeks later you realize you need a table and chairs that fit *under* it and *around* the cleared floor area, plus storage that fits the leftover wall. Today that's three separate searches, three separate fit-guesses. Because the agent would hold the **whole measured space** in context, it could **proactively match a coherent set** — pergola, table-and-chairs sized to the remaining clearance, storage sized to the empty wall — each verified to fit together and to come through the door. The persistent spatial database is what is intended to make proactive, space-aware suggestions possible instead of one-SKU-at-a-time guessing.

These three are illustrative, not exhaustive. The common thread: **the agent would remove the pains by reasoning over a remembered, measured model of your real space** — which is precisely what no current tool appears to do end-to-end.

---

## Who this is for

SpatialCart speaks to two audiences at once:

- **Scanner / 3DGS-software makers** — Your scans currently end at a pretty picture or a mesh. This protocol is meant to turn every scan into a **monetizable, agent-shoppable spatial database**. The moment an AI agent can read, measure, and *order* against your output, your free scanner becomes a commerce funnel. The pitch: be the first scanner whose output is natively fit-verified and shoppable across stores. *(Plausible fits to evaluate: Niantic/Scaniverse, Polycam, KIRI, Luma, Matterport, XGRIDS, RakuAI.)*
- **Retailers / marketplaces** — Furniture's biggest hidden cost is **fit-failure returns**. This protocol aims to verify fit *before* the agent places the order, cutting returns and lifting agentic-checkout conversion in the highest-intent, lowest-return segment. It is designed to plug into the UCP/ACP rails you're already adopting; you add the one signal the industry admits is often missing. *(Plausible fits to evaluate: IKEA, Wayfair, Ashley Furniture, The Home Depot, Amazon, Target/Walmart/Best Buy, Leroy Merlin, Kaufland, and the long tail of Shopify furniture merchants.)*

---

## Honest assumptions and open risks

I would rather lose a conversation than win one on a claim that isn't true. So, plainly:

- **Stage.** This is a concept + protocol spec + pitch. There is **no working code and no benchmarked accuracy.** It is a *designed, integrated specification and a priority disclosure*, not a product. Nothing in this document should be read as "it works."
- **Segmentation is unproven at production grade.** Reliable semantic segmentation of doors/windows/radiators **and** true metric floor area from a *phone* 3DGS scan is **active research** (SegSplat, GaussianCut, 3DGraphLLM), not a shipping commodity. Metric scale from non-LiDAR phones is imperfect. The spec defines the **schema and tolerance bands**; achieving the measurement accuracy is the core MVP risk, and I'm honest that it is unsolved here.
- **"Fit verification" has boundaries — there is no "magic guarantee."** Doorway/stair fit needs diagonal-depth and narrowest-path logic *plus* a measured delivery path. If you don't scan the hallway and stairwell, the agent can't verify them — **delivery-path clearance is a defined input, not magic.**
- **Prior-art claim is web-based, not a patent search.** The "nobody has the full closed loop" assessment comes from web research, **not** a USPTO/Espacenet/Google Patents search. A proper patent / freedom-to-operate search should happen **before** asserting novelty publicly. Kujiale/Coohom (the team behind InteriorGS) and RakuAI are the closest watchers and could be in stealth.
- **It complements, never competes with, ACP/UCP.** Positioning this as a rival checkout standard would alienate exactly the Stripe/OpenAI/Google/Shopify ecosystem whose rails it depends on. SpatialCart is the spatial-context and fit-verification layer *between* MCP and ACP/UCP.
- **Standards win by adoption, not elegance.** A solo-authored spec with no reference implementation has weak gravity. The realistic path is **one scanner vendor + one retailer co-piloting it**, not organic standardization.
- **Some competitive facts are directional.** A few specifics (RakuAI's exact MCP tool surface and whether it segments building elements; live-vs-announced ACP/UCP merchant lists; whether IKEA exposes agent-readable dimensions) came from search snippets behind access restrictions, not first-party reads. Treat them as directional and re-verify before relying on them.

---

## What I'm asking for

I've authored the spec. What I'm looking for is a partner to make it real: a 3DGS-software maker or a retailer/marketplace to **finance and build the MVP** with me as protocol author, co-founder, or technical advisor. The repository is the credential; the integrated design and the fit-verification schema are the contribution; and Andrii Shramko's authorship of the integrated loop is on the public, timestamped record.

If you build scanners, I'll show you how your output could become shoppable. If you sell furniture, I'll show you the path to fit-guaranteed, low-return agentic sales. Either way — let's build the bridge.

---

*Conceived and authored by Andrii Shramko. This document is a concept and protocol specification at the design stage; it describes intended capabilities, not a shipped product. See the repository's LICENSE, COMMERCIAL-LICENSE, and AUTHORSHIP notes for attribution and licensing terms.*


---

## Author & contact

**Conceived and authored by Andrii Shramko.** SpatialCart Protocol is a proposed open, vendor-neutral specification — a timestamped priority disclosure and a partnership invitation, not a shipping product.

- **Author:** Andrii Shramko
- **LinkedIn:** https://www.linkedin.com/in/andrii-shramko/
- **Book a call (calendar):** https://calendar.app.google/Ff729HqGk4RpzPNDA
- **Email:** zmei116@gmail.com
- **GitHub:** https://github.com/AndriiShramko

*Spec text licensed CC BY-NC 4.0 — attribution to Andrii Shramko required; commercial use requires a separate license (see [COMMERCIAL-LICENSE.md](../COMMERCIAL-LICENSE.md)).*
