# Closing the Loop: Relocalization-Anchored AR Preview (Draft Extension)

**Status: draft extension to the SpatialCart Protocol. Nothing here runs yet; every step is design intent, grounded in the cited prior work.**
Proposed by Andrii Shramko, 2026-07-24.

---

## The missing step

The core SpatialCart loop ends with the agent presenting a fit-verified design and the user confirming ([PROTOCOL.md §9](PROTOCOL.md)). But "confirm from a list" is a weak confirmation for a physical object. The strong confirmation is:

> **The agent verified it fits — and your phone shows the product standing at exactly the planned spot in your real room.**

This is possible **without GPS and without ad-hoc AR placement**, because the room was already scanned. The same pre-scan that gives SpatialCart its measured spatial database can also serve as a **visual localization map**: the phone sends a camera frame, gets back its 6DoF pose *in the scan's coordinate frame*, and renders the product model at the pose the agent planned — in that same frame. One coordinate system from plan to preview.

Open-source prior work proves each ingredient:

- **Photo → pose, no GPS.** In the OSCP GeoPoseProtocol, geolocation is an *optional* request field ([spec][gpp]); OpenVPS's MapLocalizer localizes from image + intrinsics alone ([code][ovps-main]), and without a geo-alignment transform it still returns a metrically valid pose in a local frame ([code][ovps-hloc]). OpenVPS is MIT-licensed (© 2025 Nokia, built for Open AR Cloud) ([repo][ovps]).
- **Indoor accuracy is sufficient.** HLoc-class localization (SuperPoint + SuperGlue) achieves ~2–5 cm / <1.5° median error on room-scale benchmarks (7-Scenes) ([HLoc][hloc]).
- **One capture, two artifacts.** OpenVPS builds its maps from StrayScanner-style posed RGB-D walkthroughs ([format][stray]) — the same walkthrough that produces the 3DGS scan and the segmentation database.
- **Map-frame ↔ AR alignment is solved.** The spARcl WebXR client (MIT, Open AR Cloud) already implements aligning a VPS-returned pose with the phone's local WebXR tracking ([code][sparcl-loc]), using WebXR Raw Camera Access (Android Chrome) ([chrome][rawcam]).
- **Splats render on phones.** If the preview wants the scan itself on screen (not just camera passthrough), MIT-licensed web splat renderers exist (Spark ([repo][spark]), SuperSplat viewer ([repo][ssplat])).

## The extended loop

Steps 1–6 below extend PROTOCOL.md §9; *proven* = demonstrated by cited existing tech, *to build* = new glue.

1. **One scan → two artifacts.** The walkthrough capture produces (a) the 3DGS scan + segmentation database and (b) a localization map. *Proven* ([ovps], [stray]); *to build:* emitting both from one capture session.
2. **Agent plans in the scan frame.** `fit_check` / `place_and_validate` already reason in scene coordinates; the planned placement pose is the artifact to export. *Proven* (this spec); *to build:* `get_ar_anchor` (below).
3. **Product model.** Preferred: retailer-supplied GLB/USDZ — commerce 3D pipelines mandate real-world scale ("1 glTF unit = 1 m") ([Scene Viewer][sceneviewer], [Khronos ACG 2.0][khronos]). Fallback: image-to-3D generation (e.g. Tripo, Meshy, Rodin — Rodin accepts an explicit target bounding box ([api][rodin])), then uniform rescale to the retailer-declared dimensions. Model provenance must be disclosed in the UI.
4. **Phone relocalizes — no GPS.** Camera frame → GeoPoseProtocol request → 6DoF pose in the map frame. *Proven* ([gpp], [ovps-main], [ovps-hloc]); *to build:* an explicit `frame: local` convention for indoor spaces (today OpenVPS silently falls back to a (0,0,0) geodetic reference) and per-space service resolution (the localizer URL bound to the scene, not discovered via GPS).
5. **AR shows the product at the planned pose.** spARcl-style alignment maps the localized pose into WebXR space; the product GLB renders at the planned placement; local SLAM tracks between relocalizations. *Proven* ([sparcl-loc], [rawcam]); *to build:* the scan-frame convention and an iOS path (WebXR immersive AR is still unavailable in Safari; native wrapper or App Clip required ([state of iOS WebXR][ioswebxr])).
6. **Confirm → order.** Unchanged (ACP/UCP). The fit verdict always comes from the measured database — **the AR layer visualizes; it never decides.**

## Proposed spec additions

- **`localization_map` artifact** in the scene schema, alongside the splat and the segmentation database — including retained **posed capture frames** (images + poses + intrinsics), since both HLoc-style maps and newer 3DGS-native localizers are built from them, not from the splat alone.
- **Frame convention:** one metric scan frame per scene, 1 unit = 1 m (matching glTF/AR conventions), with `frame: local` explicit for non-geo-anchored indoor scenes.
- **`get_ar_anchor` tool** (or an extension of `place_and_validate`): returns `{ product_id, model_ref, model_provenance: "vendor" | "generated", declared_dims_m, pose_in_scan_frame, fit_verdict }` — everything an AR client needs after relocalizing.

## Upgrade path: the splat as its own localization map

A fast-moving research line localizes cameras *directly* in 3DGS scenes — which would collapse "two artifacts" back into one: STDLoc (CVPR 2025, MIT) ([repo][stdloc]), ULF-Loc (CVPR 2026) ([arxiv][ulfloc]), SplatLoc (TVCG 2025) ([repo][splatloc]), GS-CPR render-and-refine (ICLR 2025) ([repo][gscpr]), 6DGS (ECCV 2024) ([page][6dgs]). All currently need per-scene preparation from posed frames and server-side GPUs — hence the pragmatic near-term stack remains HLoc-style maps, with 3DGS-native localization as the upgrade path.

## Why this is different from existing AR shopping

Mainstream AR preview (IKEA Place, Amazon "View in Your Room", Wayfair, Snap True Size, Google Scene Viewer) anchors **ad-hoc via plane detection at view time**: the user drags the model around, and nothing persists between sessions. IKEA Kreativ keeps a persistent measured room replica, but as a photo canvas — not live AR relocalized into a saved frame. VPS-grade persistence exists as infrastructure (Niantic Scaniverse/VPS, ARCore Persistent Cloud Anchors) but, per this research, **no shipped product or paper connects persistent scan-frame indoor anchoring with fit-verified shopping preview**. That connection — *plan in the scan, see it in the room* — is the contribution proposed here.

## Honest limits

- **Error stack-up.** Scan-scale error + relocalization error (cm-level in good maps; worst on height) + model-scale error compound. That is why the purchase verdict stays with the measured database; AR is presentation only.
- **Generated models are approximations.** Unseen sides are hallucinated; thin structures (chair legs, rattan, lamp stands) are a known failure mode; materials are a plausible guess. The UI must state "approximate visualization — dimensions per retailer."
- **Uniform rescale matches declared dimensions on one axis exactly** only if generated proportions match declared ones; declared dimensions often include protrusions the mesh renders differently.
- **iOS** has no WebXR immersive AR in Safari; Android Chrome first, iPhone via native wrapper.
- **Server-side GPU required** for map building and localization; map preparation takes minutes, not seconds.
- **Privacy:** a home localization map is sensitive by nature — per-user access control, and raw query photos must not be retained (see PROTOCOL.md §11).

## References

[gpp]: https://github.com/OpenArCloud/oscp-geopose-protocol
[ovps]: https://github.com/OpenArCloud/openvps
[ovps-main]: https://github.com/OpenArCloud/openvps/blob/main/maplocalizer/server/main.py
[ovps-hloc]: https://github.com/OpenArCloud/openvps/blob/main/maplocalizer/server/hloc_localizer.py
[hloc]: https://github.com/cvg/Hierarchical-Localization
[stray]: https://github.com/strayrobots/scanner/blob/main/docs/format.md
[sparcl-loc]: https://github.com/OpenArCloud/sparcl/blob/main/src/core/locationTools.ts
[rawcam]: https://groups.google.com/a/chromium.org/g/blink-dev/c/LoCH4tthbsI
[spark]: https://github.com/sparkjsdev/spark
[ssplat]: https://github.com/playcanvas/supersplat-viewer
[sceneviewer]: https://developers.google.com/ar/develop/scene-viewer
[khronos]: https://www.khronos.org/blog/introducing-asset-creation-guidelines-2.0-siggraph-2025
[rodin]: https://fal.ai/models/fal-ai/hyper3d/rodin
[stdloc]: https://github.com/zju3dv/STDLoc
[ulfloc]: https://arxiv.org/abs/2605.04730
[splatloc]: https://github.com/zhaihongjia/SplatLoc
[gscpr]: https://github.com/XRIM-Lab/GS-CPR
[6dgs]: https://mbortolon97.github.io/6dgs/
[ioswebxr]: https://launch.variant3d.com/blog/23-06-state-webxr-on-ios-beyond

*Related open ecosystems: [Open AR Cloud / OSCP](https://github.com/OpenArCloud/OSCP-Docs) (GeoPose, discovery, spARcl), [SpatialDDS](https://spatialdds.org/) (blob-reference and discovery patterns this spec intends to align with).*
