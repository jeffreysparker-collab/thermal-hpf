# Thermal HFP

**Heterochromatic Flicker Photometry for OLED Thermal Spectral Drift**

A PWA for measuring how a smartphone OLED's blue channel output shifts as the device heats up. Uses the classic HFP null-point paradigm — adjust blue luminance until flicker between a blue and yellow patch disappears — logged against device temperature over a structured heating protocol.

## Scientific Background

Blue OLED subpixels are the weakest link in OLED stacks. They require higher-energy excitons and degrade faster, both cumulatively and transiently under thermal load. This experiment probes whether short-term heating (minutes of use) shifts the blue/yellow luminance balance enough to be psychophysically detectable via flicker null displacement.

## Protocol

1. **Soak phase** — Full white screen heats the device (configurable, default 2 min)
2. **Trial** — Blue/yellow flicker displayed; observer drags slider to find luminance null (minimum visible flicker)
3. **Log** — Null value + IR thermometer reading + battery/thermal state saved
4. Repeat 6× (configurable), with inter-trial soak periods
5. **Export CSV** — Null point trajectory vs temperature

## Measurements

| Column | Description |
|--------|-------------|
| `null_blue_t` | Blue luminance at null (0–1, gamma-linearized) |
| `ir_temp` | Manual IR thermometer reading (°C or °F, your choice) |
| `thermal_state` | iOS/browser thermal state (nominal/fair/serious/critical) |
| `battery_level` | Battery % at time of log |
| `flicker_hz` | Flicker rate used |

## Setup

- Max brightness
- True Tone **OFF**
- Night Shift **OFF**  
- Do Not Disturb on
- IR thermometer aimed at phone back (or tape a logger to it)
- Dark room preferred

## Notes

- No service worker — no caching layer, always fresh from GitHub
- `thermalState` via `NSProcessInfo` is not accessible from browser JS (iOS sandboxing). The `thermal_state` column will show `unknown` unless wrapped in a native app. The IR thermometer is your ground truth.
- Battery API (`navigator.getBattery`) works on Android Chrome; not available on Safari/iOS.
- At 120Hz display: flicker at 15Hz = alternating every 4 frames — clean subharmonic

## Interpreting Results

A **downward drift** in `null_blue_t` over trials means you needed *less* blue drive to null against fixed yellow — i.e., blue output increased relative to yellow (unexpected, possibly adaptation artifact). An **upward drift** means you needed more blue to match — consistent with blue channel rolling off or yellow brightening with heat.

Watch for the IR temp to correlate with null drift. A non-monotonic pattern (drift then recovery) would be interesting and worth a cooldown trial.
