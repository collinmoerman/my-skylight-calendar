# Durable Home Assistant Deployment

This fork is not only a Lovelace dashboard. The production setup depends on
YAML packages, custom frontend cards, a custom Home Assistant core fork, a
custom Home Assistant frontend package, and two UI-backed `input_multiselect`
helpers. Treat those as one deployment unit.

## Current fork refs

Pin these refs when rebuilding or releasing the deployment:

- `home-assistant-core`: `collinmoerman/home-assistant-core`, branch `event-colors-2026.5.4` for production HA 2026.5.4 images. The `event-colors` branch tracks the same changes on upstream dev.
- `home-assistant-frontend`: `collinmoerman/home-assistant-frontend`, branch `event-colors-2026.5.4`, commit `a76cc68925228fa25c4d06a5023f9ea008f9bbbc` for the HA 2026.5.x custom package.
- `gcal_sync`: `collinmoerman/gcal_sync`, commit `5599ea017309f0abd1f0806480df6fc20d13cf92`.
- `week-planner-card`: `collinmoerman/week-planner-card`, branch `event-colors`, commit `206dd593da6cb0ad7bc82e4298b1d781f4dec449`.

Use tags/releases for these once the changes are stable. Production should not
install from a moving branch.

## Home Assistant image

Google event colors require patched core behavior and patched frontend behavior.
A stock `homeassistant/home-assistant` image will eventually lose those changes.
Build and deploy a custom image instead.

Recommended image contents:

- Home Assistant core from the `event-colors` fork.
- `gcal-sync` pinned to `5599ea017309f0abd1f0806480df6fc20d13cf92`.
- `home-assistant-frontend` installed from the HA 2026.5.x frontend fork commit `92c818e4fae6f749d84b93508c436491f618b372`.

The `home-assistant-core` fork Dockerfile now exposes `GCAL_SYNC_PACKAGE` and
`CUSTOM_FRONTEND_PACKAGE` build args and defaults them to those pinned archive
URLs. The Dockerfile filters the stock `gcal-sync` and `home-assistant-frontend`
entries out of `requirements_all.txt`, installs the rest with `--no-build`, then
installs the two pinned fork packages.

The example Portainer stack is in `deploy/home-assistant-stack.example.yml`.
Do not point Portainer back to the stock image unless losing Google event color
support is acceptable.

Docker Hub image digest: `sha256:b4fc5e1b54385635581e0694d22a0c6d7078adca27cc70854ddd729a2973e4cf`.

## HA config files

Copy these files into `/config`:

- `dashboard.yaml` -> raw YAML for the `skylight-calendar/family-calendar` dashboard.
- `packages/family_calendar.yaml` -> helpers, invitees, todo list config, add-event script.
- `packages/skylight_weather.yaml` -> forecast sensor, sourced from the configured weather entity.
- `themes/skylight.yaml` -> dashboard theme.
- `calbackgrd.png` -> `/config/www/calbackgrd.png`.

`configuration.yaml` must include:

```yaml
homeassistant:
  packages: !include_dir_named packages

frontend:
  themes: !include_dir_merge_named themes
```

Merge the resources from `deploy/lovelace-resources.yaml` into the live
`lovelace.resources` block.

## Custom integrations and cards

Install these custom integrations/cards before loading the dashboard:

- `browser_mod` integration, configured from Settings > Devices & Services.
- `ge_home` if GE appliances are still used elsewhere in the HA instance.
- `input_multiselect` from `portbusy/ha-input-multiselect`.
- `input-multiselect-card` from `portbusy/ha-input-multiselect-card`.
- `week-planner-card` from this fork/build, not the upstream HACS release, until the event-color changes are released upstream.
- `tabbed-card` from `kinghat/tabbed-card`.
- `lovelace-thermostat-card` from `fineemb/lovelace-thermostat-card`.
- `kiosk-mode` from `maykar/kiosk-mode` for the dedicated display user.
- `weather-card` from `bramkragten/weather-card`, including its `icons/` directory.
- `bubble-card`, `config-template-card`, `card-mod`, `better-moment-card`, `layout-card`, and `button-card`.

HACS can manage the standard cards, but pin or custom-repo the forked
`week-planner-card` build. A normal HACS update can overwrite the custom event
color support.

## Kiosk display user

Log the display in as a non-admin `Skylight` user and open `/skylight-calendar/family-calendar`. The dashboard-level `kiosk_mode.user_settings` block hides the Home Assistant header, sidebar, menu button, and overflow menu for that user only.

## UI-backed helpers

The `input_multiselect` integration does not support YAML helper creation. Create
these helpers through the UI, then keep their entity IDs stable:

- Name: `Calendar Selection`, entity: `input_multiselect.calendar_selection`, options: `Family`, `BenchApp`, selected by default: both.
- Name: `Calendar Event Invitees`, entity: `input_multiselect.calendar_event_invitees`, options: `Collin`, `Ashley`, selected by default: both.

The invitee options are re-synced from `sensor.family_dashboard_invitees` on HA
start by `script.sync_calendar_event_invitees`. The helper still has to exist
first.

## Config knobs

Edit `packages/family_calendar.yaml` for local mappings:

- `sensor.family_dashboard_header_config`: thermostat entity/title, weather entity, forecast entity, and forecast day count.
- `sensor.family_dashboard_invitees`: add-event invitee names, emails, and default checked state.
- `sensor.family_dashboard_todo_lists`: any number of todo list cards for the Lists tab.
- `script.add_google_calendar_event`: calendar entity map. This fork currently targets `calendar.family` and `calendar.benchapp_2`.

The dashboard tab labels stay hard-coded as `Calendar` and `Lists` by design.

## Verification checklist

After deploying or rebuilding:

1. Run Home Assistant config check.
2. Restart Home Assistant.
3. Confirm `sensor.skylight_weather_next_7_days` has `source_entity`, `forecast_days`, and five forecast items.
4. Confirm `input_multiselect.calendar_selection` has both calendars selected.
5. Confirm the dashboard loads all custom cards without red-card resource errors.
6. Confirm Google calendar events show event-level colors in the stock HA calendar and in the Skylight week planner card.
7. Add a test event with a location and invitees, then confirm the popup closes and the Google event includes attendees.
