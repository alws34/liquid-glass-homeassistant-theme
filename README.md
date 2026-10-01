# Liquid Glass

An Apple-style "liquid glass" Home Assistant theme: frosted, translucent cards and controls with a bright specular rim and soft depth, floating over a colourful gradient canvas. One theme contains light and dark palettes and follows Home Assistant's **Auto / Light / Dark** preference.

Runtime: one YAML theme plus **card-mod**, with a small press-feedback module for buttons inside nested component shadow roots. Mushroom and Bubble Card are optional unless your dashboard uses them.

**How the glass works:** cards, badges, chips, dialogs, Bubble pills and pop-ups, and the dashboard toolbar get a translucent fill (`glass-fill`) plus `backdrop-filter: blur() saturate()`, mostly through Home Assistant's own hooks (`ha-card-backdrop-filter`, `ha-dialog-surface-backdrop-filter`, `app-header-backdrop-filter`). They blur `lovelace-background`, a multi-stop gradient. The page canvas, sidebar, settings toolbars and dropdown menus stay opaque (`soft-surface`): nothing sits behind them to blur, and translucent menus are hard to read.

## Install and try it now

1. In HACS, install/update **card-mod**, **Mushroom**, and **Bubble Card**. Tested versions: Home Assistant **2026.9.1**, frontend **20260826.6**, card-mod **4.2.1**, Mushroom **5.2.3**, Bubble Card **3.3.0**.
2. Copy [`themes/liquid-glass.yaml`](themes/liquid-glass.yaml) to `/config/themes/liquid-glass.yaml` on Home Assistant. Copy [`frontend/liquid-glass-press.js`](frontend/liquid-glass-press.js) to `/config/www/liquid-glass-press.js` (create `www` if needed).
3. Merge this into your existing `/config/configuration.yaml`; keep a single `frontend:` section:

   ```yaml
   frontend:
     themes: !include_dir_merge_named themes
     extra_module_url:
       - /hacsfiles/lovelace-card-mod/card-mod.js
       - /local/liquid-glass-press.js?v=1
   ```

   **Use your actual card-mod resource URL.** Find it under **Settings → Dashboards → ⋮ → Resources**. If its URL has a `?hacstag=...` suffix, copy that exact URL into `extra_module_url` too. Retain the HACS-managed resource entry and keep both URLs identical after updates. See [card-mod installation](https://github.com/thomasloven/lovelace-card-mod#installation).

4. Check your HA configuration, then restart Home Assistant once for the `frontend` change. For subsequent theme-only edits, run **Developer tools → Actions → `frontend.reload_themes`**, then refresh the browser if necessary.
5. Open your **Profile → General → Theme**, select **Liquid Glass**, then set **Theme mode → Auto**. Home Assistant selects the light or dark palette from the device appearance. These are native [frontend theme modes](https://www.home-assistant.io/integrations/frontend/#dark-mode-support).

## Test without controlling real devices

The supplied sample uses only three test helpers.

1. Copy [`examples/test-helpers.yaml`](examples/test-helpers.yaml) to `/config/packages/lovelace_soft_test.yaml`.
2. If you do not already load packages, merge this into the existing `homeassistant:` section and restart:

   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```

   If packages are already configured differently, use your existing package layout. Do not add a second package loader. See [Home Assistant packages](https://www.home-assistant.io/docs/configuration/packages/).
3. Create a new empty dashboard called **Liquid Glass test**. Open its **Edit dashboard → ⋮ → Raw configuration editor** and paste [`examples/test-dashboard.yaml`](examples/test-dashboard.yaml). This example assumes Mushroom and Bubble Card resources are loaded.
4. Run this checklist:

   | Check | Expected result |
   | --- | --- |
   | Light, then Dark in Profile | Canvas, sidebar, cards, badges, and open menus use the corresponding palette. |
   | Auto, then change device appearance | Palette follows the device without choosing another theme or running an automation. |
   | Test switch / Modern template / Bubble switch | The helper toggles; the on/off label and state color update. |
   | Native, Mushroom, and Bubble sliders | Moving one updates the same percentage everywhere. Try 0%, 50%, and 100%. |
   | Mushroom number buttons | The minus/plus pill and its round buttons are raised. Holding plus rocks it like a switch: the plus side sinks and tilts away, the minus side lifts (and vice versa). Values update; controls remain legible at both limits. |
   | Bubble menu | Menu opens above other cards; Home/Away/Night can be selected. |
   | Open test pop-up | Its background matches the theme; nested controls work; Escape/back closes it. |
   | Entity more-info | Dialog remains readable and close controls work. |
   | Keyboard | Tab focus stays visible; existing keyboard controls continue working. |
   | Press feedback | Hold any clickable element — entity row, tile, badge (native and Mushroom template), chip, Mushroom plus/minus, native button, Bubble action, or a card with a tap action: its surface goes inset and returns on release. Space/Enter work on keyboard-focusable buttons; disabled and non-clickable elements stay unchanged. |
   | Phone / tablet / desktop | Check approximately 375px, 768px, and 1440px widths; no new horizontal scrolling or clipped controls. |
   | Reduced motion | Theme-added transitions stop; upstream cards retain their own motion behavior. |

After testing, remove the test dashboard and the helper package if no longer needed, then restart HA. The helpers have no connection to devices.

## Apply it to your existing dashboard

Select the theme in your profile, or per view with `theme: Liquid Glass`. Views use the theme's gradient (`lovelace-background`) automatically; remove any per-view `background:` override that hides it, or the glass has nothing colourful to blur.

A wallpaper image also works (`background: center / cover no-repeat url("/local/wallpaper.jpg") fixed`); glass looks best over colourful, low-detail images. Import into a **new dashboard** first and keep the original as your rollback.

## Coverage

Coverage mirrors the Neumorphism theme this was derived from: the same theme variables and public component surfaces, re-skinned as glass.

| Components | Treatment |
| --- | --- |
| Native tiles, entities, gauges, clocks, weather, history, alarm panel, to-do, picture cards | Frosted glass card, palette, state colors, rounded geometry; native control groups get darker recessed glass. |
| Native thermostat / climate card, modern more-info controls | `ha-control-*` family themed: recessed circular-slider track and mode selector, raised stepper buttons, a raised ± number bar with round raised buttons, accent-colored active fill. The circular slider is an SVG stroke, so it gets a solid recess color rather than a true inset shadow. |
| Mushroom entity, template, light, number, update | Raised cards/icon containers and styled exposed control surfaces. Current template cards use HA tile styling; classic cards use Mushroom variables. Number (buttons mode) and climate ± controls are a raised pill with round raised buttons that rocks toward the pressed side like a switch; the number slider mode stays recessed. |
| Mushroom chips and template badges | Individual glass pills; the chip row itself remains transparent. Mushroom chips sit in nested shadow roots that theme CSS cannot reach, so they are translucent without blur. |
| Bubble buttons, sliders, sub-buttons, media players, separators, pop-ups | Bubble theme variables plus scoped card-mod CSS; raised interactive surfaces (round raised media buttons), quieter separators, opaque select menus and frosted pop-up surfaces. Pressing a Bubble button sinks its visible pill; the accent-filled play button presses in with a neutral shade instead of a grey smudge. |
| Button-card, mini-graph, ApexCharts, uptime, calendar-card-pro | Inherited card shell and palette where the card uses HA theme variables; existing data/series colors and custom content remain. |
| Scheduler, custom to-do, Bambu Lab cards, Dreame map, UniFi map, WebRTC, more-info-card | Theme-aware outer surfaces. Their private controls, canvas drawings, maps, video overlays, and data colors require individual card support. |
| Grid, sections, horizontal/vertical stacks, auto-entities, decluttering, config-template, restriction, simple-swipe, conditional | Structural wrappers stay transparent; their child cards inherit the theme. |
| Iframes, remote logos, photos, video | Surrounding surface is themed; embedded pages and image/video pixels cannot be recolored by an HA theme. |

**Press feedback is not limited to that list.** With `liquid-glass-press.js` loaded, the inset press effect applies to any element the frontend renders as clickable (computed `cursor: pointer`) — default entity rows, tiles, native and Mushroom badges, chips, Bubble actions, and cards with a `tap_action` — regardless of card type, including cards wrapped in card-mod's `mod-card` (for example a Mushroom template badge used as a card). The surface that sinks is the element that paints the button, not an icon inside it. Inline text links and non-clickable elements are left alone.

The ± rockers and Bubble button presses live inside component shadow roots, so they need `liquid-glass-press.js`; the theme's CSS alone cannot reach them. Tune the rocker with `soft-rocker-tilt` (default `12deg`) and `soft-rocker-perspective` (default `150cqw`, relative to the control's width) at the top of the theme.

**A YAML theme cannot guarantee every internal element of every third-party card.** Hard-coded inline styles, closed shadow roots, canvases, and cross-origin iframes are outside the common theme interface. This package styles the exposed surfaces and preserves state/action semantics. The specialized cards above still need a visual pass on your installation with their real data.

## HACS distribution

Install as a HACS custom repository: **HACS → ⋮ → Custom repositories**, add `https://github.com/alws34/liquid-glass-homeassistant-theme` with type **Theme**, then download **Liquid Glass**. Copy `frontend/liquid-glass-press.js` from the [latest release](https://github.com/alws34/liquid-glass-homeassistant-theme/releases/latest) to `/config/www/` yourself.

This folder has the required structure for a **Theme** repository: one file under `themes/`, a root `hacs.json`, and this README. See [HACS theme requirements](https://www.hacs.xyz/docs/publish/theme/). HACS installs the theme YAML only; copy `frontend/liquid-glass-press.js` to `/config/www/` yourself and bump its `?v=` suffix after updates.

## Tuning and troubleshooting

- **Frost:** change `glass-blur` (default `blur(24px) saturate(180%)`). Opacity is `glass-fill` / `glass-recess` per mode; raise it if text over a busy wallpaper is hard to read.
- **Canvas colours:** edit `glass-backdrop` per mode.
- **Cards look flat grey:** a per-view `background:` override is hiding the gradient; remove it or point it at a colourful wallpaper.
- **No blur at all:** the browser lacks `backdrop-filter`, or card-mod is not loaded. Cards still render as translucent fills.
- **Slow on old tablets:** backdrop blur is GPU work per card; lower the blur radius or raise the fill opacity.
- **Raised cards but flat icons/controls:** verify card-mod is loaded through the exact `extra_module_url`, then refresh. The base theme works without card-mod, but its extra surface styling does not.
- **A clickable element does not press inward:** verify the module loads. In the browser console, `window.__liquidGlassPress` must print `true`; if it is `undefined`, open `/local/liquid-glass-press.js?v=1` in a tab — a 404 means the file is missing from `/config/www/` (a newly created `www` folder also needs an HA restart). Badges in the view's badge row still press without it (theme CSS), but badges wrapped in `mod-card`, Bubble buttons, and the ± rockers do not. The module follows the event path through open shadow roots and briefly changes the shadow of the innermost element the frontend renders as clickable (computed `cursor: pointer`), which covers default entity rows, tiles, badges, chips, and any card tap target. It restores existing inline styles on release, cancellation, or window blur and leaves actions unchanged. Closed shadow roots and cross-origin embedded pages remain outside its reach.
- **Text is unreadable in dialogs on iOS (Safari or the Companion app), intermittently:** this is a Home Assistant core bug (`frontend/src/state/themes-mixin.ts`) — for any non-"default" theme resolved to light mode, HA sets `<meta name="color-scheme">` to `"dark light"` instead of `"light"`. WebKit uses that hint for native form-control colors independently of the page's CSS, so sliders/switches/inputs inside dialogs can render dark-native while the page itself is light. `liquid-glass-press.js` corrects this meta tag whenever Liquid Glass is active, derived from the actually-applied text color, so it self-heals even if HA never rewrites the tag. Verify the resource URL was bumped and hard-refresh the Companion app once after updating.
- **Only one card differs:** inspect its local `styles`, `card_mod`, background, and Bubble module settings. Explicit card overrides can take precedence over the theme.
- **Mode does not follow your device:** choose **Auto**, remove a per-card mode override, and check the companion app's appearance setting. `frontend.set_theme` selects a default theme for a mode; it does not force every user's current light/dark preference.
- **Pop-up colors briefly lag:** update Bubble Card and refresh the browser. Existing JavaScript-generated RGB/state colors remain the card's responsibility.
- **Keep the exact theme name:** `Liquid Glass` and `card-mod-theme: Liquid Glass` must match.
- **Rollback:** select your previous profile theme and return to the original dashboard. You can then remove this theme file and reload themes.
  The press module is inactive when the selected theme does not expose `soft-shadow-inset`; remove its frontend entry/file too if uninstalling.

## Validation and design references

To look at the ± rockers and Bubble media buttons without Home Assistant, run `python3 -m http.server 8765` in the repo root and open `http://127.0.0.1:8765/tests/preview.html`. It loads the real theme YAML and press module into stand-in cards that copy the upstream Mushroom, HA, and Bubble markup and CSS.

Run the local structural checks:

```sh
python3 -m venv .venv
.venv/bin/pip install -r requirements-dev.txt
.venv/bin/python tests/check_theme.py
node tests/check_press.mjs
```

These check duplicate YAML keys, CSS syntax, light/dark token parity, variable cycles, contrast and HACS layout. Contrast is measured against `soft-surface`/`soft-recess`, the opaque colours the glass reads as over the default canvas; a custom wallpaper changes that, so check legibility over yours.

The JavaScript check verifies press/release, the pressed-side marker and ± rocker styles, cancellation, keyboard activation, window blur, disabled controls, theme opt-in, and exact restoration of existing inline styles.

Not yet verified on a live Home Assistant instance; the preview page is the rendering test so far.
