
<div align="right">
  <details>
    <summary >🌐 Language</summary>
    <div>
      <div align="center">
        <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=en">English</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=zh-CN">简体中文</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=zh-TW">繁體中文</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=ja">日本語</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=ko">한국어</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=hi">हिन्दी</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=th">ไทย</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=fr">Français</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=de">Deutsch</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=es">Español</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=it">Italiano</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=ru">Русский</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=pt">Português</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=nl">Nederlands</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=pl">Polski</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=ar">العربية</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=fa">فارسی</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=tr">Türkçe</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=vi">Tiếng Việt</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=id">Bahasa Indonesia</a>
        | <a href="https://openaitx.github.io/view.html?user=mohesles&project=my-skylight-calendar&lang=as">অসমীয়া</
      </div>
    </div>
  </details>
</div>

# DIY Smart Home Family Calendar (Skylight Clone)

![Sklylight calendar](assets/main_view.jpeg)
![DIY Skylight](assets/sky2.png)

## 📖 Introduction

My wife has been recently bombarded in social media with ads for smart home calendars (Skylight, Cozyla, Hearth) and was ready to spend over $300 on one. Before giving her the green light, I asked for a chance to research them.

I realized most offered similar functionality but differed significantly in price. Most importantly, I didn't see any outstanding feature that I couldn't implement in **Home Assistant**.

**The Goal:** A WAF-approved (Wife Acceptance Factor), countertop-friendly touchscreen calendar that integrates deep into our smart home without monthly fees.

## 💡 Why DIY?

Choosing the DIY route with Home Assistant provided several benefits over buying a Skylight/Hearth display:

* **No Monthly Fees:** Avoids subscriptions for "premium" features.
* **Seamless Integration:** It talks to our lights, meals (OurGroceries), and presence sensors.
* **Old Hardware:** Repurposed a Mini PC and a standard monitor.
* **Privacy:** No vendor lock-in or risk of the company shutting down.

## 🛠 Hardware Selection

This is currently built to show the dashboard on any HD (1920x1080) display.

In my case, the requirement was for it to "look like" skylight, be touchscreen, be countertop, possibility to move it to different locations. Therefore I went with the hardware described below.
Nevertheless, you case might be different and will need you to adjust it as needed, for example if you want to display it on a tablet or something else.

The hardware I originally used I chose based on what I mentioned above plus with the hope to be able to extend functionality using the webcam, speaker and microphone. Currently I would probably build it differently now in hindsight, since I havent had time to address these additional hardware ideas.

* **Monitor:** [HP Engage 15-inch Touchscreen](https://computers.woot.com/offers/hp-engage-16t-fhd-monitor). I chose this over generic portable monitors because it includes a built-in **Speaker, Webcam, and Microphone**, allowing for future voice control or video calls.
* **Computer:** An old Mini PC (NUC/Tiny PC) running Windows/Linux in Kiosk mode, or a Raspberry Pi 4.~~


## ✨ Features

* **Family-wide & Individual Views:** Toggle specific family members' calendars on/off.
* **Two-way Sync:** Edit events on the screen or on our phones (Google Calendar).
* **"Add Event" Popup:** A custom UI to add events to specific calendars directly from the screen.
* **Weather & Date:** Beautiful, glanceable header.
* **Responsive:** Automatically adjusts day-count based on screen width (Mobile vs Desktop).

---

## ⚙️ Installation Guide

### Moerman fork notes

This fork keeps the upstream HACS/custom-card dashboard structure and applies the Moerman-specific mappings needed for the live Home Assistant instance.

* Calendars are limited to `calendar.family` and `calendar.benchapp_2`.
* The dashboard has a `Calendar` tab and a `Lists` tab. The Lists tab defaults to OurGroceries `todo.meals` and `todo.meijer`.
* The header has time, current weather, five-day forecast, and a configurable Nest thermostat card.
* Google event colors require the patched Home Assistant core, patched Home Assistant frontend package, patched `gcal_sync`, and patched `week-planner-card` fork described in [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md).
* Standard Home Assistant helpers are created by the YAML package. `input_multiselect` helpers are not YAML-backed and must be created in the UI once, then kept with stable entity IDs.

For production rebuilds, use [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md) as the source of truth. It lists the current fork refs, custom image strategy, custom card resources, and restore checklist.

### 1. Prerequisites

Home Assistant must have packages and themes enabled:

```yaml
homeassistant:
  packages: !include_dir_named packages

frontend:
  themes: !include_dir_merge_named themes
```

Install these frontend cards through HACS or as custom repositories:

* `week-planner-card` from the Moerman event-colors fork/build until those changes are released upstream.
* `bubble-card`
* `config-template-card`
* `card-mod`
* `better-moment-card`
* `weather-card` from `bramkragten/weather-card`, including its `icons/` directory.
* `browser_mod`
* `layout-card`
* `button-card`
* `input-multiselect-card` from `portbusy/ha-input-multiselect-card`
* `tabbed-card` from `kinghat/tabbed-card`
* `lovelace-thermostat-card` from `fineemb/lovelace-thermostat-card`

Install these integrations/components:

* Browser Mod from Settings > Devices & Services, not only from HACS downloads.
* `input_multiselect` from `portbusy/ha-input-multiselect`.
* The patched Google Calendar event-color stack if Google event colors need to render in both the stock calendar and this dashboard.

### 2. Backend Packages

Copy these files into your Home Assistant config directory:

* [packages/family_calendar.yaml](packages/family_calendar.yaml) -> `/config/packages/family_calendar.yaml`
* [packages/skylight_weather.yaml](packages/skylight_weather.yaml) -> `/config/packages/skylight_weather.yaml`

Edit `packages/family_calendar.yaml` for local mappings:

* `sensor.family_dashboard_header_config`: thermostat entity/title, weather entity, forecast entity, and forecast day count.
* `sensor.family_dashboard_invitees`: add-event invitee names, emails, and default checked state.
* `sensor.family_dashboard_todo_lists`: any number of todo-list cards for the Lists tab.
* `script.add_google_calendar_event`: calendar entity map. This fork currently targets `calendar.family` and `calendar.benchapp_2`.

The forecast package reads the weather entity and day count from `sensor.family_dashboard_header_config`; there is no separate weather entity to update in `skylight_weather.yaml`.

### 3. UI-Backed Multiselect Helpers

Create these with the `input_multiselect` integration UI before loading the dashboard:

* Name: `Calendar Selection`; entity ID: `input_multiselect.calendar_selection`; options: `Family`, `BenchApp`; selected by default: both.
* Name: `Calendar Event Invitees`; entity ID: `input_multiselect.calendar_event_invitees`; options: `Collin`, `Ashley`; selected by default: both.

The invitee helper is re-synced from YAML by `script.sync_calendar_event_invitees` on Home Assistant start, but the helper entity must already exist. If Home Assistant generates a different entity ID, rename it in the entity registry or update the dashboard/package references.

### 4. Lovelace Resources

Merge [deploy/lovelace-resources.yaml](deploy/lovelace-resources.yaml) into the live `lovelace.resources` block in `configuration.yaml`. The example uses `/local/community/...` paths because that is what the live instance uses. If your HACS install exposes `/hacsfiles/...`, keep the filenames and adjust only the path prefix.

The custom `week-planner-card` build is especially important. If HACS updates it back to upstream, this dashboard will lose event-level Google colors.

### 5. Dashboard And Theme

1. Create or open the `skylight-calendar` dashboard.
2. Open the raw YAML editor and paste [dashboard.yaml](dashboard.yaml).
3. Copy [themes/skylight.yaml](themes/skylight.yaml) to `/config/themes/skylight.yaml`.
4. Copy [calbackgrd.png](calbackgrd.png) to `/config/www/calbackgrd.png`.
5. Restart Home Assistant and select the `Skylight` theme for the display user.

### 6. Custom Home Assistant Image

For Google event colors to survive updates, Portainer should run a custom Home Assistant image, not the stock image. The image should include:

* `home-assistant-core` from the `event-colors` fork.
* `gcal_sync` pinned to the commit listed in [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md).
* `home-assistant-frontend` from the `event-colors-2026.5.4` package fork listed in [docs/DEPLOYMENT.md](docs/DEPLOYMENT.md).

An example stack shape is provided in [deploy/home-assistant-stack.example.yml](deploy/home-assistant-stack.example.yml). Replace the image tag with the actual pushed custom image tag.

---

## 📐 How It Works (Under the Hood)

### Calendar Selection

The calendar picker is `input_multiselect.calendar_selection`. `config-template-card` converts the selected options into filters for `week-planner-card`, so Family and BenchApp can be enabled independently while the calendar card stays in one place.

### Event Colors

Google event colors pass through four layers:

1. `gcal_sync` fetches calendar and event color metadata from Google.
2. The Home Assistant Google Calendar integration exposes those colors on calendar events.
3. The Home Assistant frontend renders those colors in the stock calendar.
4. The forked `week-planner-card` reads the same event color fields when `useEventColors: true` is set in [dashboard.yaml](dashboard.yaml).

If any of those layers is replaced with the stock upstream version, this dashboard falls back to the static calendar colors.

### Event Creation Script

The Add Event popup calls `script.add_google_calendar_event`. The script selects the target Google calendar, passes title/description/location, supports all-day or timed events, and sends selected invitees through the patched `google.create_event` attendees field.

### Lists Tab

The Lists tab is generated from `sensor.family_dashboard_todo_lists` in [packages/family_calendar.yaml](packages/family_calendar.yaml). Add or remove list entries there; the dashboard will create one todo card per configured list.

### Weather Header

The current weather card and forecast sensor read from `sensor.family_dashboard_header_config`. The forecast sensor keeps the existing entity ID `sensor.skylight_weather_next_7_days` for compatibility, but it now renders the configured number of days, currently five.

## NOTES

My original post was just to give a high level overview of how to do it and allow people to adjust code to make it work in their specific scenarios.

In particular I did this because every display and need is different. I can't develop for all potential sizes of displays, dashboards, etc. So it is built to work in the display I mentioned or any (1920x1080) but should be editable for others.

Talking about display, I originally suggested that one because it was on sale at Woot and was a very economic way to get a touchscreen display at the time. This might not be the case now, so use whatever display works for you. Tablet, touchscreen, phone, whatever. The main thing youll need to edit is the dashboard.

