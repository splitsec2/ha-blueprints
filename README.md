# Home Assistant blueprints

Three automation blueprints I run at home. Each file's header is the real
documentation — quirks, edge cases, and what doesn't work are in there.

GPL-3.0. The motion blueprint is a fork of a GPL-3.0 blueprint, so the whole repo is.

| Blueprint | Trigger | Turns off? | Dim before off |
|---|---|---|---|
| Advanced motion-activated lights | motion | yes | absolute % |
| Sensor-or-Switch Light | motion, presence, door, or the switch | yes | relative % |
| Tiered door alert | door contact | n/a | n/a |

## Advanced motion-activated lights

[![Import blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Fsplitsec2%2Fha-blueprints%2Fmain%2Fblueprints%2Fautomation%2Fday_night_motion_lights_with_dim_before_off.yaml)

Day/night brightness, a dim step before off, and a grace window so the automation
doesn't stomp a wall-switch change two minutes later.

Fork of [SmartThingsConnect/HomeAssistantMigration](https://github.com/SmartThingsConnect/HomeAssistantMigration).
Six changes, all listed in the header. The one that caused the fork: upstream's
colour-temp templates check a value the dropdown never emits, so picking a colour
temperature does nothing at all — no error, no logbook entry. Filed as
[issue #2](https://github.com/SmartThingsConnect/HomeAssistantMigration/issues/2),
open since May; upstream last pushed March.

The rest: `manual_override_seconds` (default 600), explicit `from`/`to` on the trigger
so it doesn't fire when a device reconnects, and a percent→0-255 fix on the dim compare.

Dims to an absolute percentage, not a percentage of current brightness. If you want
relative dim, use Sensor-or-Switch instead.

## Sensor-or-Switch Light

[![Import blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Fsplitsec2%2Fha-blueprints%2Fmain%2Fblueprints%2Fautomation%2Fsensor_or_switch_light.yaml)

One light, one behaviour, whether a sensor or a person starts it.
START → ON → HOLD → DIM → OFF, where START is motion, presence, a door opening, or
someone flipping the switch.

Doors start it but don't hold it, so a propped-open door won't keep the light on.
Lux/sun gates the automatic triggers only — flipping the switch ignores the gate,
because that's intent. Leave `hold_sensors` empty for a switch-only room and the timer
becomes the hold. A sensor going `unknown`/`unavailable` falls back to the timer rather
than going dark on someone who's still sitting there.

Brightness and on/off only. Colour temp and the curve while on belong to Adaptive
Lighting, which is why this is one arc instead of eleven thousand lines.

## Tiered door alert

[![Import blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Fsplitsec2%2Fha-blueprints%2Fmain%2Fblueprints%2Fautomation%2Ftiered_door_alert.yaml)

Three stages for a door left open: local announcement, then escalation, then mobile.
Chime TTS per stage, optional light flash, and an override sensor to suppress it.
Presets for fridge, freezer, exterior, garage.

## Importing

Buttons above, or paste the raw URL under Settings → Automations & scenes → Blueprints
→ Import blueprint:

```
https://raw.githubusercontent.com/splitsec2/ha-blueprints/main/blueprints/automation/day_night_motion_lights_with_dim_before_off.yaml
https://raw.githubusercontent.com/splitsec2/ha-blueprints/main/blueprints/automation/sensor_or_switch_light.yaml
https://raw.githubusercontent.com/splitsec2/ha-blueprints/main/blueprints/automation/tiered_door_alert.yaml
```

Filenames follow HA core (`snake_case`, no suffix) and won't change — renaming breaks
re-import for anyone who already installed one.

## Re-import overwrites your copy

These set `source_url`, so HA offers to re-import, and re-import replaces your file
without asking. That happened here: a re-import dropped all six patches from the motion
blueprint, and an automation carried on passing `manual_override_seconds` to an input
that no longer existed. HA ignored it, the grace window stopped working, and nothing
logged any of it. Fork rather than editing in place.

## PRs

Welcome. If a fix to the motion blueprint also applies upstream, send it there too.

---

*Last Updated: 2026-09-08 | v1.0 — initial publish: motion (forked, 6 patches), Sensor-or-Switch, tiered door alert; GPL-3.0 throughout.*
