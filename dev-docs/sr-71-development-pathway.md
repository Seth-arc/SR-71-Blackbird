# SR-71 Digital Twin Development Pathway

Detailed phase plan to deliver the SR-71 immersive digital twin with uncompromised functionality, physics accuracy, and visual fidelity. Each phase includes objectives, discipline-specific tracks, dependencies, and quality gates.

## Phase 0 – Discovery & Alignment (Weeks 0–3)

**Objectives**
- Lock mission scope, fidelity targets, and SME availability.
- Validate feasibility of physics + rendering stack via spike prototypes.
- Establish asset, data, and QA pipelines.

**Key Steps**
**Tech Proving**: Prototype Three.js + WASM bridge for sample telemetry feed; benchmark high-poly cockpit asset in target browsers.
- **Pipeline Setup**: Configure repo, CI checks (TypeScript, ESLint), Storybook, asset validation scripts, and documentation spaces.
- **QA Planning**: Draft testing matrix, performance budgets, and acceptance criteria per subsystem.

**Deliverables & Quality Gates**
- Approved creative/technical brief; backlog seeded in Linear.
- Working prototype demonstrating Web Worker ↔ renderer data exchange at ≥120 Hz simulation.
- Asset pipeline dry run (import → retopo → export) with naming conventions validated.

## Phase 1 – Exterior & Hangar Foundations (Weeks 4–7)

**Objectives**
- Stand up exterior aircraft visualization with hangar context.
- Implement core infrastructure: `ExperienceRoot`, `QualityManager`, camera/navigation scaffolding.
- Validate performance baselines on reference hardware.

**Engineering Track**
- Integrate exterior LODs, collision proxies, and orbit camera rigs.
- Implement hotspot scaffolding with dummy metadata.
- Build hangar lighting system (volumetric passes, HDR skyboxes) and quality tier toggles.

**Art & Visual Track**
- Block out hangar environment, ground equipment, and lighting passes.
- Apply initial PBR materials to exterior (titanium, inlet composites) with 4k runtime textures.
- Author placeholder VFX (dust motes, subtle heat shimmer) for performance profiling.

**Audio Track**
- Layer ambient hangar loops, distant engine rumbles, and interactive spot sound tests.

**QA & Performance**
- Run first GPU frame timing snapshot on RTX 3070 / M2 hardware; target ≥60 FPS.
- Validate navigation accessibility (keyboard/gamepad parity) and hotspot focus states.

**Exit Criteria**
- Exterior inspection loop playable end-to-end with three functional hotspots.
- Quality tiers (high/medium) switching without visual artifacts.
- Performance telemetry overlay reporting stable frame times.

## Phase 2 – Cockpit Build & UX Systems (Weeks 8–13)

**Objectives**
- Deliver fully modeled and textured cockpit (pilot + RSO) with interactable instrumentation.
- Implement procedural checklist framework and `Cockpit Assist` overlays.
- Establish baseline telemetry plumbing for gauges.
- Codify hyperminimal UI guidelines ensuring context-driven prompts and zero persistent chrome.

**Engineering Track**
- Build `CockpitRig` hierarchy, map >200 switches/controls, implement interaction states (normal/guarded/breaker).
- Develop checklist engine (state machine via XState) with audio callouts and gating conditions.
- Integrate 6DoF camera controls, head-box constraints, and dual-user collaboration infrastructure.

**Art & Visual Track**
- Finalize cockpit textures (UDIM workflow), glass shaders, and instrument emissives.
- Animate needles, indicator flags, and breaker springs; export glTF clips.

**Audio Track**
- Record switch clicks, checklist narration placeholders, suit ventilation loops.

**QA & Accessibility**
- Instrument coverage validation (SME walkthrough); achieve ≥90% layout accuracy.
- Accessibility audit for captions, high-contrast theme, remappable controls.
- Hyperminimal UI verification: measure prompt dwell times (<1.5 s default) and ensure visible UI footprint stays under 8% of viewport during normal flow.
- Implement analytics hooks for interaction tracking.

**Exit Criteria**
- Cockpit startup checklist executable with simulated data stubs.
- Dual-user session demo verifying control arbitration (pilot vs RSO inputs).
- SME approval on cockpit fidelity and switchology.

## Phase 3 – Physics Integration & Realism Systems (Weeks 14–18)

**Objectives**
- Integrate flight dynamics, inlet/propulsion, thermal, and fuel/CG modules into runtime.
- Synchronize simulation outputs with cockpit instrumentation and visualization layers.
- Implement failure states and training feedback loops.

**Engineering Track**
- Embed WASM physics modules into `PhysicsBridge`; ensure deterministic state snapshots at 120 Hz.
- Wire CL/CD tables, TSFC curves, spike scheduling, thermal grid, and fuel transfer logic.
- Implement fail-state handlers (inlet unstart, over-temp, CG out-of-range) triggering audio/visual cues.

**Art & Visual Track**
- Author thermal shaders (heat maps, emissive bloom gradients), fuel transfer overlays, and contrail/VFX refinements.
- Align instrument animations with simulation data (Mach meter, fuel gauges).

**Audio Track**
- Integrate engine state blending, caution warnings, unstart bang/rumble effects.

**QA & Testing**
- Build regression tests for physics determinism, instrumentation accuracy (tolerance ±1%).
- Profile simulation + render budgets; ensure no missed frames under stress scenarios.

**Exit Criteria**
- Full startup → climb → cruise loop playable with live physics.
- Failure scenarios reproducible with checklist response guidance.
- Thermal and structural visualizations match reference telemetry within agreed tolerances.

## Phase 4 – Mission Narrative & Immersion (Weeks 19–23)

**Objectives**
- Deliver mission timeline playback, recon payload interactions, and cinematic transitions.
- Polish lighting, post-processing, and ambient storytelling.
- Finalize audio narrative mix.

**Engineering Track**
- Implement `MissionTimeline` with D3 globe overlays, sensor door animations, slow-motion scrub, and annotation layers.
- Script camera transitions between hangar, cockpit, and exterior chase modes.
- Integrate archival media playback with synchronization to telemetry.

**Art & Visual Track**
- Final lighting passes (ACES tonemapping, bloom, SSAO) tuned per quality tier.
- Add cinematic VFX (hangar god rays, vapor cones, canopy reflections) with adaptive performance settings.
- Populate archival imagery carousel and mission data visuals.

**Audio Track**
- Blend narration, archival comms, mission music, and spatialized cockpit audio.
- Finalize audio mixing presets for kiosk vs desktop experiences.

**QA & UX**
- Usability sessions for mission playback controls (scroll, controller, kiosk touch).
- Verify localization pipeline for captions/subtitles.

**Exit Criteria**
- Mission timeline deliverable with at least one fully narrated sortie.
- Cinematic transitions hitting target frame times on reference hardware.
- Audio mix passes loudness and localization checks.

## Phase 5 – Hardening, QA, & Optimization (Weeks 24–27)

**Objectives**
- Drive bug backlog to acceptable levels; certify performance targets.
- Complete accessibility, localization, and cross-device QA.
- Prepare kiosk packaging and deployment runbooks.

**Engineering & QA Track**
- Execute full regression suite (unit, integration, performance, WebXR).
- Optimize shaders, instancing, and streaming assets for low-tier settings.
- Harden Quality Manager heuristics; ensure graceful degradation.

**Art & Audio Track**
- Texture/performance polish (mip biasing, normal map tweaks, audio compression).
- Final VFX adjustments for WebXR and kiosk tiers.

**Documentation & Ops**
- Author operator guides, troubleshooting docs, and analytics dashboards.
- Finalize legal/licensing attribution packages.

**Exit Criteria**
- Zero critical bugs, high-priority bugs <5.
- Performance: 60 FPS desktop, 45 FPS WebXR spectator, 30 FPS kiosk baseline.
- Accessibility and localization sign-offs completed.

## Phase 6 – Launch & Sustainment (Week 28 onward)

**Objectives**
- Deploy production build, kiosk image, and companion analytics.
- Establish post-launch content cadence and support workflows.

**Launch Activities**
- Cut gold master build; run final smoke tests on target hardware.
- Publish kiosk installer with auto-start scripts and watchdog processes.
- Distribute marketing assets (trailers, screenshots) captured from cinematic mode.

**Sustainment Plan**
- Schedule quarterly mission updates, SME webinars, and telemetry content drops.
- Monitor analytics (session length, checklist completion, performance) and feed insights into backlog.
- Maintain CI pipelines, regression tests, and accessibility audits for each release.

**Success Metrics Review**
- Track KPI alignment (session duration, SME satisfaction, museum adoption) against `sr-71-threejs-project.md` goals.
- Hold retrospective with cross-discipline leads to capture lessons learned.

## Cross-Phase Quality Threads

- **SME Validation**: Touchpoint at end of each phase to approve authenticity (exterior, cockpit, physics, narrative).
- **Performance Budgets**: Continuous monitoring via telemetry overlay; baseline captured Phase 1 and updated every phase.
- **Security & Licensing**: Ongoing checks on asset usage rights, especially for archival media and sensor depictions.
- **Accessibility & Localization**: Each phase adds layers; final compliance checks in Phase 5.
- **Documentation**: Living knowledge base updated with every subsystem change, ensuring handoffs remain smooth between teams.

Maintain synchronization of this pathway with roadmap changes in `sr-71-threejs-project.md`. Update deliverables and gates if scope shifts or new missions are introduced.

