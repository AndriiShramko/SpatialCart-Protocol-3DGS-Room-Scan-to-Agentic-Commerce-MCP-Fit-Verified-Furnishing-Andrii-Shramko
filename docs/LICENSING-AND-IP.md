# Licensing & IP — SpatialCart Protocol

**Authored by Andrii Shramko.** This page explains, in plain language, how the intellectual property in this repository is positioned: what is protected, what others are allowed to do, what they are *not* allowed to do without asking, and how a company can license the **SpatialCart Protocol** specification to build on it commercially.

> **Honest stage note:** SpatialCart Protocol is a **concept, an open specification, and a pitch** at the specification stage. There is **no working code, no benchmarked accuracy, and no shipping product** in this repository. The IP that exists here is the *integrated design and the written specification* — a vendor-neutral, standardized fit-verification schema that connects a phone **3D Gaussian-splatting room scan** to **agentic commerce**. What is being protected and licensed is the design, the schema, and Andrii Shramko's authorship of the integrated loop — **not** a running system. Nothing on this page should be read as a claim that the protocol is implemented, tested, or production-ready.

---

## 1. What this repository *is*, legally

This repository is a **defensive publication** of an integrated protocol design — an answer to a question the industry has not yet answered: *what is the missing data layer between a 3D room scan and buying furniture online?*

Two stacks already exist independently in the market:

1. **Phone-captured 3D Gaussian-splatting (3DGS) room scans** — Scaniverse, Polycam, KIRI Engine, Luma AI, Apple RoomPlan, Matterport, XGRIDS.
2. **Agentic commerce rails** — OpenAI/Stripe **ACP** ([Agentic Commerce Protocol](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol)), Google/Shopify **UCP** ([Universal Commerce Protocol](https://developers.googleblog.com/under-the-hood-universal-commerce-protocol-ucp/)), Amazon Buy-for-Me, and agent-payment systems from Visa and Mastercard — sitting on top of the **Model Context Protocol (MCP)** as the de-facto agent-to-tool data layer.

What has **not** been published before, to the best of the author's web research, is the *connective tissue*: a measured spatial database, expressed as a vendor-neutral schema, that standardizes the two signals the industry openly admits are missing — **in-room fit** (does the product physically fit the measured floor and wall space?) and **delivery-path / doorway clearance** (does it fit through the doorway, stairwell, or elevator?). This is the answer to *"how do I make sure furniture an AI orders will actually fit through my door?"* and *"what standard exposes room dimensions and doorway clearance to a shopping AI agent via MCP?"*

Independent industry sources flag exactly this gap: room-fit and dimensional accuracy is described as "the key differentiation in AI furniture shopping" ([paz.ai](https://www.paz.ai/for/home-goods-brands)), and most home-goods catalogs are reported to "lack room-fit signals like recommended room size, ceiling clearance, stair clearance, and doorway clearance for delivery" ([myhfa.org](https://myhfa.org/blog/how-ai-is-leveling-the-playing-field-for-furniture-retailers-in-2026/)). These are vendor/industry-blog observations, not peer-reviewed data — treat them as directional signal, not proof.

Publishing the integrated design here, with a public commit history, does two things:

- It **timestamps Andrii Shramko's authorship** of the integrated fit-verification loop on a specific, verifiable date.
- It establishes **prior art** — a public, dated disclosure that helps prevent a third party from later patenting the same integrated loop *against* Andrii.

> **Prior-art caveat (honest, updated 2026-07):** A spot Google Patents check has now surfaced two **granted** patents on the *in-room fit* step — **Snap US 12,327,277 B2** ("Home based augmented reality shopping," granted Jun 2025) and **Amazon US 12,141,929 B1** ("Augmented reality furniture layout recommendation," granted Nov 2024). Neither claims **delivery-path clearance, cross-store agentic ordering, or a vendor-neutral fit-schema** — which is SpatialCart's narrow delta — but their existence means the bare "does it fit the room" idea is **not** patentable open space, and it changes the filing calculus in §4. This remains a spot check, **not** a full USPTO / Espacenet freedom-to-operate search; commission one before asserting novelty as a legal claim or filing. Kujiale/Coohom (InteriorGS), RakuAI, and Roomform.ai are the closest active products and could be operating in stealth.

---

## 2. The license

This project uses a **dual-track** licensing posture.

### Track 1 — The specification, whitepaper, and schema docs

Licensed under **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)**.

**What CC BY-NC 4.0 permits — anyone may:**

- **Read, share, and redistribute** the spec, schema, and docs in any medium.
- **Adapt and build upon** the material — remix, transform, extend the schema — for **non-commercial** purposes (research, evaluation, internal prototyping, education).

**…on two conditions:**

- **Attribution.** You must credit **Andrii Shramko** by name, link back to this repository, and indicate if you made changes. This is the clause that *legally cements authorship*: anyone reusing the text or schema is contractually obligated to name Andrii as the author.
- **NonCommercial.** You may **not** use the material, or anything derived from it, for **commercial purposes** without a separate commercial license. Shipping a product, a paid feature, a hosted service, or a revenue-generating integration built on this spec is a commercial use.

**What CC BY-NC 4.0 forbids without a separate license:**

- Commercial deployment of the protocol or schema in any product or service.
- Stripping or omitting attribution to Andrii Shramko.
- Re-publishing the spec as your own work.

### Track 2 — Future reference / example code

There is **no code in this repository today.** *If and when* example or reference code is added, the intent is to license it **separately** under the **Apache License 2.0**.

Apache 2.0 (not MIT) is chosen deliberately: it includes an **explicit patent grant**, and it is the established norm for protocols that *want* to be adopted — notably, ACP itself is published under Apache 2.0. Matching that precedent lowers the friction for scanner vendors and retailers to adopt a reference implementation.

> If a time-delayed commercial moat is later desired for a hosted reference server, **BSL 1.1** (Business Source License) is the reserved alternative — open to read and self-host, with commercial-hosting rights converting to an open license after a set period. This is an option held in reserve, not a current commitment.

---

## 3. What copyright actually protects here (and what it does not)

This is the single most important thing to understand, stated plainly:

- **Copyright protects the *expression*** — the specific written text of the specification, the wording, the structure of the document, the diagrams, and the exact schema definitions as written.
- **Copyright does NOT protect the bare *idea*.** The abstract concept "connect a 3D room scan to a shopping agent and check fit" is **not** ownable by copyright. Anyone is free to independently arrive at the same idea and implement it in their own words and their own schema.

So what gives Andrii leverage over the *idea/method* itself?

- **The public, timestamped disclosure** (prior art / defensive publication) — which does not grant exclusivity but blocks others from claiming the integrated method as theirs.
- **A patent** — the *only* instrument that can grant exclusivity over the **method/system** (the fit-verification process), independent of how it is written. See below.

---

## 4. Provisional patent — the exclusivity option

If exclusivity over the **fit-verification method and system** matters, the path is a **US provisional patent application**.

- A provisional is **comparatively cheap**, gives a **~12-month priority window**, and **preserves the option** to file a full (non-provisional) patent later with claims on the fit-verification method/system (measured room geometry + segmented architectural elements + per-SKU dimensional fit envelope + delivery-path constraints).
- **Critical sequencing — read this carefully:** A public spec, once disclosed, can **bar patentability in absolute-novelty jurisdictions** (e.g. the EU). CC BY-NC does **not** grant or preserve any patent rights. Therefore, if patenting is desired, the correct order is:

  1. **File the US provisional patent FIRST.**
  2. **Then publish** the CC BY-NC spec.

  Do **not** publish first and file later if EU patent rights matter to you.

- If Andrii decides **not** to patent, the **public spec is itself a strong defensive publication** — it does not grant him exclusivity, but it does prevent others from patenting the same integrated loop against him.
- **Draft any provisional around the incumbents.** Because Snap (**US 12,327,277 B2**) and Amazon (**US 12,141,929 B1**) already hold granted claims on *in-room fit* (see the prior-art caveat in §1), a filing should center on the elements they do **not** claim — **delivery-path clearance, the cross-store agentic-ordering loop, and the vendor-neutral fit-schema** — and IP counsel should run a freedom-to-operate check against both patents first.

> **Not a hypothetical detail:** because this repository may already be public, anyone evaluating the patent route should treat the publication date as the disclosure date and consult patent counsel about which windows (e.g. the US grace period) remain open. The author has not represented that any patent has been filed.

---

## 5. Commercial licensing — how to build on this

**If your company wants to use, ship, or build a commercial product or service on the SpatialCart Protocol specification, you need a commercial license. Contact Andrii Shramko.**

The CC BY-NC NonCommercial clause is intentional — it is the **forcing function** that opens this conversation. This is **not** a closed door; it is an open invitation. Andrii designed this protocol to be *adopted*, and he is actively, passionately looking for the first scanner/3DGS-software maker and the first retailer or marketplace to co-sign, pilot, and **finance the MVP**. The repository is the credential; the build is the next chapter — and he wants to build it with a partner.

A commercial license can cover, depending on what you need:

- **Commercial use of the spec/schema** in your product, feed, or service (tiered by company size / use).
- **Founder / architect / advisory engagement** — finance and build the MVP with Andrii as the protocol's author and technical lead.
- **Reference-implementation and integration services** — paid help wiring the bridge for the first scanner vendor and first retailer who adopt it (build-the-bridge contracts).
- A future **"SpatialCart-conformant" certification program** for scanner outputs and retailer feeds that pass fit-verification.
- **Patent-backed IP licensing**, *if and when* the fit-verification method patent is filed and granted (it has not been, as of this writing).

This protocol is explicitly designed to **complement, not compete with**, ACP, UCP, and MCP. It sits *between* MCP (the data layer) and ACP/UCP (the orchestration / checkout layer) — adopting it does **not** force you off your existing commerce stack. It adds the one signal the whole industry agrees is missing: **fit verification** for room-fit dimensional data and delivery-path clearance.

This is also the answer to the questions retailers and scanner vendors are starting to ask out loud: *"is there an open standard for fit-verified furniture ordering across multiple stores?"* and *"how can a 3DGS scanner app expose measured walls, doors, and windows to AI shopping agents?"* The honest status: the **standard is specified here; the implementation is the work ahead.**

---

## Contacts

- **Author:** Andrii Shramko
- **LinkedIn:** https://www.linkedin.com/in/andrii-shramko/
- **Book a call (calendar):** https://calendar.app.google/Ff729HqGk4RpzPNDA
- **Email:** zmei116@gmail.com
- **GitHub:** https://github.com/AndriiShramko

---

## Not legal advice

This document explains the *intent* and *posture* of the project's licensing and IP in plain language. It is **not legal advice** and does not create any attorney-client relationship. Patent timing, freedom-to-operate, jurisdictional novelty rules, and license enforceability are fact-specific — consult a qualified IP attorney before relying on any statement here, before filing any patent, and before making any public-disclosure or commercial-licensing decision. The authoritative license terms are the full texts of **CC BY-NC 4.0** and, for any future reference code, **Apache 2.0**, as included in this repository.