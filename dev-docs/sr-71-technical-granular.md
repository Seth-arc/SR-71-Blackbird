# SR-71 Blackbird Technical Deep Dive

Purpose-built reference for the immersive SR-71 digital twin. Aggregates verified dimensions, materials, subsystems, and physics behaviors with implementation notes for high-fidelity rendering and simulation.

> **Source integrity:** Unless otherwise noted, facts derive from NASA Dryden/Armstrong technical memoranda, USAF Technical Order 1SR-71A-1, Pratt & Whitney J58 documentation, and Lockheed Skunk Works archival interviews. Cross-check new additions against primary sources before downstream use.

## 1. Geometric Baseline

- **Overall length:** 107 ft 5 in (32.74 m) measured nose pitot tip to exhaust.
- **Wingspan:** 55 ft 7 in (16.94 m) including chines.
- **Height:** 18 ft 6 in (5.64 m) to top of vertical stabilizer.
- **Wheelbase:** 37 ft 10 in (11.53 m); main gear track 15 ft 8 in (4.78 m).
- **Fuselage cross-sections:** Near nose (station 285) elliptical 7.1 ft × 6.0 ft; at cockpit (station 440) flattened pentagon to accommodate sensor bay; tail boom narrows to 4.2 ft width.
- **Control surface throw limits:**
  - Elevons: ±17° (inner panels) and ±24° (outer panels).
  - Rudders: ±25° each; canted inward 15°.
  - All-moving horizontal stabilizers absent; pitch handled through elevons.

**Asset guidance:** Maintain 8k texture-friendly seam lines along corrugated wing skins and chine panel breaks. Ensure canopy framing aligns with pilot and RSO visibility arcs (70° forward, 25° downward through side window).

## 2. Mass Properties & Loading

- **Gross takeoff weight:** ~140,000 lb (63,503 kg) with full fuel and sensors.
- **Empty weight:** ~67,500 lb (30,617 kg).
- **Fuel capacity:** 80,000 lb design load; usable capacity typically 69,800 lb due to expansion space.
- **Center of gravity (CG):** Target 25% MAC; must stay between 21%–31% MAC. Fuel transfer logic prioritizes chine and forward fuselage tanks to maintain CG envelope.
- **Refueling:** KC-135Q or KC-10; fill order nose → chine → wing → tail tanks.

Provide CG visualization overlays tied to tank levels for cockpit checklist training.

## 3. Structural Architecture & Materials

### 3.1 Primary Structure

- **Titanium alloy:** Ti-13V-11Cr-3Al (B120-VCA) forms 93% of structural weight. Yield strength ≈ 180 ksi at ambient; retains >120 ksi at 1,000 °F.
- **Skin panels:** Corrugated to accommodate thermal expansion; riveted/spot-welded with titanium fasteners. Wing corrugation pitch ~3 in.
- **Frames & longerons:** Combination of titanium I-beams and hat stiffeners; chines integrate with main fuselage frames to reduce radar hotspots.
- **Canopy:** Titanium frame with ultrasonically welded fused-quartz panes. Quartz thickness: 0.25 in outer, 0.2 in inner with inert gas gap.

### 3.2 Stealth-Oriented Composites

- **Chine/leading edges:** Silicone-asbestos reinforced phenolic laminates, with internal radar-absorbing ferrite fillers.
- **Inlet spike coatings:** Radar-absorbing epoxy skins over titanium core.
- **Control surface caps:** Fiberglass/graphite cloth to smooth radar returns.

### 3.3 Engine & Hot-Section Materials

| Component | Material | Max service temp |
|-----------|----------|------------------|
| Diffuser case | Inconel 718 | 1,250 °F (677 °C) |
| Ejector flap inner surfaces | Hastelloy X | 1,200 °F (649 °C) |
| Exhaust ejector rings | René 41 | 1,350 °F (732 °C) |
| Turbine disks | Waspaloy / Astralloy | 1,200–1,300 °F |
| Control cables | Elgiloy | Maintains elasticity at 900 °F |

### 3.4 Manufacturing Notes

- **Contamination control:** Titanium machining required distilled water coolants; chlorinated water embrittles alloy.
- **Heat treatment:** Entire fuselage segments baked at 1,000–1,300 °F then cooled in argon. Recommend thermal stress simulation for digital twin assembly sequences.
- **Fasteners & sealants:** Newly developed high-temp sealants (e.g., Dow Corning 93-500) handle 600 °F skin temps.

## 4. Thermal Dynamics & Heat Management

- **Surface temps at Mach 3.2:** Inlet lips 800 °F, nose 620 °F, cockpit windshield 600 °F, aft fuselage near 1,200 °F.
- **Thermal expansion:** Airframe lengthens up to 4 in (10 cm) at cruise. Panel gaps shrink; fuel leaks on ground diminish in flight.
- **Fuel as heat sink:** JP-7 pumped through hydraulic oil coolers, avionics racks, ECS, and engine bearing compartments before combustion.
- **Hydraulic fluid:** MIL-H-27601, stable to 750 °F; two independent systems running 4,000 psi.
- **Environmental Control System:** Liquid oxygen supplies breathing; suit inlet temperature maintained at ~70 °F via fuel-cooled heat exchangers. Cabin pressurized to 3.5 psi differential (~26,000 ft equivalent).

**Simulation cue:** Implement thermal lags between surface nodes and internal structure. Use simplified finite-difference grid (skin, sub-structure, fuel) updated at 10 Hz to feed cockpit temperature indicators.

## 5. Propulsion & Inlet System

### 5.1 Pratt & Whitney J58 Overview

- **Type:** Afterburning turbojet with six-stage compressor + single-stage turbine + convergent-divergent nozzle.
- **Static thrust:** 32,500 lbf each at sea level; ~34,000 lbf at Mach 3 cruise (ram effect).
- **Afterburner:** Variable geometry spray bars; has continuous operation capability (unlike most turbines).

### 5.2 Inlet Spike & Bypass

- **Spike travel:** 26 in (66 cm) total. Fully forward (0 in) at subsonic; retracts 1.6 in per 0.1 Mach up to 26 in at Mach 3.2.
- **Shock management:** Primary oblique shock anchored near spike tip; secondary shocks conditioned through bleed slots.
- **Bleed/bypass tubes:** Six tubes divert up to 20% compressor air directly to afterburner beyond Mach 2.2.
- **Engine control computer:** Analog air inlet control (AIC) monitoring Mach, AoA, compressor pressure ratio. Digital simulation should replicate unstart logic and recovery steps (rapid spike extension + bleed door opening).

### 5.3 Fuel & Ignition

- **Fuel tank pressurization:** Stored nitrogen gas prevents cavitation and lowers flammability.
- **Triethylborane (TEB):** Pyrophoric chemical used for ignition; 600 cc per engine with 16 shots (start + AB lights). Visualize green flash at ignition events.

## 6. Fuel System & CG Management

- **Tank layout:** Nose (Tank 1), forward fuselage (2A/2B), chines (3L/3R), wings (4L/4R), aft fuselage (5), and tail (6). Vent/expansion spaces allow thermal growth.
- **Transfer sequence:** Automatic logic (coupled to CG computer) maintains CG within limits. Manual override available; caution/warning lights for imbalance >1,000 lb.
- **Refueling port:** Top fuselage left of centerline; includes relief valves to prevent overpressure.

**Cockpit instrumentation:** Dual fuel quantity indicators (pilot + RSO), CG calculator display, crossfeed valve controls on engineer’s panel.

## 7. Flight Controls & Aerodynamics

- **Elevons:** Hydraulically actuated, redundant control loops. Combined pitch/roll commands. Trim driven by autopilot to manage Mach tuck.
- **Rudders:** All-moving; can differential split (drag rudder mode) to aid lateral control at high Mach.
- **Stability augmentation:** Automatic Flight and Inlet Control System (AFICS) manages yaw dampers, pitch stability, and inlet spike coordination.
- **Chine vortices:** Generate up to 40% total lift at high AoA; maintain laminar flow around canopy; require precise modeling for landing/takeoff attitude.
- **Cruise efficiency:** Fuel flow decreases with Mach increase between 2.5 and 3.2 due to improved inlet compression; capture in simulation by table-driven TSFC (thrust-specific fuel consumption) curve.

## 8. Cockpit & Crew Systems

- **Crew:** Pilot (front) and Reconnaissance Systems Officer (rear) separated by titanium bulkhead with cable pass-throughs.
- **Pressure suits:** David Clark S1030; provide 3.5 psi suit pressure. Suit cooling tied into ECS heat exchangers.
- **Key pilot instruments:** Mach meter (reads to 3.5), digital astro-inertial navigation system (ANS) display, attitude director indicator, vertical velocity indicator ±6,000 ft/min, fuel CG readout, and caution warning panel.
- **RSO station:** Radar imagery scope, ECM controls, ANS keypad, sensor payload monitors. Include fold-out knee panels for mission cards.
- **Checklists:** Hydraulic preflight, inlet check, engine light, climb, cruise, descent. Provide interactive states (normal vs abnormal) for digital twin training.

## 9. Sensor & Avionics Suite

- **ANS:** Astro-inertial navigation using scanning telescope; accuracy ±1,000 ft over 5,000 nm. Requires star catalog; simulate initialization (align to Polaris) ~8 min.
- **SLAR/Optical payloads:** Side-looking airborne radar, optical cameras (OBC, TEC). Apertures behind chine doors; doors open 30° during operation.
- **ECM:** Electronic countermeasure arrays in tail cones; refer to declassified general behavior (noise jamming, deception) without detailed parameters.
- **Data recorder:** 9-track tape machines; maintain audio cues (reel spin, beeps) for immersion.

## 10. Physics Modeling Guidelines

### 10.1 Aerodynamics

- Use Mach-dependent CL/CD tables sourced from NASA TM X-62,523. Interpolate across altitude (pressure/temperature) for accurate performance.
- Apply compressibility corrections for control surfaces; elevate control forces above Mach 2 due to dynamic pressure.
- Model chine vortex lift onset around 12° AoA with hysteresis to mirror stable vortices.

### 10.2 Propulsion

- Simulate inlet shock position via 1D compressible flow equations. Unstart triggers when pressure ratio exceeds limits; force spike to slam forward 6 in and drop thrust by ~17% until recovery.
- Fuel flow model: TSFC ~1.9 lb/(lbf·hr) at Mach 2.2 dropping to 1.6 lb/(lbf·hr) at Mach 3.0.
- Incorporate TEB shot counter that inhibits relights when depleted.

### 10.3 Thermal

- Skin heat equation: dT/dt = (q_conv + q_fric - q_radiation - q_fuel)/ (m·c). Simplify with precomputed lookup for steady-state temps vs Mach/altitude.
- Structural expansion: ΔL = α·L·ΔT with α ≈ 8.6e-6 /°F for Ti-13V-11Cr-3Al.
- Feed cockpit gauges and caution lights: windshield > 650 °F triggers caution, inlet > 950 °F triggers compressor bleed alert.

### 10.4 Systems Integration

- Run physics core at ≥120 Hz fixed timestep; render interpolation to 60 FPS.
- Use SharedArrayBuffer to pass state vectors (Mach, altitude, temps, tank masses) to Three.js instrumentation for deterministic displays.

## 11. Sensory & Audio Cues

- **Engine audio:** Layer spool whine, afterburner roar, and inlet whoosh. Unstart produces pronounced bang followed by low-frequency rumble.
- **Cockpit ambience:** ECS blower, suit ventilation hiss, caution/warning chimes (three-tone for general, continuous for critical).
- **Vibration cues:** At Mach 3, expect low-amplitude, high-frequency vibration; map to camera shake in cockpit mode.

## 12. Visual Reference Targets

- **Surface wear:** Titanium oxidizes to deep blue/purple near engine trunks; apply gradient masks along aft chines.
- **Fasteners & panel IDs:** Alphanumeric panel markings (white stenciling) every 18 in. Nose sensor windows tinted gold anti-radiation coating.
- **Fuel leak staining:** Simulate JP-7 streaks along belly when on ground; vanish during flight segments.
- **Thermal glow:** At cruise, minimal visible glow; only nozzle interiors show dull red when afterburner active.

## 13. Implementation Checklist

1. Validate geometry against Smithsonian Udvar-Hazy scans or NASA CAD overlays.
2. Confirm material assignments per component; apply temperature-dependent shader variants.
3. Integrate physics modules (aero, propulsion, thermal, fuel) with cockpit instrumentation bindings.
4. Script nominal and abnormal procedures (inlet unstart, hydraulic failure, CG out-of-limit) with associated audio/visual feedback.
5. Build reference database (images, tech orders) with metadata for each interactive hotspot.
6. Conduct SME review focusing on cockpit layout, switchology, and flight behavior accuracy.

## 14. Outstanding Research Gaps

- High-resolution documentation of chine composite layup patterns (consult Lockheed archives).
- Precise electro-mechanical response times for hydraulic actuators under high-temp conditions.
- Detailed ECM behavior (only partially declassified); treat as non-interactive backdrop unless clearance obtained.
- Quantitative data for thermal gradients during descent/cooldown to enhance simulation authenticity.

Maintain update log for any new data points and cascade revisions into simulation parameters and UI references immediately.

