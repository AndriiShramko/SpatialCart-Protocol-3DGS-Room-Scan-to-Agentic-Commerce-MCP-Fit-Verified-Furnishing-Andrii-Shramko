# SpatialCart Protocol — The Spatial MCP Specification

> **The technical heart of SpatialCart Protocol.** This document defines *Spatial MCP*: a proposed, open, vendor-neutral [Model Context Protocol](https://github.com/modelcontextprotocol) interface that a 3D-scan-hosting service would expose to AI agents — so that an agent could **measure your real space from a phone 3D Gaussian-splatting room scan, verify that a real product fits the room AND the delivery doorway, and then place the order across stores** via agentic-commerce rails (ACP / UCP).
>
> **Authored by Andrii Shramko.** This is a *specification and concept at the design stage* — an open proposal others can implement. **There is no working reference server, and no benchmarked accuracy. Nothing here ships today.** Where a capability depends on unproven research, or on data the agent cannot obtain by itself, this document says so plainly. Read [§12 Honesty & Open Problems](#12-honesty-status-and-open-problems) before quoting any capability as "available."

---

## Table of contents

1. [What this protocol is (and is not)](#1-what-this-protocol-is-and-is-not)
2. [Where Spatial MCP sits in the stack](#2-where-spatial-mcp-sits-in-the-stack)
3. [The core idea: a measured spatial database](#3-the-core-idea-a-measured-spatial-database)
4. [The segmentation database: entities & attributes](#4-the-segmentation-database-entities--attributes)
5. [Metric scale: the hard dependency](#5-metric-scale-the-hard-dependency)
6. [Spatial MCP — read & measure tools](#6-spatial-mcp--read--measure-tools)
7. [Spatial MCP — fit-verification tools](#7-spatial-mcp--fit-verification-tools)
8. [The external commerce side: store/marketplace MCPs](#8-the-external-commerce-side-storemarketplace-mcps)
9. [The end-to-end agent loop](#9-the-end-to-end-agent-loop)
10. [Coordinate system, units & conventions](#10-coordinate-system-units--conventions)
11. [Security, permissions & privacy](#11-security-permissions--privacy)
12. [Honesty, status, and open problems](#12-honesty-status-and-open-problems)
13. [Authorship, license & how to get involved](#13-authorship-license--how-to-get-involved)

---

## 1. What this protocol is (and is not)

**The question this answers:** *How can an AI agent measure my room from a phone scan and order furniture that actually fits — in the room and through the door?* Today, it cannot — because the connective layer is missing.

**The problem.** Two technology stacks already exist but have never been wired together:

1. **Phone-captured 3D Gaussian-splatting (3DGS) room scans.** Scaniverse, Polycam, KIRI Engine, Luma AI and Apple RoomPlan let anyone turn a few minutes of phone capture into a 3D reconstruction of a real room. (See [State of Gaussian Splatting 2026](https://www.thefuture3d.com/blog/state-of-gaussian-splatting-2026/), [Scaniverse dev blog](https://dev.scaniverse.com/news/creating-splats-which-app-to-choose), [Apple RoomPlan](https://developer.apple.com/augmented-reality/roomplan/).)
2. **Agentic commerce rails.** The [Agentic Commerce Protocol (ACP)](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol) from OpenAI + Stripe, the [Universal Commerce Protocol (UCP)](https://developers.googleblog.com/under-the-hood-universal-commerce-protocol-ucp/) from Google + Shopify, and Amazon's "Buy for Me" let an AI agent actually *check out and pay* at real stores.

The missing connective tissue is a **standardized, measured, machine-readable description of the user's real room** — and, crucially, the **two fit signals the furniture industry openly admits are missing**: *in-room fit* (does the sofa physically fit the measured floor and wall space?) and *delivery-path clearance* (will it fit through the doorway, stairwell, or elevator?). Industry sources independently flag this exact gap — "room-fit and dimensional accuracy is the key differentiation in AI furniture shopping," and "most home-goods catalogs lack room-fit signals like ceiling clearance, stair clearance, and doorway clearance for delivery" ([paz.ai](https://www.paz.ai/for/home-goods-brands), [myhfa.org](https://myhfa.org/blog/how-ai-is-leveling-the-playing-field-for-furniture-retailers-in-2026/)). *(These are vendor/industry-blog observations, treated here as directional signal — not peer-reviewed data.)*

**SpatialCart Protocol — and its Spatial MCP interface — is proposed as that connective tissue.**

| It **IS** | It is **NOT** |
|-----------|---------------|
| An open, versioned, vendor-neutral **data-layer + tool specification** | A competing checkout / payment protocol |
| A proposed **MCP server contract** any scan host could implement | A scanner app, a 3DGS engine, or a finished product |
| A **fit-verification schema**: room geometry + segmented architectural elements + delivery-path constraints | A guarantee of measurement accuracy (that is an MVP risk — see §12) |
| **Complementary** to MCP (data), and to ACP/UCP/AP2 (orchestration & checkout) | A replacement for any of them |

Spatial MCP deliberately **does not handle payment, catalogs, or order orchestration.** It is designed to answer exactly one class of question: *"What does this real room measure, and will this specific object fit — in the room and through the door?"* Everything downstream (find the product, propose a design, take payment, schedule delivery and install) is delegated to **external commerce MCP servers** that already speak ACP/UCP (§8).

---

## 2. Where Spatial MCP sits in the stack

*Is there a protocol that connects 3D Gaussian-splatting scans to agentic commerce checkout?* Not yet — this spec proposes where one would sit:

```
                ┌──────────────────────────────────────────────┐
                │            AI AGENT (the orchestrator)         │
                │   ChatGPT · Gemini · Claude · Copilot · etc.   │
                └───────────────┬───────────────┬───────────────┘
                                │               │
              reads the room    │               │   shops & buys
              (THIS SPEC)        │               │   (existing rails)
                                ▼               ▼
            ┌───────────────────────────┐   ┌──────────────────────────────┐
            │       SPATIAL MCP         │   │     COMMERCE MCP (external)   │
            │  (scan-hosting service)   │   │  Store / marketplace servers  │
            │                           │   │                              │
            │  load_scene · list_rooms  │   │  find_product_by_dimensions  │
            │  get_entity · measure     │   │  get_dimensions              │
            │  get_opening · fit_check  │   │  design_proposal             │
            │  clearance                │   │  place_order_with_delivery_  │
            │  place_and_validate       │   │       install                │
            └───────────┬───────────────┘   └───────────────┬──────────────┘
                        │                                   │
              measured spatial DB                  ACP / UCP / AP2 / MCP
              from a 3DGS phone scan               checkout & payment rails
```

- **MCP** is the de-facto agent-to-tool data layer (built by Anthropic, donated to the Linux Foundation in Dec 2025). Spatial MCP is designed to be *one more MCP server* — nothing exotic.
- **ACP** (Apache-2.0; spec updated 2026-04-17) and **UCP** (announced at NRF 2026) are the orchestration/checkout layers. UCP itself is positioned to "run over MCP." Spatial MCP is intended to feed them the spatial truth they lack.
- **Spatial MCP** is proposed as the new, missing layer: turning a scan host's output from "a pretty splat" into "a queryable, measured, fit-aware spatial database an agent can reason and act against." *To be clear: this layer does not exist yet as open MCP — that is the whole point of publishing the spec.*

---

## 3. The core idea: a measured spatial database

A raw 3D Gaussian splat is, by itself, **not shoppable**: it is millions of view-dependent primitives with no labels and (often) no reliable real-world scale. An agent cannot ask a splat "how wide is the doorway?" any more than it can ask a JPEG. *What is the missing data layer between a 3D room scan and buying furniture online?* This.

Spatial MCP defines the **intermediate artifact** that would make a scan agent-readable: a **Segmentation Database** — a structured, metric, semantic description derived from the scan, containing:

1. **Rooms** with floor area, ceiling height, and footprint polygon.
2. **Architectural elements** — walls, floor, door-openings, windows, radiators, fixtures — each as a labeled entity with metric dimensions, position, orientation, and adjacency.
3. **A navigable / clearance graph** linking rooms through their openings, so a *delivery path* from the building entrance to the target room can be evaluated.

This mirrors what production platforms already produce on the *measurement* side — e.g. Matterport's Model API auto-returns room width/length/height/area and lets users measure windows and doors ([Matterport automated measurements](https://support.matterport.com/s/article/Automated-Rooms-Measurements-and-Property-Report?language=en_US)), and Apple RoomPlan emits parametric walls/doors/windows in USD. **Spatial MCP's proposed contribution is to standardize how such a database is *exposed to an agent as MCP tools*, and to add the fit-verification layer on top.** Based on web research, no vendor today exposes this full chain as open MCP (see §12 — the pieces exist, the wired loop does not).

> **Honesty flag.** Reliable semantic segmentation of doors/windows/radiators and true metric floor area *from a phone 3DGS scan* is **active research, not a shipped commodity** (SegSplat, GaussianCut, 3DGraphLLM, InteriorGS are research code/datasets). This spec defines the *schema and the contract*; achieving the underlying detection accuracy is an explicit, unsolved MVP risk (§12).

---

## 4. The segmentation database: entities & attributes

All entities share a common envelope. Dimensions are **metric (meters)** unless a field says otherwise; every measured value carries a **confidence** and a **source** (`lidar`, `photogrammetry`, `roomplan`, `user_input`, `derived`).

### 4.1 Common entity envelope

```jsonc
{
  "id": "ent_8f2c…",            // stable within a scene
  "type": "room|wall|floor|door_opening|window|radiator|fixture",
  "label": "living_room",       // open-vocabulary semantic label
  "bbox": {                     // axis-aligned, meters, scene frame
    "min": [x, y, z],
    "max": [x, y, z]
  },
  "obb": {                      // oriented bounding box (optional)
    "center": [x, y, z],
    "size":   [w, h, d],
    "yaw_deg": 0.0
  },
  "confidence": 0.0,            // 0..1 detection/measurement confidence
  "source": "lidar|photogrammetry|roomplan|user_input|derived",
  "attributes": { /* type-specific, see below */ },
  "adjacency": ["ent_…"]        // touching/connected entities
}
```

### 4.2 Entity types & type-specific attributes

| Entity | Key attributes (metric unless noted) |
|--------|--------------------------------------|
| **room** | `floor_area_m2`, `ceiling_height_m`, `footprint_polygon` (ordered 2D vertices), `connected_openings[]`, `room_class` (kitchen/bedroom/…) |
| **wall** | `length_m`, `height_m`, `thickness_m?`, `usable_run_m` (longest unobstructed span for placing furniture against it), `normal` (outward direction) |
| **floor** | `area_m2`, `level_z_m`, `surface` (hardwood/carpet/tile?) — informational |
| **door_opening** | `clear_width_m`, `clear_height_m`, `swing` (in/out/sliding/none), `threshold_height_m?`, `hinge_side?`, `is_exterior` (bool) |
| **window** | `width_m`, `height_m`, `sill_height_m`, `orientation` (compass bearing if geo-anchored) |
| **radiator** | `width_m`, `height_m`, `depth_m`, `standoff_from_wall_m`, `wall_id` — a **placement obstacle** the agent must route furniture around |
| **fixture** | generic: built-ins, columns, vents, outlets, light fittings — each an obstacle or constraint with `bbox`/`obb` |

### 4.3 Geo-anchored attributes (optional)

If the scan host knows the scene's geolocation and true-north orientation, it MAY expose **sun-exposure** signals so an agent can answer "which wall gets afternoon sun?" (relevant for fabric fade, plant placement, screen glare):

```jsonc
"geo": {
  "lat": 0.0, "lon": 0.0,
  "true_north_yaw_deg": 0.0,
  "window_sun_exposure": [
    { "window_id": "ent_…", "bearing_deg": 187, "direct_sun_hours_est": 4.5 }
  ]
}
```

> Geo-anchoring is **opt-in and privacy-sensitive** (§11). Most scans will omit it.

---

## 5. Metric scale: the hard dependency

**Everything in this protocol is metric-scale dependent.** Fit verification is meaningless if the scene's units are unknown or wrong by 10%.

A conforming Spatial MCP server MUST declare, per scene, how scale was established:

- `lidar` — captured on a LiDAR-equipped device (iPhone/iPad Pro, dedicated scanner). Strongest metric ground truth.
- `marker` — a known-size fiducial (e.g. a printed marker, a credit-card-sized reference, a standard A4 sheet, or a labeled doorway) placed in the scene to fix scale.
- `roomplan` — Apple RoomPlan / ARKit, which produces metric parametric geometry.
- `photogrammetry_estimated` — scale inferred without LiDAR or a marker. **Lowest confidence; MUST be flagged**, and `fit_check` results derived from it MUST carry reduced confidence and a wider tolerance band.

```jsonc
"scale": {
  "method": "lidar|marker|roomplan|photogrammetry_estimated",
  "meters_per_unit": 1.0,
  "confidence": 0.0,
  "reference": "marker A4 @ ent_… | device LiDAR | …"
}
```

> **Honesty flag.** Metric scale from non-LiDAR phones is imperfect, and this spec does not solve that. Its answer is *not* to pretend otherwise but to make scale **explicit, sourced, and confidence-tagged**, and to require fit logic to propagate that uncertainty (§7). When in doubt, the agent should ask the user to add a marker or scan on a LiDAR device.

---

## 6. Spatial MCP — read & measure tools

These tools are designed to let an agent **load a scene and interrogate its geometry.** All are read-only and side-effect-free. (Naming follows MCP tool conventions; payloads are illustrative, not final, and no server implements them today.)

### `load_scene`
Load a hosted scan into an agent-readable session and return scene metadata.
```jsonc
// in
{ "scene_uri": "spatialcart://host/scene/abc123", "auth": "…" }
// out
{ "scene_id": "abc123", "scale": { … }, "room_count": 3,
  "bounds": { "min": [..], "max": [..] }, "geo": { … }|null,
  "capabilities": ["segmentation","fit_check","delivery_path","geo"] }
```

### `list_rooms`
Enumerate rooms with summary metrics.
```jsonc
// out
{ "rooms": [ { "id": "ent_room1", "label": "living_room",
              "floor_area_m2": 23.4, "ceiling_height_m": 2.62,
              "openings": ["ent_door1","ent_door2"],
              "confidence": 0.82 } ] }
```
*(Illustrative values, not measurements from a real scan.)*

### `get_entity`
Fetch one entity (any type) by id, with full attributes and adjacency.
```jsonc
// in
{ "entity_id": "ent_door1" }
// out
{ /* full common envelope + type-specific attributes from §4 */ }
```

### `measure`
Measure between two points / entities, or query a derived quantity. The agent supplies what it wants measured; the server returns a value **with confidence and the scale source**.
```jsonc
// in
{ "scene_id": "abc123",
  "query": { "kind": "distance|free_floor_span|wall_run|clearance_height",
             "from": "ent_wallA", "to": "ent_wallB" } }
// out
{ "value_m": 3.18, "confidence": 0.79, "scale_source": "lidar" }
```

### `get_opening`
Return the constraining geometry of a door/opening/stair/elevator as a **passage** — the unit the delivery-path logic consumes.
```jsonc
// in  { "opening_id": "ent_door_front" }
// out
{ "id": "ent_door_front", "kind": "door|stair|elevator|hallway_turn",
  "clear_width_m": 0.81, "clear_height_m": 2.03,
  "swing": "in", "is_exterior": true,
  "turn_radius_m": 1.1,        // for hallway turns / landings
  "confidence": 0.74 }
```

---

## 7. Spatial MCP — fit-verification tools

This is the **unique wedge of SpatialCart**: standardizing the two signals the industry admits are missing — answering *How do I make sure furniture an AI orders will fit through my door?* These tools are designed to take a candidate object's **dimensional fit envelope** (length × width × height, plus optional rigidity / legs-removable flags) and reconcile it against the measured scene.

### `fit_check` — does it fit the room *and* the delivery path?
The headline tool. It runs **two independent checks** and returns both, so an agent never confuses "fits the room" with "fits through the door."

```jsonc
// in
{ "scene_id": "abc123",
  "target_room": "ent_room1",
  "object": {
    "sku": "store://wayfair/sofa-XYZ",         // optional provenance
    "dims_m": { "length": 2.18, "width": 0.95, "height": 0.88 },
    "diagonal_depth_m": 1.12,                   // narrowest-pass diagonal
    "rigid": true, "legs_removable": false
  },
  "delivery_path": {
    // ordered passages from building entrance to target room.
    // MAY be auto-derived from the scene's opening graph,
    // OR supplied by the user when the path was not scanned.
    "passages": ["ent_door_front","ent_stair_1","ent_door_room1"],
    "source": "scene_graph|user_input|mixed"
  } }
// out
{ "in_room_fit": {
    "fits": true,
    "placements_possible": 2,
    "limiting_constraint": "free_floor_span",
    "margin_m": 0.31, "confidence": 0.80 },
  "delivery_path_fit": {
    "fits": false,
    "blocking_passage": "ent_door_front",
    "blocking_reason": "diagonal_depth 1.12m > door clear_width 0.81m",
    "suggestion": "remove legs OR choose width<=0.79m OR confirm window delivery",
    "confidence": 0.71 },
  "overall": "blocked_on_delivery" }
```
*(Illustrative payload — the numbers show the intended logic, not a real result.)*

**Delivery-path logic** is intended to use the standard *diagonal-depth + narrowest-path* method that manual furniture-fit calculators already use ([Luna fit calculator](https://www.lunafurn.com/pages/furniture-fit-calculator), [ItemFits doorway](https://itemfits.com/fit/door)) — but fed **automatically** from the scan's opening graph instead of hand-typed numbers.

> **Honesty flag.** A delivery path can only be verified for passages that were actually scanned. If the hallway, stairwell, or elevator was not captured, `delivery_path.source` MUST be `user_input` (or `mixed`) and the result confidence MUST reflect that. **This is a defined input, not magic** (§12).

### `clearance` — explicit single-passage check
A lower-level helper: does an object pass a single opening, accounting for swing, turn radius, and threshold?
```jsonc
// in  { "opening_id": "ent_stair_1", "dims_m": {…}, "diagonal_depth_m": 1.12 }
// out { "passes": false, "limiting": "turn_radius",
//       "required_m": 1.30, "available_m": 1.10, "confidence": 0.70 }
```

### `place_and_validate` — try a concrete placement (read-only simulation)
Given a proposed position/orientation in a room, validate that it does not collide with walls, radiators, fixtures, or other already-placed items, and report remaining circulation space.
```jsonc
// in
{ "room_id": "ent_room1",
  "object": { "dims_m": {…} },
  "pose": { "position": [x,y], "yaw_deg": 90, "against": "ent_wallA" },
  "existing": [ /* other placed objects */ ] }
// out
{ "valid": true, "collisions": [], "wall_gap_m": 0.02,
  "circulation_ok": true, "walkway_min_m": 0.74, "confidence": 0.78 }
```

> `place_and_validate` is intended as a **validation/simulation** call, not a purchase. The agent loops over candidate SKUs and placements until it has a design that passes both `fit_check` checks, *then* — and only then — hands off to commerce.

---

## 8. The external commerce side: store/marketplace MCPs

Spatial MCP **stops at fit.** To find, design with, and buy real products, the agent calls **separate commerce MCP servers** operated by stores/marketplaces — the ones already emerging under ACP and UCP. SpatialCart does **not** define these; it defines the **handshake** so a fit-aware agent can drive them. The recommended commerce-side tool surface (which retailers could map onto their existing ACP/UCP feeds and Shopify Storefront MCP endpoints):

### `find_product_by_dimensions`
Search a store's catalog constrained by the fit envelope the agent derived from the scan. This is the inversion that today's catalogs make hard: *search by the space, not by the keyword.*
```jsonc
// in
{ "category": "sofa",
  "max_dims_m": { "length": 2.30, "width": 0.98, "height": 0.95 },
  "max_diagonal_depth_m": 0.79,        // <- delivery doorway constraint!
  "style": "mid-century", "budget": { "max": 1200, "currency": "USD" } }
// out { "products": [ { "sku": "…", "dims_m": {…}, "price": {…} } ] }
```
ACP's Product Feed already supports optional `length`/`width`/`height` + `dimensions_unit`, and Google Merchant Center exposes width/height/depth via schema.org/Product — so the dimensional data needed here **already has a home in existing feeds** ([ACP feed spec](https://developers.openai.com/commerce/product-feeds/spec?fields=required)).

### `get_dimensions`
Fetch authoritative per-SKU dimensions (and, ideally, the store's own delivery-clearance signals: requires-stairs flag, assembled vs boxed dimensions, legs-removable).
```jsonc
// in  { "sku": "store://…/sofa-XYZ" }
// out { "assembled_m": {…}, "boxed_m": {…}, "legs_removable": true,
//       "ships_assembled": false, "weight_kg": 41 }
```

### `design_proposal`
Optional: ask the store/agent to assemble a coherent multi-SKU room design within the measured constraints and budget — every item already passed through `fit_check`.

### `place_order_with_delivery_install`
The checkout handoff. This is designed to ride **existing rails** — ACP's Agentic Checkout + Delegate Payment (Stripe Shared Payment Token), or UCP's Checkout capability — and add the delivery/installation parameters the spatial context produced.
```jsonc
// in
{ "cart": [ { "sku": "…", "qty": 1 } ],
  "delivery": { "path_verified": true, "white_glove": true,
                "install_required": true,
                "notes": "front door 0.81m; remove sofa legs on arrival" },
  "payment": { "token": "spt_…" } }   // scoped token, never raw card
```
**Spatial MCP issues no payments.** Payment is a scoped, merchant-bound token handled entirely by the commerce rail (ACP Shared Payment Token, UCP/AP2, Visa/Mastercard agent credentials).

---

## 9. The end-to-end agent loop

*Which spec lets ChatGPT or Gemini read a Gaussian splat of my room and furnish it with real SKUs?* This proposes how an agent would string the two MCP sides together — **none of this runs yet; it is the intended flow:**

1. **Capture.** User scans the room on a phone (Scaniverse/Polycam/KIRI/RoomPlan); the scan host produces a Segmentation Database and exposes it via Spatial MCP.
2. **Load & understand.** Agent calls `load_scene` → `list_rooms` → `get_entity`/`measure` to learn the room's true geometry, walls, openings, radiators.
3. **Derive constraints.** Agent computes the **fit envelope** (free floor span, wall runs, ceiling height) and the **delivery constraint** (narrowest passage from `get_opening` along the path).
4. **Shop by space.** Agent calls the store's `find_product_by_dimensions` with those constraints — searching by the space, not by guesswork.
5. **Verify fit.** For each candidate, `get_dimensions` → Spatial MCP `fit_check` (in-room **and** delivery path) and `place_and_validate`. Reject anything that fails either check.
6. **Design.** Agent (optionally via `design_proposal`) assembles a coherent, all-fits room within budget.
7. **Confirm with the human.** Present the design, the placements, and any caveats (low-confidence scale, unscanned delivery path).
8. **Order.** `place_order_with_delivery_install` over ACP/UCP, with delivery/install parameters informed by the spatial truth. Payment via scoped token on the commerce rail.

The intended result: **fit-verified, lower-return, high-intent agentic furniture purchases** — the segment everyone wants and, per this research, nobody has wired up yet.

---

## 10. Coordinate system, units & conventions

- **Units:** meters for length, square meters for area, degrees for angles. Weight (commerce side) in kg. A server MAY accept/emit imperial on request but MUST store and reason in SI.
- **Frame:** right-handed; **+Z up** (floor plane at the room's `level_z_m`). Yaw measured clockwise from scene +X; if geo-anchored, `true_north_yaw_deg` relates scene-X to north.
- **Polygons:** footprints are ordered vertex lists in the floor plane (2D), counter-clockwise = interior.
- **Confidence:** every measured numeric value carries `0..1` confidence; agents SHOULD treat anything below a host-declared threshold as "ask the user / re-scan."
- **Tolerance bands:** `fit_check` results SHOULD include a `margin_m` and propagate scale uncertainty — a "fits" with a 2 cm margin on photogrammetry-estimated scale is **not** the same as a "fits" with a 30 cm margin on LiDAR.
- **Versioning:** every payload carries a `spec_version` (date-stamped, e.g. `2026-06-27`), following the ACP/UCP precedent of clean, versioned, vendor-neutral schemas.

---

## 11. Security, permissions & privacy

A room scan of someone's home is **sensitive personal data.** Spatial MCP is designed to adopt MCP's deny-by-default posture:

- **Scoped access.** `load_scene` requires an explicit, revocable grant for a specific scene. No ambient "read all my scans."
- **Read-only by default.** Every tool in §6–§7 is side-effect-free. The only state-changing call in the loop (`place_order_with_delivery_install`) lives on the **commerce** side, behind the user's payment authorization.
- **No payment surface here.** Spatial MCP never sees card data; payment is a scoped, merchant-bound token issued by the commerce rail.
- **Geo & PII opt-in.** Geolocation/sun-exposure and any recognizable contents are opt-in and SHOULD be minimized or excluded from what the agent receives.
- **Audit log.** Conforming hosts SHOULD keep a full audit trail of which agent loaded which scene and which measurements were read (mirroring the deny-by-default + audit pattern already described by early phone-scan MCP products).

---

## 12. Honesty, status, and open problems

**This is a specification and a pitch, not a shipping product.** Per the author's own discipline ("never report done/works on unproven things"), here is the unvarnished status:

- **No reference server exists yet.** The tool surface above is a *proposed contract*, not running code. No accuracy has been benchmarked. Every JSON payload in this document is illustrative.
- **Segmentation is research, not a commodity.** Reliably labeling door-openings, windows, and radiators and returning true metric floor area *from a phone 3DGS scan* is active research (SegSplat, GaussianCut, 3DGraphLLM, InteriorGS, ScanNet++/ScanNet benchmarks). Production-grade segmented measurement today comes from Matterport (REST/SDK, mesh-based, enterprise tier) and XGRIDS (BIM/Revit plugin) — **neither exposes it as open MCP.** The closest "phone-scan → splat → MCP" product (RakuAI) appears game/simulation-oriented with read-only metrics, and **no public evidence confirms it segments architectural elements** — exactly the gap this spec targets. *(That RakuAI characterization comes from search snippets behind access blocks, not first-party reads — treat it as directional and re-verify.)*
- **Metric scale is imperfect** without LiDAR or a marker. The spec makes scale explicit and confidence-tagged (§5) rather than pretending it is solved.
- **Delivery-path verification needs the path to be captured or entered.** Unscanned stairs/elevators are a *defined user input*, not an inference (§7).
- **"Nobody has the full loop" is from web research, not a patent search.** A proper USPTO / Espacenet / Google Patents freedom-to-operate search is recommended before asserting novelty publicly. Kujiale/Coohom (authors of InteriorGS) and RakuAI are the closest watchers and could be in stealth.
- **This does not compete with ACP/UCP.** It *depends* on them. Positioning it as a rival checkout protocol would alienate the very rails it rides.
- **Standards win by adoption, not elegance.** A solo-authored spec with no reference implementation and no consortium has weak gravity. The realistic path is one scanner vendor + one retailer co-piloting this, not organic standardization.

If you can only remember one sentence: **the integrated design, the fit-verification schema, and Andrii Shramko's authorship/priority of the wired loop are the assets — the measurement accuracy is the MVP risk to be financed and proven.**

---

## 13. Authorship, license & how to get involved

**Conceived and authored by Andrii Shramko.** SpatialCart Protocol is published as an open, vendor-neutral specification so that scanner / 3DGS-software makers can make their output agent-shoppable, and retailers / marketplaces can capture fit-verified, lower-return agentic furniture sales.

**License (intended).** The specification, schema, and whitepaper text are intended to be released under **CC BY-NC 4.0** — open to read and evaluate, attribution to *Andrii Shramko* required, and commercial shipping requires a commercial license (the path to a partnership conversation). Any future reference / example code is intended to be licensed separately under **Apache 2.0**, matching ACP's precedent and its explicit patent grant. See `LICENSE`, `COMMERCIAL-LICENSE.md`, and `AUTHORSHIP.md` in the repository.

> **IP timing note (honest):** publicly disclosing a spec can bar later patenting in absolute-novelty jurisdictions. If patent protection matters, file a US provisional **first**, then publish. If not, this public, timestamped spec is itself a strong **defensive publication** establishing priority of the integrated loop.

**Andrii is passionate about building this and ready to lead a team.** He is available to (a) license the spec, (b) co-found or advise a scanner vendor or retailer building the MVP, and (c) lead the reference implementation. If your scans end at a pretty picture, or your agentic checkout is blind to physical space — **this is the proposed missing layer, and the author is reachable.**

### Contacts

- **Author:** Andrii Shramko
- **LinkedIn:** https://www.linkedin.com/in/andrii-shramko/
- **Book a call (calendar):** https://calendar.app.google/Ff729HqGk4RpzPNDA
- **Email:** zmei116@gmail.com
- **GitHub:** https://github.com/AndriiShramko



---

*SpatialCart Protocol · Spatial MCP specification · authored by Andrii Shramko · concept/spec stage, no working server — see [§12](#12-honesty-status-and-open-problems) for status. Spec version 2026-06-27.*