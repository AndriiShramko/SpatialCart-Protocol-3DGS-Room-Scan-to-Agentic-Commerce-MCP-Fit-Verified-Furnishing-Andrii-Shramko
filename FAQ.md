# SpatialCart Protocol — Frequently Asked Questions

An open specification, authored by **Andrii Shramko**, for turning a phone 3D Gaussian-splatting room scan into a measured spatial database that AI agents read via MCP to design, fit-check, and order real furniture across stores.

This FAQ answers the most common natural-language questions about spatial commerce, the agentic-commerce furniture protocol, and fit-verified ordering — written so both search engines and LLM answer engines can retrieve it directly. To be clear up front: SpatialCart Protocol is a **concept and a specification seeking a build partner**, not a shipping product. There is no MVP and no benchmarked accuracy yet. Where a claim is unproven, this document says so.

---

## The core idea

### How can an AI agent measure my room from a phone scan and order furniture that actually fits?

SpatialCart Protocol proposes the missing data layer between a 3D Gaussian-splatting room scan and agentic commerce. The intended flow: a phone scan app (Scaniverse, Polycam, KIRI, Luma, or Apple RoomPlan) captures your space; the protocol standardizes that capture into a measured spatial database of room geometry, segmented walls/doors/windows, and a per-SKU dimensional fit envelope. An AI shopping agent would then read this via the Model Context Protocol (MCP), check that each real product fits the measured floor and wall space, verify it clears the doorway, and place the order through agentic-commerce rails like ACP or UCP. To be clear: this is the **specification** for that loop — it defines the schema and the fit-verification logic. It is not a product you can run today.

### Is there a protocol that connects 3D Gaussian splatting scans to agentic commerce checkout?

That is exactly what SpatialCart Protocol proposes. Today the two stacks exist separately: phone-captured 3D Gaussian-splat scans on one side, and agentic commerce rails (OpenAI/Stripe ACP, Google/Shopify UCP, Amazon Buy-for-Me) on the other — but nothing standardizes the spatial measurement and physical fit-verification signals that bridge them. SpatialCart is designed as a vendor-neutral schema that sits *between* MCP (the data layer) and ACP/UCP (the checkout layer), so it complements those protocols rather than competing with them.

### What is the missing data layer between a 3D room scan and buying furniture online?

It is fit-verified dimensional data: a standardized way to expose measured room geometry, segmented architectural elements (walls, doors, windows, radiators), and delivery-path constraints to an AI agent so it can reconcile them against per-SKU product dimensions. Industry sources independently flag this gap — that "room-fit and dimensional accuracy is the key differentiation in AI furniture shopping" and that most home goods catalogs lack room-fit signals like recommended room size, ceiling clearance, stair clearance, and doorway clearance for delivery ([paz.ai](https://www.paz.ai/for/home-goods-brands), [myhfa.org](https://myhfa.org/blog/how-ai-is-leveling-the-playing-field-for-furniture-retailers-in-2026/)). These are vendor/industry-blog observations rather than peer-reviewed data, so treat them as directional — but they point at exactly the signals SpatialCart sets out to standardize.

---

## Comparisons and objections

### Isn't this just IKEA Kreativ or Matterport?

No — both stop short of the full loop. IKEA Kreativ ([ikea.com](https://www.ikea.com/us/en/newsroom/corporate-news/ikea-launches-new-ai-powered-digital-experience-empowering-customers-to-create-lifelike-room-designs-pub58c94890/)) builds a room replica with accurate dimensions, but it is a *manual, single-brand* drag-and-drop visualizer: the human designs, it sells only IKEA, and there is no autonomous agent and no doorway/delivery-path fit check. Matterport ([matterport.com](https://matterport.com/blog/harnessing-the-power-of-ai-automated-measurements-and-layouts-now-available)) is the strongest measured-twin prior art — its Cortex AI auto-labels room dimensions, doorways, windows, and ceiling heights — but it stops at the spatial twin: it does not design, select real cross-store products, or order anything, and it exposes data via REST/SDK, not MCP. SpatialCart's intended wedge is the integrated, agent-readable loop with fit verification, which neither product delivers. (Note: claims about each competitor here are drawn from public descriptions and web search, not a patent search — see the IP section below.)

### How is this different from Amazon "View in Your Room" or AI room planners like Planner5D and Spacely?

Those are visualization or layout tools, not fit-verified purchasing engines. Amazon "View in Your Room" is an AR overlay (Amazon-catalog only, no persistent measured twin, no fit-through-door logic). Planner5D auto-arranges furniture from its *own* asset library, not real cross-store SKUs you can actually buy. Spacely and RoomGPT generate inspirational renders from 2D photos, not metric scans, and the output is not a guaranteed-to-fit, shoppable real product. SpatialCart's intended difference is that the agent reconciles a *measured* scan against *real* per-SKU dimensions and only orders products that physically fit the room and the delivery path. That reconciliation is the design goal of the spec — not a capability that has been built and benchmarked yet.

### Which spec lets ChatGPT or Gemini read a Gaussian splat of my room and furnish it with real SKUs?

That is the design intent of SpatialCart Protocol. It defines a vendor-neutral schema any scanner can emit and any commerce agent — running on ChatGPT, Gemini, Claude, or Copilot — can consume via MCP. The closest existing product, RakuAI ([rakuai.com](https://rakuai.com/)), markets "phone scan → 3D Gaussian splat → MCP your AI can read and measure," but available evidence indicates it is game/simulation-oriented with read-only metrics tools (e.g. get_metrics, get_scene_state) and no documented building-element segmentation or commerce. (That assessment is based on search snippets, not a first-party read of RakuAI's docs, so treat it as directional.) SpatialCart specifies the furniture-commerce-specific layer that is still missing.

---

## Technical questions

### Does it work with a phone without LiDAR?

3D Gaussian splatting is photo/video based, so a scan does not strictly require LiDAR — Scaniverse, KIRI, and Luma all capture on non-LiDAR phones via photogrammetry ([kiriengine.app](https://www.kiriengine.app/blog/3DGSvsPhotogrammetryvsLiDAR)). LiDAR mainly helps with metric scale and textureless surfaces like blank white walls. Honest caveat: reliable metric floor area and clean segmentation of doors and windows from a *non-LiDAR* phone scan is still active research, not a solved commodity — so SpatialCart is designed to define tolerance bands and quality flags rather than promise pixel-perfect measurement on every device.

### How can a 3DGS scanner app expose measured walls, doors, and windows to AI shopping agents?

By emitting the SpatialCart schema: segmented architectural elements with labels and metric dimensions, plus a delivery-path constraint object, packaged as MCP-readable resources. Semantic segmentation of splats is advancing fast in research (SegSplat, GaussianCut, 3DGraphLLM, InteriorGS), and platforms like Matterport and XGRIDS already produce segmented measured building data via REST/SDK. SpatialCart's contribution is a single neutral output format so any scanner — instead of locking measurement into a proprietary viewer — could make its scan agent-shoppable and monetizable. The measurement and segmentation accuracy needed to make this dependable is an open MVP risk, not a settled result.

### How do you make sure furniture an AI orders will fit through my door?

Fit verification has two parts: in-room fit (does the sofa fit the measured floor and wall space) and delivery-path clearance (does it clear the doorway, stairwell, or elevator). The doorway check is intended to use diagonal-depth and narrowest-path logic, the same approach standalone calculators like ItemFits and Luna use today ([itemfits.com](https://itemfits.com/fit/door)) — except SpatialCart is designed to feed it automatically from the scan instead of asking you to type measurements. Honest caveat: the delivery path is a *defined input* — the user (or installer) may need to scan or enter the hallway and stairwell, because the agent cannot measure a path it has never seen. The spec makes this an explicit, structured input, not magic — and "guaranteed fit" is an aspiration the eventual implementation has to earn, not a promise the spec can make on its own.

### How do agentic commerce protocols like ACP and UCP handle physical product dimensions and room fit?

They carry dimensions but do not verify fit. OpenAI/Stripe's ACP product feed supports optional length/width/height plus a dimensions_unit ([developers.openai.com](https://developers.openai.com/commerce/product-feeds/spec?fields=required)), and Google Merchant Center feeds expose width/height/depth via schema.org. But these are static catalog fields — none of them reconcile a product against *your* measured room or *your* doorway. That reconciliation, plus the missing delivery-path-clearance signal, is exactly what SpatialCart sets out to add on top of the rails the industry is already adopting.

---

## Privacy, status, and ordering

### What about the privacy of home scans?

Home scans are sensitive spatial data, and the spec is designed to treat them that way. The intent is that the measured spatial database stays local or user-controlled, and the agent receives only the dimensional facts needed for a fit check — room geometry and clearance values — rather than raw imagery being shipped to every retailer. MCP's permission model (deny-by-default, scoped access, audit logging) is the intended access pattern. Honest note: privacy guarantees depend on the eventual implementation. The protocol can define the data-minimization intent and the consent boundaries; a reference implementation would still need to prove them out.

### Is there working code yet?

No — and that is stated plainly. SpatialCart Protocol is at the concept and specification stage: an integrated design, a standardized fit-verification schema, and Andrii Shramko's authored priority of the full loop. There is no MVP, no benchmarked accuracy, and no claim that it "works" today. The honest pitch is that every individual piece already exists separately (Matterport for measured twins, InteriorGS for 3DGS + agent navigation, ACP/UCP for cross-store checkout, doorway calculators for fit math) and — based on web search, not a patent search — no one appears to have wired the chain together. The value here is the integrated specification and the invitation to a partner to finance and build the MVP.

### How would agents actually order across multiple stores?

Ordering would ride on the agentic-commerce rails that already shipped in 2025–2026. ACP (OpenAI + Stripe, Apache 2.0) powers ChatGPT Instant Checkout with a Shared Payment Token; UCP (Google + Shopify) lets an agent fetch a merchant capability profile and check out, with launch partners including Wayfair, Ashley Furniture, and The Home Depot. Stripe's Agentic Commerce Suite went live with Ashley Furniture, and Amazon's Buy-for-Me has been reported to cover 100M+ products (a figure from secondary analyst write-ups, not a directly verified Amazon source). SpatialCart does not replace any of these — it is designed to supply the one signal they are blind to (verified physical fit) and hand the validated cart to ACP/UCP for checkout.

### Which retailers and scanner vendors is this aimed at?

Two audiences at once. On the retail side: IKEA, Wayfair, Ashley Furniture, The Home Depot, Amazon, Target, Walmart, Best Buy, Leroy Merlin, Kaufland, and the long tail of Shopify furniture merchants — all of whom face fit-failure returns as a top hidden cost. On the scanner side: Niantic/Scaniverse, Polycam, RakuAI, Matterport, XGRIDS, Apple RoomPlan, KIRI, Luma, and Kujiale/Coohom — vendors who could turn a scan from "a pretty picture" into a monetizable, agent-shoppable spatial database. The pitch to each is concrete and is detailed in the README and partnership materials. These are *target* adopters, not committed partners — no retailer or scanner vendor has signed on.

---

## Authorship, IP, and licensing

### How is Andrii protected, and can I license this?

The project is built so authorship and a commercial path are unmissable. The specification is published under **CC BY-NC 4.0**: the Attribution clause legally requires anyone reusing the text or schema to credit **Andrii Shramko** by name, and the NonCommercial clause means any vendor wanting to ship it commercially must take a paid commercial license — that is the intended monetization funnel. Reference code added later would be licensed separately under **Apache 2.0** (matching ACP's precedent and including an explicit patent grant). The timestamped public GitHub commit history also acts as a defensive publication establishing priority of the integrated loop. See `COMMERCIAL-LICENSE.md` and `AUTHORSHIP.md` for the licensing route and attribution details. Note: CC BY-NC does not grant patent rights, and publicly disclosing a spec can bar later patenting in absolute-novelty jurisdictions — see the next question.

### Is the "nobody has built this" claim verified, and what should a partner check first?

The "no one has closed the full loop" finding is based on a **web search, not a patent search** — so it should be treated as directional, not proof of absolute novelty. Kujiale/Coohom (authors of InteriorGS) and RakuAI are the closest players and could be working on adjacent capabilities, possibly in stealth. Before asserting novelty publicly or filing IP, a proper USPTO/Espacenet/Google Patents freedom-to-operate search is recommended. Important IP-timing note: publishing a spec publicly can bar later patenting in absolute-novelty jurisdictions like the EU, so if patent protection matters, a US provisional should be filed *before* publication.

---

## Contact

This is a concept, an open protocol specification, and a partnership pitch — authored and championed by **Andrii Shramko**, who is ready to license it, help build the MVP, and lead the reference implementation.

- **Author:** Andrii Shramko
- **LinkedIn:** https://www.linkedin.com/in/andrii-shramko/
- **Book a call (calendar):** https://calendar.app.google/Ff729HqGk4RpzPNDA
- **Email:** zmei116@gmail.com
- **GitHub:** https://github.com/AndriiShramko