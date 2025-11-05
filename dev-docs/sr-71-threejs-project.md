# SR-71 Blackbird Immersive Digital Twin

## Project Overview
- Deliver a photoreal, browser-based SR-71 experience centered on the aircraft itself: exterior inspection, cockpit immersion, and high-fidelity flight physics that let users feel how the Blackbird behaves at speed and altitude.
- Prioritize a 1:1 cockpit recreation with functioning instrumentation, switchology, and mission-critical workflows that map to authentic pilot/RSO procedures.
- Combine advanced rendering with physically grounded simulation (aerodynamics, thermal response, fuel system behavior) so visuals and telemetry stay synchronized.
- Ship as both a desktop microsite and kiosk-ready install, with optional WebXR cockpit presence for museums and STEM labs.

## Experience Pillars
- **Visual Fidelity**: Museum-grade exterior and interior assets with cinematic lighting, microdetail textures, and accurate materials down to cockpit placards and gauge glass.
- **Cockpit Presence**: Interactive pilot and RSO stations with six-degrees-of-freedom (6DoF) head movement, hand-targetable controls, and guided procedures.
- **Physics Realism**: Real-time flight dynamics derived from SR-71 performance data, including inlet spike scheduling, Mach-dependent control response, and fuel transfer logic.
- **Data Provenance**: Every interactive element references verifiable sources (technical orders, NASA fact sheets, pilot memoirs) surfaced via contextual overlays.

## Target Audience & Devices
- Primary: aerospace professionals, museum visitors, pilots-in-training, and advanced enthusiasts running Chromium-based browsers, Safari 15+, or Firefox 100+ on desktop.
- Secondary: 4K kiosk displays with controller/touch input and optional VR-lite rigs (standing cockpit mode). Mobile/tablet support limited to a guided spectator mode.

## Experience Flow
- **Arrival – Hangar Lighting Pass**: Users orbit the Blackbird in a moody hangar, toggling surface inspections (panel seams, titanium fasteners, sensor bays) with macro zoom.
- **Step-In – Cockpit Orientation**: Seamless transition to pilot seat. Guided tutorial highlights canopy release, seat harness, and primary instrument clusters.
- **Systems Bring-Up**: Interactive checklists power hydraulic, electrical, and environmental systems. Switch actions animate, gauges spool up, warning flags clear in real time.
- **Engine & Flight Regime Simulation**: Users advance throttles through low, mid, and high Mach regimes. Flight director and analog gauges reflect aerodynamic loads, skin temperatures, and spike positions.
- **Recon Pass Playback**: Mission timeline replays recorded telemetry from declassified sorties, synchronizing cockpit instruments, external camera cuts, and narrated context.
- **Debrief**: After-action summary visualizes thermal maps, structural stress margins, and sensor captures, with links to references used.

## Core Features
- Full-fidelity cockpit with >200 interactable switches, circuit breakers, and guard covers mapped to authentic positions and logic.
- Hybrid flight model combining lookup tables (lift/drag/Mach) with runtime integrators for inertial behavior, atmospheric density, and control authority.
- Dynamic instrumentation: mechanical gauge meshes, CRT scopes, INS displays, and RSO radar screens driven by synchronized simulation data streams.
- PBR pipeline with HDR environment maps, anisotropic titanium shaders, heat shimmer, contrail/vapor simulation, and adaptive glare on canopy glass.
- Real-time structural/thermal visualization toggles showing fuel transfer, chines heating, and inlet shock stability.
- Mission data layer enabling slow-motion scrubs, freeze-frames, and overlay annotations referencing manuals and pilot callouts.

## Interactions & UX Flow
- `Cockpit Assist`: optional holographic overlays labeling controls; toggled per station for training or free-play.
- Hyperminimal HUD philosophy: zero persistent chrome, with context-sensitive prompts that fade in only when interaction affordances change and retract within 1.5 s of inactivity.
- Procedural checklists with branching states (normal, abnormal) that drive audio callouts and system outcomes.
- Dual-control mode allowing two users (pilot + RSO) to collaborate via network session or split input devices.
- Camera rig presets: pilot eye, RSO eye, ejection seat standing, exterior chase, maintenance boom inspection.
- Accessibility: remappable controls, keyboard/gamepad parity, high-contrast UI theme, narration captioning, and adjustable motion intensity.
- WebXR spectator cockpit enabling head-tracked immersion with simplified interaction rays.

## Visual Fidelity Strategy
- Begin from licensed CAD/laser scans; perform retopo targeting <180k tris exterior LOD0 and <120k tris full cockpit, with optimized LOD ladders for kiosks.
- UDIM texture workflow (8k source → 4k runtime) with multi-channel detail maps for titanium grain, panel rivets, and cockpit wear.
- Custom shader suite: anisotropic reflections, temperature-based emissive bloom, canopy Fresnel with photo-etched HUD combiner, and gauge glass parallax.
- Volumetric lighting for cockpit floodlights, fiber-optic indicator glows, and hangar god rays; GPU particles for vapor trails and JP-7 venting.

## Physics & Simulation Stack
- Flight dynamics modeled via SR-71 aerodynamic coefficients (NASA Technical Memorandums), spline-interpolated across Mach/altitude envelopes.
- Inlet spike control logic simulating shock placement, unstart risk, and engine surge feedback; visualized via timeline graphs.
- Thermal layer accounting for skin heating, fuel temperature equalization, and boundary layer bleed using simplified finite difference approximations.
- Fuel system model with individual tank volumes, CG tracking, and crossfeed rules; integrates with cockpit gauges and caution lights.
- Environmental model covering atmospheric density, contrail thresholds, and celestial lighting for day/night missions.
- Physics runs in Web Workers using WASM modules (Rust or C++) with shared buffers supplying data to Three.js render thread.

## Technical Stack
- Three.js r165+ with React Three Fiber, modular ES6 build, and progressive enhancement toward WebGPU renderer when stable.
- Typescript + Vite + React UI shell managing state via Zustand (simulation data), XState (cockpit procedures), and React Query (asset streaming).
- WASM-based physics core compiled from bespoke Rust/C++ flight model libraries; messaging via SharedArrayBuffer for deterministic tick rate.
- Post-processing via Drei/Postprocessing (ACES tonemapping, SSAO, motion blur tuned to Mach velocity, bloom, chromatic aberration toggles).
- Audio pipeline: Web Audio API + Resonance Audio for 3D spatial cues; multi-layered engine states, ECS panels, and cockpit clicks recorded from museum assets.

## Scene Architecture
- `ExperienceRoot`: orchestrates renderer, simulation clock, quality manager, and routing between exterior/cockpit modes.
- `SR71Exterior`: handles LOD swaps, panel inspections, dynamic material swaps (black ops, NASA white), and maintenance animations.
- `CockpitRig`: encapsulates seat transforms, instrument layouts, switch logic, and animation bindings tied to simulation bus.
- `PhysicsBridge`: Web Worker interface managing simulation tick, state diffs, and failover when running in reduced-quality spectator mode.
- `UIHud`: overlays flight data, caution/warning annunciators, thermal/structural toggles, and mission briefing cards.
- `AudioDirector`: mixes ambient hangar, cockpit ventilation, engine rumble, radio chatter, and voiceover checklists.

## Asset Pipeline
- **Capture**: Partner with museums for LiDAR/photogrammetry of cockpit, exterior panels, landing gear, and instrumentation; supplement with CAD where gaps exist.
- **Modeling**: Blender + Houdini for retopo and procedural cabling; maintain naming conventions aligning with SR-71 tech orders.
- **Texturing**: Substance 3D Painter/Designer for PBR sets, ensuring spec/rough response matches titanium and cockpit plastics; include micro-scratch decals.
- **Animation**: Rig canopy, control surfaces, landing gear, inlet spikes, breaker springs, and gauge needles; export glTF + animation clips.
- **Audio**: Record switch clicks, warning tones, environmental systems, and engine states; encode to Opus with loop points and dynamic filters.
- **Data**: Curate mission telemetry (speed, altitude, Mach, skin temps, fuel pressure) into normalized JSON/GLB streams with versioning.

## Performance & Optimization
- Dynamic quality tiers adjusting shadow resolution, particle density, reflection probes, and SSR toggles based on real-time GPU timing.
- WASM physics runs at fixed 120 Hz, interpolated to render frames for smooth instrumentation; fall back to 60 Hz on constrained hardware.
- Streaming asset loader using DRACO + KTX2 compression, with cockpit prioritized in memory; background streaming for exterior LODs in cockpit mode.
- Occlusion culling for cockpit avionics and view-dependent instrument lighting to conserve fill rate.
- Developer telemetry overlay capturing CPU/GPU frame cost, simulation latency, and memory footprint for QA.

## Tooling & Workflow
- GitHub Actions CI for TypeScript, ESLint, unit/integration tests, and WebGL snapshot comparisons.
- Storybook + React Three Fiber Canvas for component isolation (gauges, switches, HUD widgets).
- Linear for task management; Notion/Confluence pages track SME interviews and reference citations.
- Automated validation scripts checking animation clip naming, texture resolution limits, and cockpit switch coverage.
- Weekly SME syncs with former SR-71 crew to validate cockpit layout, procedures, and simulation behavior.

## QA & Testing Plan
- Unit tests: simulation integrators, gauge conversions, checklist state machines, and Web Worker messaging.
- Integration tests: full startup procedure, inlet unstart recovery sequence, thermal visualization toggles, and dual-user collaboration flow.
- Performance regression: GPU timing harness, physics determinism checks, and WebXR rendering budget tests.
- Accessibility audits: keyboard navigation, screen reader labels for HUD elements, motion sensitivity toggles.
- Hardware matrix: Windows (Chrome/Edge), macOS (Safari/Chrome), Linux (Chrome), kiosk builds, VR-lite rigs (Meta Quest, Varjo Aero via WebXR emulator).

## Development Roadmap

### Phase 0 – Research & Prototyping (Weeks 0-3)
- Acquire cockpit/exterior scans, confirm data licensing, and ingest NASA/USAF technical orders for instrumentation and performance envelopes.
- Prototype WASM physics core with simplified SR-71 aerodynamic tables; verify gauge data pathways into Three.js.
- Establish art direction boards for hangar lighting, cockpit mood, and thermal visualization cues.

### Phase 1 – Exterior & Infrastructure Foundations (Weeks 4-7)
- Integrate exterior model LODs, lighting rig, and quality manager; validate PBR response against reference photography.
- Implement `ExperienceRoot`, `PhysicsBridge`, and baseline HUD overlay; stream dummy simulation data to instrumentation.
- Stand up automated asset validation pipeline and CI infrastructure.

### Phase 2 – Cockpit Build & Systems (Weeks 8-13)
- Block in pilot/RSO stations, animate switches, needles, and annunciators; connect to simulated electrical/hydraulic buses.
- Implement checklist system with normal/abnormal branches, audio callouts, and progress persistence.
- Deliver accessible cockpit assist overlays and 6DoF camera rigs; run SME validation pass.

### Phase 3 – Physics Integration & Visualization (Weeks 14-18)
- Expand flight model with inlet spike scheduling, thermal calculations, and fuel transfer logic; tune against historical telemetry.
- Synchronize instrument shaders, HUD, and structural visualizers with physics output; add failure states (unstart, over-temp, fuel imbalance).
- Optimize WASM performance, implement fallback spectator mode, and profile across hardware targets.

### Phase 4 – Immersion & Polish (Weeks 19-23)
- Author cinematic transitions between hangar, cockpit, and recon playback; integrate audio soundscape and mission narration.
- Finalize post-processing, thermal/contrail VFX, and mission replay UI; complete localization-ready UI strings.
- Conduct accessibility, performance, and WebXR certification passes; resolve high/critical defects.

### Phase 5 – Launch & Sustainment (Weeks 24-26+)
- Harden builds, finalize kiosk packaging, prepare analytics dashboards, and produce operator documentation.
- Release mission telemetry packs, schedule SME-backed webinars, and plan quarterly updates (new missions, training scenarios).
- Monitor telemetry, capture user feedback, and prioritize sustainment backlog.

## Dependencies & Risks
- Access to licensable cockpit scans and instrumentation documentation; gaps may necessitate supplemental capture sessions.
- Balancing high-end physics fidelity with browser performance, especially under kiosk/WebXR constraints.
- Legal constraints around reproducing certain classified subsystems (sensor packages) in real time.
- Synchronizing SME availability with development milestones for timely validation.
- Ensuring WASM physics determinism across browsers and hardware to prevent drift between render and simulation threads.

## Success Metrics
- 60 FPS render + 120 Hz simulation on reference hardware (RTX 3070 / Apple M2 Max) with cockpit active; 45 FPS target in WebXR spectator mode.
- Cockpit interaction completion rate >80% for guided checklist users; average session length >8 minutes in kiosk installs.
- Positive SME validation scores on instrumentation accuracy (>95% correct layout) and flight behavior realism.
- Adoption by at least two museums or aerospace training programs within six months of launch.
- User-reported realism satisfaction >4.5/5 in post-experience surveys.

## References & Inspiration
- NASA Dryden/Armstrong SR-71 technical memorandums on flight characteristics, inlet management, and thermal response.
- USAF Technical Order 1SR-71A-1 (Pilot’s Flight Manual) and 1F-12B-1 for instrumentation and procedures.
- Smithsonian Udvar-Hazy SR-71 cockpit scans, Beale AFB training footage, and pilot memoirs (Brian Shul, Richard H. Graham).
- Web-based flight simulators (e.g., MSFS WebAssembly stack), high-fidelity aircraft mods, and museum-grade digital twins for benchmarking.


