# SR-71 Digital Twin Component & Functionality Blueprint

Derived from `sr-71-threejs-project.md` to map the immersive experience into concrete components, associated dimensions, and user-facing functionality. Use as a handoff-ready reference for engineering, art, and simulation teams.

## 1. Aircraft Dimensions & Physical Zones

| Zone | Dimensions / Notes | Experience Usage |
|------|--------------------|------------------|
| Overall airframe | Length 107 ft 5 in; wingspan 55 ft 7 in; height 18 ft 6 in | Sets world scale, camera clipping ranges, and collision envelopes |
| Hangar footprint | 160 ft × 120 ft recommended (clearance for cinematic orbit) | Defines `Arrival – Hangar Lighting Pass` navigation bounds |
| Engine nacelle | Diameter 5.2 ft intake lip, spike travel 26 in | Drives visualized spike animation and inlet inspection hotspots |
| Cockpit canopy | Width 6.2 ft, length 12.7 ft | Determines seating positions, 6DoF headbox, canopy reflections |
| Pilot seat to RSO offset | 4.5 ft longitudinal separation | Governs dual-user collaboration and camera swap logic |
| Mission payload bay | 12 ft chine length per side | Used for sensor bay transparency toggles and recon payload reveals |

**Implementation notes:** Apply consistent scaling across Three.js scenes. Ensure texture atlases align with chine and wing corrugation seams while maintaining the 8k→4k LOD workflow defined in the project plan.

## 2. Scene Modules & Major Components

### 2.1 Arrival – Hangar Lighting Pass

- **Components:** `SceneRoot`, HDR skybox, volumetric light shafts, animated hangar doors, ground crew props.
- **Dimensions:** Hangar door aperture 80 ft wide × 40 ft tall to frame fuselage reveal.
- **Functionality:**
  - Free-orbit camera with macro zoom to inspect panel seams and fasteners.
  - Hotspots for exterior materials (titanium skin, inlet spike composites).
  - Ambient audio layering (tool clatter, distant turbine howl).

### 2.2 Cockpit Orientation & Systems Bring-Up

- **Components:** `CockpitRig`, pilot/RSO instrument clusters (>200 interactables), `Cockpit Assist` overlays, procedural checklist UI.
- **Dimensions:** Seat height 3.5 ft above floor, canopy clearance 18 in above helmet to inform head movement limits.
- **Functionality:**
  - Guided tutorial highlighting canopy latch, harness, primary gauges.
  - Interactive hydraulic/electrical/environmental system switches with animated needle response.
  - Suit ventilation, ECS audio cues, caution/warning annunciators synced to system state.

### 2.3 Engine & Flight Regime Simulation

- **Components:** `PhysicsBridge`, flight model WASM core, inlet spike animation rig, thermal visualization shaders.
- **Dimensions:** Spike translation path 26 in; elevon deflection ±17°/±24°.
- **Functionality:**
  - Throttle input drives Mach simulation, inlet shock placement graphs, and real-time gauge updates.
  - Fail state scripting (inlet unstart, over-temp, fuel imbalance) with audio/visual feedback.
  - Structural heat map overlay toggles illustrating chine heating and fuel transfer.

### 2.4 Recon Pass Playback & Mission Timeline

- **Components:** `MissionTimeline`, D3 globe overlays, sensor bay door animations, archival audio player.
- **Dimensions:** Globe radius 6 ft in scene space for kiosk legibility; payload door swing 30°.
- **Functionality:**
  - Scroll- or controller-driven timeline advancing telemetry, external camera cuts, cockpit readouts.
  - Slow-motion scrub, freeze-frame, and annotation overlays citing mission sources.
  - Integration with `TelemetryService` for speed, altitude, skin temperature graphs.

### 2.5 Debrief & Analytics Layer

- **Components:** Mission summary UI, thermal/structural metric charts, archival imagery carousel.
- **Functionality:**
  - Displays peak Mach, altitude, CG deviations, inlet events.
  - Links to reference documentation (tech orders, NASA memos) captured per hotspot.
  - Provides export hooks for museum kiosk analytics or classroom modules.

## 3. Core Systems & Simulation Components

| Component | Inputs | Outputs | Key Functionality |
|-----------|--------|---------|-------------------|
| Flight Dynamics Engine | Control surfaces, atmospheric model, Mach target | CL/CD forces, attitude, accelerations | Hybrid lookup + integrator model for Mach 0–3.2, chine vortex lift, Mach tuck compensation |
| Inlet & Propulsion Manager | Mach, throttle, spike position | Engine thrust, compressor state, TEB counter | Simulates turbo-to-ramjet transition, bypass flow, unstart detection, afterburner scheduling |
| Thermal System | Mach/altitude, fuel mass, ambient temp | Skin temps, cockpit glass temp, caution flags | Simplified finite-difference thermal grid feeding heat shaders and instrumentation |
| Fuel & CG Controller | Tank levels, refuel events, maneuver state | CG %, transfer commands, warning lights | Maintains CG between 21–31% MAC, supports manual override for abnormal checklist |
| Audio Director | Simulation state, camera position | Spatialized audio mix | Blends turbine, cockpit ECS, warning tones, archival narration per scene |
| Quality Manager | GPU frame timing, device profile | Render tier adjustments | Dynamically tunes shadow resolution, post-processing, particle density |

Each system runs within the Web Worker-based `PhysicsBridge`, relaying deterministic state snapshots to rendering via SharedArrayBuffer for 120 Hz simulation cadence.

## 4. Interaction & UX Components

- **Camera Presets:** Pilot eye, RSO eye, ejection seat stand-up, exterior chase, maintenance boom—each with default FOV and positional offsets.
- **`Cockpit Assist` Overlays:** Toggleable holographic labels, throttle/yoke highlights, switch grouping cues for training mode.
- **Hyperminimal UI Policy:** Keep HUD hidden by default; reveal micro-panels only on interaction focus, auto-dismissing within 1–2 seconds of inactivity and stacking never exceeding 8% screen real estate.
- **Dual-Control Collaboration:** Network session support enabling simultaneous pilot/RSO interaction; ensures authority resolution for shared systems (fuel transfer, checklist advancement).
- **Accessibility Toolkit:** Remappable controls, high-contrast UI theme, captioning, motion-intensity slider, keyboard/gamepad parity.
- **WebXR Spectator Mode:** Head-locked cockpit camera, gaze-based interaction rays, simplified shader path for VR-lite devices.
- **Tutorial & Analytics:** Adaptive onboarding that retires after first completion; `AchievementTracker` logging hotspot visits, checklist completion, mission milestones.

## 5. Asset & Pipeline Components

- **Capture Pipeline:** LiDAR/photogrammetry ingestion, museum licensing management, verification against NASA CAD references.
- **Modeling & Texturing:** Blender/Houdini retopo, Substance Painter UDIM authoring, texture bake validation scripts (triangle counts, resolution caps).
- **Animation Library:** glTF clips for canopy, landing gear, inlet spikes, elevons, needle sweeps, breaker springs, checklist interactions.
- **Audio Library:** Opus-encoded switch clicks, warning tones, engine states, ground ambience, narration stems.
- **Data Services:** `TelemetryService` distributing curated mission JSON payloads; localization package for HUD text and voiceover transcripts.

## 6. Dependencies & Cross-Team Touchpoints

- **External SMEs:** Weekly validation for cockpit layout, procedure accuracy, and mission data authenticity.
- **Legal & Licensing:** Track museum scan rights, archival audio usage, and export-control considerations for sensor payload depiction.
- **QA Coverage:** Unit tests for camera controllers and state machines; integration tests for mission timeline, cockpit flow, quality tiers; performance harness for WebXR budgets.

## 7. Implementation Checklist Summary

1. Lock exterior/cockpit geometry scale using Smithsonian/NASA references.
2. Wire `Flight Dynamics`, `Inlet & Propulsion`, `Thermal`, and `Fuel & CG` modules into `PhysicsBridge` with deterministic state snapshots.
3. Build cockpit interaction map covering >200 switches with normal/abnormal checklist states and feedback cues.
4. Integrate mission timeline overlays, recon payload animation, and archive-driven narration.
5. Configure Quality Manager presets and performance telemetry overlay for QA.
6. Validate accessibility, localization, and dual-user flows prior to polish phase.

Maintain synchronization with `sr-71-threejs-project.md` for roadmap sequencing and update this blueprint when new components or functionality are approved.

