# Voron 2.4 Red — Klipper Config

**Owner:** Mike Thompson (Columbia, SC)  
**Machine:** Voron 2.4 “Red” — Octopus + Nitehawk SB, Stealthburner / Clockwork 2, **E3D Revo EVO**

This is a working, production config from a heavily tuned DIY Voron — not a stock paste. Use it as a reference, steal ideas, adapt pinouts to *your* wiring.

> ⚠️ **Before you copy blindly:** MCU serials, probe offsets, mesh profiles, PID, and purge/park coordinates are machine-specific. Change those. Thermistor type for a real Revo 104NT core is `ATC Semitec 104NT-4-R025H42G` (not Generic 3950).

---

## What’s in this build

### Motion & mechanics
- CoreXY Voron 2.4 with **0.9°** steppers on X/Y (`full_steps_per_rotation: 400`, 32 µsteps)
- Geared Z (80:16) with four Z motors
- Input shaping + careful tramming (bed/frame held to ~0.01 mm square OCD levels)
- `max_velocity` 300 / high accel with spreadCycle on motion axes (`stealthchop_threshold: 0`)

### Toolhead
- Stealthburner + Clockwork 2
- **E3D Revo EVO** on Nitehawk SB (`pullup_resistor: 2200`)
- Correct Semitec 104NT sensor type + tuned extruder PID
- Klicky probe dock/attach, QGL, nozzle scrub pack

### Filament runout (custom)
Full runout suite in `filament-runout.cfg`:
- Creality-style mechanical switch on Octopus **Stop3** (`^!PG11`)
- Pause / park, hold bed, nozzle strategy, long idle while waiting
- Runout-aware **RESUME** (filament detected + heat wait)
- **`RUNOUT_PURGE_BUCKET`** into a single rear purge bucket (X≈115, Y≈299) with Z-safe raise only (no dive into a tall print), longer purge + snap so the noodle stays in the bucket
- `CHECK_FILAMENT` preflight in `PRINT_START`
- Sensor off/on hooks around LOAD/UNLOAD

### Lighting
- Stealthburner + chamber NeoPixel effects (`LED-Effects-Main.cfg`, `LED-Macros-Main.cfg`)
- Status macros, runout alert strobe, print progress eye candy

### UX / extras
- Runs Mainsail + KlipperScreen on the host (UI/host configs not published here)
- Knomi 2 on the toolhead

---

## Layout

| Path | What |
|------|------|
| `printer.cfg` | Main machine config, macros, includes |
| `nitehawk-sbv2.cfg` | Toolboard / extruder / SB |
| `filament-runout.cfg` | Runout sensor + macros |
| `KlickyProbe/` | Klicky + QGL helpers |
| `nozzle_scrub-2.cfg` | Wipe / scrub |
| `LED-*.cfg` | LED effects & status macros |

---

## Highlights worth stealing

1. **Don’t stack retracts on PAUSE** — Mainsail already retracts; an extra `G1 E-5` with no matching unretract prints air on resume.
2. **Runout purge ≠ normal pause** — bucket purge only on the runout resume path.
3. **Revo thermistor** — Generic 3950 on a Semitec Revo can read ~12–15 °C cold. Use the right `sensor_type`, then re-PID.
4. **Nitehawk Rsense** — extruder `sense_resistor: 0.100` on Nitehawk; Octopus axes `0.110`. Don’t mix boards.

---

## Credits & attribution

This config stands on a lot of other people’s work. Thank you.

### Required plugin
- **[Klipper LED Effects](https://github.com/julianschill/klipper-led_effects)** (`led_effects`) by **Julian Schill** — used heavily via `LED-Effects-Main.cfg` (`[led_effect]` layers) and `LED-Macros-Main.cfg` (`SET_LED_EFFECT` / `STOP_LED_EFFECTS`). Install that plugin on the host; these cfgs will not work without it.

### Upstream configs / macros we adapted
- **[Voron Design](https://github.com/VoronDesign)** — Voron 2.4 printer design, stock config patterns, and community conventions.
- **[Klipper](https://github.com/Klipper3d/klipper)** — firmware + config language everything here targets.
- **[Klicky Probe](https://github.com/jlas1/Klicky-Probe)** — dockable probe macros in `KlickyProbe/` (maintained lineage from **Garrettwp**, Annex magprobe work originally by **Mental**, with contributions noted in-file from **RyanG**, **Trails**, **richardjm**, and others).
- **Voron / community nozzle scrub + purge-bucket macros** — basis for `nozzle_scrub-2.cfg` (`CLEAN_NOZZLE` / `_CLEAN_NOZZLE`); coordinates, purge gates, and LED hooks heavily customized for this machine.
- **BigTreeTech Knomi** status helpers — starting point for `Knomi2.cfg` (toolhead display status variables / M109–M190 wrappers).
- **Nitehawk / Stealthburner toolboard samples** — pinout and extruder section patterns adapted in `nitehawk-sbv2.cfg` (verify against *your* board revision).

### This machine’s custom work (Mike + 3D Printers bot)
- **`filament-runout.cfg`** — written for this printer (sensor, pause/runout state, heat strategy, runout-aware `RESUME`, `RUNOUT_PURGE_BUCKET`, `CHECK_FILAMENT`, LED runout alert).
- **LED lock / status sequencing** — `_LED_LOCK` / `_LED_UNLOCK`, status macros, print-progress chamber effects, runout strobe, and fixes so pause/resume don’t leave LEDs stuck.
- **Broad mods across the stack** — `PRINT_START` / park / purge behavior, Revo thermistor + PID notes, TMC / motion tuning, Klicky + QGL integration, and lots of small “don’t do that twice” safety fixes.

If we missed someone whose snippet clearly lives on in these files, open an issue or PR and we’ll add you.

---

## License / use

Free to use, fork, and adapt for your own printer.  
If it saves you a weekend of debugging, star the repo and tell someone about your Voron.

Built and abused in real prints — including the dumb little “I LOVE YOU BABE!” plaque that started as a filament-runout victory lap.

— **Mike Thompson** + the **3D Printers** bot that helped wrestle the macros
