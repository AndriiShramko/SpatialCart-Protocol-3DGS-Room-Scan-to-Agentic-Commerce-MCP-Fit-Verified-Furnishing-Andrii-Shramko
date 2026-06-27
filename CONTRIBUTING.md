# Contributing to SpatialCart Protocol

Thanks for stopping by. **SpatialCart Protocol** is an open specification — authored by **Andrii Shramko** — for turning a phone-captured **3D Gaussian-splatting room scan** into a **measured spatial database** that AI agents read via **MCP (Model Context Protocol)** to design, **fit-check**, and order real furniture across stores. It is the connective tissue *between* two stacks that already exist but have never been wired together: the spatial-scan stack (Scaniverse, Polycam, KIRI, Luma, Apple RoomPlan) and the agentic-commerce rails (ACP, UCP, MCP). It complements those rails — it does not compete with them.

Be clear-eyed about the stage: **this is a concept and protocol specification, not working software.** There is no reference implementation yet, no benchmarked measurement accuracy, and no shipping MVP. The value today is the integrated design and the standardized **fit-verification schema** — in-room fit *and* delivery-path/doorway clearance, the two signals that the furniture-commerce industry openly discusses as [missing from most catalogs](https://myhfa.org/blog/how-ai-is-leveling-the-playing-field-for-furniture-retailers-in-2026/). If you have ever asked *"how do I make sure furniture an AI orders will actually fit through my door?"* or *"what is the missing data layer between a 3D room scan and buying furniture online?"* — this is the spec that tries to answer it, and **contributions of every kind are genuinely welcome.** Andrii is passionate about this and actively looking to build a team around it.

There are three ways to engage.

---

## 1. Implementers — build the bridge

Right now the most valuable contribution is a **reference implementation**, because standards win by adoption, not elegance. A solo-authored spec with no running code has weak gravity; real code is what changes that. Concretely:

- **A reference Spatial-MCP server** — exposes a segmented, measured room (walls, floor area, doors, windows, ceiling height) from a 3DGS scan as MCP tools/resources an agent can read. Honest caveat: reliable semantic segmentation of doors/windows/radiators and true metric floor area from a phone scan is still [active research](https://arxiv.org/pdf/2508.09977) (SegSplat, GaussianCut, [3DGraphLLM](https://arxiv.org/pdf/2412.18450)), not a solved commodity — and metric scale from non-LiDAR phones is imperfect. Prototyping against that uncertainty is exactly the kind of help we need.
- **Store-MCP adapters** — thin adapters that map a retailer's product feed and dimensional data onto the SpatialCart fit-verification schema, so an agent can reconcile a real SKU's dimensional envelope against the measured room and the delivery path. The [OpenAI ACP feed spec](https://developers.openai.com/commerce/product-feeds/spec) already carries optional length/width/height with a dimensions unit — a clean target to map from.
- **Capture-side emitters** — sample code showing how a scanner output (Scaniverse SPZ, Polycam raw export, [Apple RoomPlan](https://developer.apple.com/augmented-reality/roomplan/) USD) could emit the SpatialCart room-geometry schema.
- **Schema validators, test fixtures, tolerance-band logic** — including the diagonal-depth / narrowest-path math that doorway, stairwell, and elevator fit actually require. Note honestly that delivery-path clearance depends on a measured path or user input — it is a defined schema input, not magic.

**To start:** open an issue describing what you want to build, or open a draft PR against the schema docs. Small, focused proposals beat large ones. Please flag clearly what is implemented, what is stubbed, and what is unverified — this project follows a standing rule of never reporting something as "working" until it is verified end-to-end.

Future reference/example code is intended to be licensed under **Apache 2.0** (matching ACP's precedent and its explicit patent grant), separate from the spec license below.

---

## 2. Partners — retailers, marketplaces, scanner vendors

If you represent a **retailer/marketplace** asking *"how do we capture high-intent, fit-guaranteed, low-return agentic furniture sales?"* — or a **3DGS / scanner-software maker** asking *"how do we make our scans agent-shoppable and monetizable?"* — the conversation is a partnership one, not a pull request. The protocol is designed to sit between **MCP** (the data layer) and **ACP/UCP** (the orchestration/checkout layer), so adopting it does not force you off the rails you are already adopting.

Please head to **[PARTNERSHIP.md](PARTNERSHIP.md)** for how adoption, pilots, and co-development could work — and reach out directly. As the spec's author, **Andrii Shramko** is available to license it, help build the MVP, and lead the reference implementation.

---

## 3. Idea contributors

Not writing code or signing a partnership? You can still move this forward:

- Sharpen the **fit-verification schema** — edge cases in delivery-path clearance, stair turns, elevator depth, ceiling-height swap-outs.
- Contribute **prior-art and patent pointers.** The "nobody has closed the full loop" claim rests on web search, *not* a formal patent search — a proper USPTO / Espacenet / Google Patents review is welcome and important before any novelty is asserted publicly. (Manycore's [InteriorGS](https://github.com/manycore-research/InteriorGS) and RakuAI are the closest known work and deserve a careful look.)
- Test the idea against real rooms and real SKUs and report where the reasoning breaks.
- Improve the docs, the natural-language framing, or the alignment with MCP / ACP / UCP.

Open an issue with the `idea` or `prior-art` label. Thoughtful critique is as valuable as agreement — if you think a piece of this won't work, say so and show why.

---

## A note on commercial use

The specification is published openly so anyone can read, evaluate, and build interest — but **commercial use requires a separate license.** The spec is offered under **CC BY-NC 4.0**: attribution to **Andrii Shramko** is required, and any commercial shipping of the protocol means coming to talk about a commercial license. That funnel is intentional — it is how the work is sustained.

Before contributing or adopting, please read **[LICENSING-AND-IP.md](LICENSING-AND-IP.md)** for the full license terms, the authorship/attribution notice, the commercial-license path, and the IP/prior-art caveats.

By opening a PR or issue, you agree your contribution can be incorporated under the project's licensing model.

---

## Contacts

- **Author:** Andrii Shramko
- **LinkedIn:** https://www.linkedin.com/in/andrii-shramko/
- **Book a call:** https://calendar.app.google/Ff729HqGk4RpzPNDA
- **Email:** zmei116@gmail.com
- **GitHub:** https://github.com/AndriiShramko

*Conceived and authored by Andrii Shramko. SpatialCart Protocol is an open, vendor-neutral specification at the concept stage — a priority disclosure of the integrated scan-to-fit-verified-checkout loop, seeking partners to build it.*