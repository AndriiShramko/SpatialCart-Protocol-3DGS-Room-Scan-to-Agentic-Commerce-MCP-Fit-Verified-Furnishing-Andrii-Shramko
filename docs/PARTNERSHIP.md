# SpatialCart Protocol — Partnership & Licensing Brief

**An open specification for turning a phone 3D Gaussian-splatting room scan into a measured spatial database that AI agents read via MCP to design, fit-check, and order real furniture across stores.**

Authored by **Andrii Shramko**. This document is an invitation to talk.

---

## Read this first (honesty up front)

SpatialCart Protocol is a **concept, a protocol specification, and a pitch** — not a shipped product. There is **no working code, no benchmarked accuracy, and no live deployment yet**. The value on offer today is the integrated design: a clean, vendor-neutral schema that connects two stacks the industry already built separately but never wired together —

1. **phone-captured 3D Gaussian-splat room scans** (Scaniverse, Polycam, KIRI, Luma, Apple RoomPlan), and
2. **agentic commerce rails** (OpenAI/Stripe **ACP**, Google/Shopify **UCP**, Amazon Buy-for-Me, Visa/Mastercard agent payments) —

…with the one signal both sides admit is missing in the middle: **fit verification** (does the real SKU fit the measured room *and* clear the delivery doorway/stairwell/elevator).

**Andrii Shramko is looking for a financing and/or licensing partner** — a scanner-software maker, a retailer/marketplace, or a government/urban buyer — to co-fund the MVP, co-own the standard, and become the first reference adopter. He is passionate about this, ready to build the team, and available to license the spec, lead the reference implementation, and help build the bridge.

This brief speaks to three audiences. Jump to yours:

- **[A) Home, furniture & building retailers](#a-retailers--marketplaces)** (IKEA, Wayfair, Ashley, Home Depot, Leroy Merlin, Kaufland, Shopify merchants…)
- **[B) 3DGS software & scanner makers](#b-3dgs-software--scanner-makers)** (Scaniverse/Niantic, Polycam, Matterport, XGRIDS, RakuAI, KIRI, Luma…)
- **[C) Governments & urban planners](#c-governments--urban-planners)** (street-furniture, lighting, exterior digital twins)

---

## Where this sits (so nobody thinks it competes with their stack)

SpatialCart is **not** a new checkout protocol. It is the **data layer between MCP and ACP/UCP** — the answer to the question *"what is the missing data layer between a 3D room scan and buying furniture online?"*:

- **MCP (Model Context Protocol)** — the agent-to-tool data layer (built by Anthropic, donated to the Linux Foundation in Dec 2025, backed by Block, OpenAI, Google, Microsoft, AWS). SpatialCart is explicitly designed to be emitted and consumed over MCP.
- **ACP / UCP** — the orchestration + checkout rails. ACP is Apache-2.0, OpenAI + Stripe ([github.com/agentic-commerce-protocol](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol)). UCP is Google + Shopify, announced at NRF 2026 and explicitly designed to "run over MCP" ([developers.googleblog.com](https://developers.googleblog.com/under-the-hood-universal-commerce-protocol-ucp/)).
- **SpatialCart** — the missing schema in between: *room geometry + segmented architectural elements (walls, doors, windows, radiators) + per-SKU dimensional fit envelope + delivery-path constraints*. Any scanner can emit it; any commerce agent can consume it. Adopting it does **not** force anyone off their existing stack.

The wedge is **fit verification**, and it is evidence-backed: independent industry sources state that "room-fit and dimensional accuracy is the key differentiation in AI furniture shopping," and that most home-goods catalogs **lack** recommended-room-size, ceiling-clearance, stair-clearance and doorway-clearance signals ([paz.ai](https://www.paz.ai/for/home-goods-brands), [myhfa.org](https://myhfa.org/blog/how-ai-is-leveling-the-playing-field-for-furniture-retailers-in-2026/)). These are vendor/industry-blog sources — directional, not peer-reviewed — but they independently name the exact gap this spec targets.

---

<a name="a-retailers--marketplaces"></a>
## A) Retailers & marketplaces

*For IKEA, Wayfair, Ashley Furniture, The Home Depot, Target, Walmart, Best Buy, Amazon, Leroy Merlin, Kaufland, and the long tail of Shopify furniture merchants.*

### The problem you already feel

Agentic commerce is here, but **it is blind to physical space**. ACP and UCP can negotiate a cart and tokenize a payment, but neither knows whether the sofa fits the room — or whether it fits through the front door. For furniture and large-format goods, **fit-failure is a major hidden return driver**: a customer orders, the item arrives, it won't clear the doorway or fill the wall, and it goes back. Returns on bulky goods are among the most expensive returns you process.

This is the heart of the question retailers keep asking: *"how do I make sure furniture an AI orders will fit through my door?"* The ACP product feed *does* support optional length/width/height + unit and weight fields, and Google Merchant Center supports schema.org width/height/depth — so **dimensions can travel**. What's missing is the **room side** of the equation and the **fit math** that reconciles SKU dimensions against a *measured* space and a *measured* delivery path. That is exactly what SpatialCart standardizes.

### The offer

Adopt (or co-develop) an open, vendor-neutral **fit-verification feed** that plugs into the UCP/ACP rails you're already adopting. The agent verifies fit **before** it places the order:

- **In-room fit** — the SKU's dimensional envelope is checked against the measured floor/wall space from the customer's scan.
- **Delivery-path clearance** — diagonal-depth + narrowest-path logic against a measured doorway/stairwell/elevator (a *defined input*, supplied by scanning the path or by user entry — not magic).

Intended result: **higher agentic-checkout conversion** (the agent can confidently say "yes, this fits") and **lower return rates** on your most return-prone category. To be clear, these are the design goals — the spec defines the schema and tolerance bands; the conversion and return-rate impact is exactly what a pilot would measure.

### What Andrii brings

- The authored, versioned, vendor-neutral schema — the connective tissue between your catalog and the customer's spatial scan.
- A neutral-party standard you can endorse without ceding control to a rival's walled garden.
- Availability to license the spec, build the MVP, and lead the reference integration as protocol author / technical advisor.

### What a pilot looks like

1. **Scope (weeks 0–2):** pick one high-return furniture category (e.g. sofas, wardrobes, vanities) and one geography.
2. **Feed mapping (weeks 2–6):** map a slice of your catalog into the SpatialCart per-SKU fit envelope (dimensions you mostly already have + clearance signals).
3. **Scan side (parallel):** pair with one scanner vendor (see Section B) to produce a measured room + doorway input.
4. **Agent loop (weeks 6–12):** an MCP-exposed agent reads room + feed, runs fit verification, and surfaces "fits / doesn't fit / fits but tight" before checkout via your existing ACP/UCP endpoint.
5. **Measure:** conversion lift on fit-verified recommendations vs. control, and return-rate delta on shipped fit-verified orders.

### Why these names specifically

- **IKEA** already has Kreativ room-scan + an AI Assistant + LiDAR scanning, but stops at single-brand manual visualization — the natural adopter to add **autonomous fit-verified ordering**.
- **Wayfair** is a UCP co-developer and named early agentic-checkout partner on Google AI Mode — large-item furniture where doorway/stair fit is a top return driver.
- **Ashley Furniture** is live on Stripe's Agentic Commerce Suite and a Google UCP launch partner — big bulky goods = highest fit-failure cost.
- **The Home Depot** expanded its Google Cloud agentic-AI partnership at NRF 2026 and endorsed UCP — appliances/vanities where delivery clearance is critical.
- **Amazon** has Buy-for-Me + Alexa for Shopping over 100M+ products and View-in-Your-Room AR, but lacks a persistent measured twin + fit logic.
- **Leroy Merlin & Kaufland** are EU big-box / marketplace entry hooks (Kaufland already runs a multi-country Seller API; Leroy Merlin is reported to be exploring agentic AI).
- **Shopify furniture merchants** — every store is auto-assigned a Storefront MCP endpoint, making the long tail the protocol's natural distribution channel.

> **Honesty note for retailers:** several "live vs. announced" ACP/UCP merchant claims, Amazon's coverage figures, and IKEA's exact agent-feed dimension exposure came from search summaries and secondary write-ups, not first-party confirmation. Treat the competitive framing as directional; we'll confirm specifics together before any public claim.

### Contact to discuss

If you're a retailer or marketplace and want fit-verified, low-return agentic furniture sales — **[contact Andrii](#contact).**

---

<a name="b-3dgs-software--scanner-makers"></a>
## B) 3DGS software & scanner makers

*For Scaniverse/Niantic, Polycam, KIRI Engine, Luma AI, Matterport, XGRIDS, RakuAI, and the Apple RoomPlan / ARKit ecosystem.*

### The problem you already feel

Your scan currently ends at **a pretty picture or a mesh**. A user looks at it — and that's where the value stops. There is no native path from "a scan you look at" to "a scan agents act on," and therefore no monetizable downstream of every free capture. The open question for your category — *"how can a 3DGS scanner app expose measured walls, doors and windows to AI shopping agents?"* — does not yet have a shipping answer.

As of mid-2026, based on web research (not a patent search), **no publicly verified product exposes the full chain**: published 3DGS phone scan → semantic segmentation of doors/windows/radiators/walls + metric floor area → exposed to an LLM agent via MCP to *act* on it. The pieces exist separately:

- **RakuAI** is closest — markets "scan a room → 3D Gaussian splat → your AI assistant can read, measure and reason over it via MCP" — but it appears game/sim-oriented, with read-only `get_metrics` / `get_scene_state`, and **no documented building-element segmentation**.
- **Matterport** delivers segmented, measured building data (room/wall/door/window measurements via its Model API) — but via REST/SDK, Enterprise-tier, mesh-based, **not** MCP and **no** commerce.
- **XGRIDS** emits metric + BIM-semantic output, but via a Revit plugin, **not** an agent interface.
- Splat segmentation + scene-graph-over-LLM tooling (SegSplat, GaussianCut, 3DGraphLLM, InteriorGS) remains **research code**, not a packaged MCP-over-a-published-scan service.

### The offer

Add two things to your output and you change your business model:

1. **Architectural segmentation** — label walls, floor, doors, windows, radiators and return metric floor/wall area (with honest tolerance bands).
2. **A Spatial MCP** — expose that measured, segmented scene to LLM agents so they can read it, fit-check against real SKUs, and order against it.

The moment an agent can **read, measure and ORDER** against your output, your **free scanner becomes a commerce funnel**: a new sales-and-retention story, plus an output you can monetize (cert, feed access, revenue share on fit-verified orders).

### What Andrii brings

- The vendor-neutral SpatialCart schema your scan can emit natively — so you don't have to invent the commerce side.
- A defensive, category-leading position: **RakuAI and Kujiale/Coohom (the org behind InteriorGS) are already circling this.** Adopting (or co-developing) an open, neutral, independently-authored standard lets you **lead the category instead of being locked out of a rival's walled garden.**
- Andrii as protocol author / co-founder / paid technical advisor to build the reference implementation with you.

### What a pilot looks like

1. **Capture baseline (weeks 0–2):** take your existing splat/mesh output as the input.
2. **Segmentation MVP (weeks 2–8):** prototype door/window/wall/floor-area labeling on top of it (honest about accuracy on non-LiDAR phones — metric scale from photo-only capture is imperfect, and reliable element segmentation is active research, not a commodity).
3. **Spatial MCP (weeks 6–10):** expose a read API — room geometry, segmented elements, dimensions — as MCP tools an agent can call.
4. **Fit loop (weeks 10–14):** wire to one retailer feed (see Section A) so an agent can run fit verification end-to-end.
5. **Measure:** does the agent correctly answer "does X fit this room and this doorway?" on a held-out set, with a stated tolerance band.

> **Honesty note for scanner makers:** RakuAI's exact MCP tool surface and whether it does building-element segmentation came from snippets behind 403s, not first-party reads. Reliable metric segmentation from a phone splat is a genuine MVP risk we will measure, not assume.

### Contact to discuss

If you make a scanner or 3DGS software and want your scans to become agent-shoppable and monetizable — **[contact Andrii](#contact).**

---

<a name="c-governments--urban-planners"></a>
## C) Governments & urban planners

*For municipalities and agencies planning street furniture, lighting, signage and exterior public space.*

### The opportunity

The same loop runs **outdoors**. Cities already build exterior digital twins from mobile-mapping LiDAR (Cyclomedia, <2 cm relative accuracy for inventorying street furniture, lamp posts, signs) and aerial 3D city models (Vexcel), situated via platforms like Bentley iTwin + Cesium. Concrete deployments exist: Smart Dublin's Cesium-powered twin for bike-route utilization; the City of Raleigh using an NVIDIA smart-city blueprint to automate streetlight timing; NorDark-DT placing light poles with IES photometric files in an urban twin.

Meanwhile, **phone-captured 3DGS** (Scaniverse, Polycam, KIRI) has made *cheap, fast, crowd-scalable* exterior capture real — but the two threads (government twins from mobile-mapping, and consumer-grade phone splats) have **not yet been shown converging in a named municipal procurement**. That convergence is the opening.

### The offer

A neutral schema to turn **exterior phone/handheld 3DGS scans** of a street, plaza or park into a **measured spatial database** that planning agents can query and act on: place/relocate a bench, bin, kiosk, sign or light pole; check clearance against the measured pavement, curb and existing furniture; and — where a procurement catalog exists — fit-check and requisition real street-furniture SKUs against the measured space.

### What Andrii brings

- The same SpatialCart fit-verification schema, applied to exterior elements (street furniture, lighting).
- Vendor-neutrality — works alongside CityGML / 3D Tiles / IFC substrates and existing twin platforms rather than replacing them.
- Authorship and availability to lead a reference pilot.

### What a pilot looks like

1. **Pick a block:** one street or plaza segment with a known street-furniture/lighting plan.
2. **Capture:** phone/handheld 3DGS pass of the exterior, plus existing mobile-mapping/GIS data if available.
3. **Segment + measure:** label pavement, curb, existing furniture, poles; produce metric clearances.
4. **Agent loop:** a planning agent proposes placements and checks clearance/spec (e.g. pole spacing, ADA-style passage widths) against the measured scene.
5. **Measure:** planner time saved and placement-clearance accuracy vs. manual survey.

> **Honesty note for governments:** exterior 3DGS-from-phone for street-furniture planning is, to our knowledge, **not yet a proven municipal deployment** — this is a designed concept building on adjacent, real deployments. Accuracy and procurement-fit are pilot questions, stated openly.

### Contact to discuss

If you're a city, agency or urban-planning vendor — **[contact Andrii](#contact).**

---

## What Andrii is asking for

A **financing and/or licensing partner** to:

- **co-fund the MVP** (you finance, Andrii authors and leads the reference build), and/or
- **take a commercial license** of the spec/schema (the open spec is published under **CC BY-NC 4.0** — non-commercial use is free with attribution to *Andrii Shramko*; commercial shipping requires a paid commercial license, which is the conversation we'd like to have), and/or
- **co-sign a pilot** as the first scanner vendor + first retailer (or first city) to adopt the standard.

Realistic path, stated honestly: standards win by **adoption, not elegance**. A solo-authored spec with no reference implementation has weak gravity on its own. The goal is **one scanner vendor + one retailer (or one city)** to co-sign and pilot — not organic standardization. That is precisely the partnership this brief is asking for.

> **IP timing note:** publishing a spec publicly can affect patentability in absolute-novelty jurisdictions. If patent protection matters to a partner, the right sequence is a US provisional filing *before* broad public disclosure. Andrii is open to discussing IP structure as part of any partnership. The "no one has the full closed loop" framing is based on web research, **not** a USPTO/Espacenet/Google Patents search — a proper freedom-to-operate search is recommended before asserting novelty publicly.

---

<a name="contact"></a>
## Contact — let's discuss

- **Author:** Andrii Shramko
- **LinkedIn:** https://www.linkedin.com/in/andrii-shramko/
- **Book a call (calendar):** https://calendar.app.google/Ff729HqGk4RpzPNDA
- **Email:** zmei116@gmail.com
- **GitHub:** https://github.com/AndriiShramko

If you are a retailer, a scanner/3DGS maker, or a government/urban buyer and any of the above resonated — reach out to **Andrii Shramko** to license the spec, co-fund the MVP, or co-sign a pilot. He is ready to build the team.

---

*SpatialCart Protocol — concept and specification authored by Andrii Shramko. Spec text under CC BY-NC 4.0; any future reference code intended under Apache 2.0. This is a priority disclosure and a partnership invitation, not a shipping product.*