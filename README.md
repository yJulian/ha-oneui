# One UI for Home Assistant

A Samsung One UI 9 inspired Home Assistant theme for Galaxy phones, tablets and foldables. Rounded cards, quiet surfaces, clear typography and blue accents bring a familiar Galaxy feel to your home controls. Includes light and dark modes in one theme, with a black canvas in dark mode.

The design reference is Samsung's [current One UI page](https://www.samsung.com/uk/one-ui/), which identifies One UI 9 as the latest version as of October 3, 2026. Colors and dimensions here are our interpretation, not official Samsung tokens. This is an independent project, with no Samsung affiliation.

## Install

### HACS

1. In HACS, open **Custom repositories** from the menu.
2. Add `https://github.com/yJulian/ha-oneui` with the type **Theme**.
3. Open **One UI for Home Assistant** and download it.
4. Enable the theme directory with the `frontend:` configuration shown below, then check the configuration and restart Home Assistant if this is your first theme installation.
5. Select **One UI** in your user profile, and choose **Auto**, **Light** or **Dark** as the color mode.

HACS manages the theme under `/config/themes/one_ui/`. Avoid installing a second copy manually, since both copies define the same theme name. Local edits to the HACS-managed file may be replaced by updates.

The repository includes a root-level `hacs.json` that explicitly selects `themes/one_ui.yaml` and displays this README in HACS. See the [HACS theme requirements](https://www.hacs.dev/docs/publish/theme/) and [manifest documentation](https://www.hacs.dev/docs/publish/start/).

### Manual installation

1. Copy [themes/one_ui.yaml](themes/one_ui.yaml) to `/config/themes/one_ui.yaml` on your Home Assistant instance. Create the `themes` directory if needed.
2. Merge the following into `/config/configuration.yaml`. If you already have a `frontend:` section, add the `themes:` entry there rather than creating a second section. Keep your existing theme include if it already loads this directory.

   ```yaml
   frontend:
     themes: !include_dir_merge_named themes
   ```

3. Check the Home Assistant configuration and restart Home Assistant when first enabling the theme directory.
4. Open your user profile and select **One UI** as the theme. Set the color mode to **Auto** to follow your device's light/dark preference, or choose **Light** or **Dark** explicitly.

After editing the theme, run `frontend.reload_themes` from **Developer tools → Actions**. Refresh the browser or reopen the Companion App if colors are cached.

The base theme requires no custom cards, JavaScript, card-mod, external fonts or HACS. Optional larger card-feature controls use card-mod as described below. See Home Assistant's [theme installation and mode documentation](https://www.home-assistant.io/integrations/frontend/) for the supported configuration format.

## Galaxy setup

Use the Home Assistant Android Companion App or a modern browser. With the profile color mode set to Auto, the frontend follows the system preference exposed by the app or browser. Check Android's dark mode settings if the mode does not match. Do not force a dashboard-specific theme when you want the profile selection to apply.

The font stack first tries `One UI Sans` and `SamsungOne`, then Roboto and the system sans-serif. Availability depends on the Android WebView/browser; installing the theme does not make Samsung's proprietary fonts available. No font files are bundled or fetched.

Built-in cards retain their native accessibility behavior. Sections dashboards work well on narrow phone displays and expand across larger tablet/foldable screens. Keep frequent controls near the top of each section and avoid dense entity lists.

## Larger Tile controls

Card-feature controls use a consistent `20px` corner radius in both modes, including the sub-buttons inside Tile cards. This applies without extra dependencies.

For larger controls, install **card-mod** through HACS and follow its [installation instructions](https://github.com/thomasloven/lovelace-card-mod#installation). The theme includes an optional [card-mod theme hook](https://github.com/thomasloven/lovelace-card-mod/blob/master/README-themes.md) that increases feature height from `42px` to `48px` and uses `10px` spacing between grouped feature buttons. It affects built-in card features, including Tile sliders, switches and grouped buttons. It does not resize the main Tile entity icon or custom-card sub-buttons.

Reload themes and refresh the frontend after installing card-mod. Without card-mod, the updated corners still apply and feature controls keep Home Assistant's default height. Home Assistant [sets feature height inside the component](https://github.com/home-assistant/frontend/blob/dev/src/panels/lovelace/card-features/hui-card-features.ts), so a plain YAML theme cannot override it through inheritance.

For Sections dashboards with features below the Tile content, allow enough vertical space: use **3 rows** in the card's Layout settings. The example dashboard already does this for its brightness controls. Check inline features and manually sized cards for clipping on narrow screens; move features below the content if needed.

Change `one-ui-feature-height` to adjust the enhanced height, or `ha-card-features-border-radius` to adjust the corners. These hooks depend on frontend component structure and should be checked after frontend updates. The larger controls have not yet been visually verified in a running HA instance.

## Example dashboard

[examples/dashboard.yaml](examples/dashboard.yaml) provides a Sections dashboard with quick controls, climate, weather and media using only built-in cards. Create a new dashboard, open its raw configuration editor and paste the example there. Replace all example entity IDs with your own; lights must support brightness and the weather entity must provide daily forecasts. Remove cards for devices you do not have. The example inherits your profile theme.

The theme also works on existing dashboards; the example is optional.

## Customize

Edit the corresponding `modes.light` and `modes.dark` values in the theme:

| Variable | Purpose |
| --- | --- |
| `primary-color` | Blue accent, selection and common active controls |
| `primary-background-color` | Dashboard canvas |
| `card-background-color` | Cards, sidebar and Material surfaces |
| `secondary-background-color` | Recessed input surfaces |
| `primary-text-color` / `secondary-text-color` | Main and supporting text |
| `text-primary-color` | Text on an accent-colored surface |
| `one-ui-yellow` / `one-ui-teal` | Active lights and fans |

`ha-card-border-radius` at the top applies to both modes. The default is `28px`; try `20px` for a more compact appearance. Preserve sufficient text/background contrast when changing colors. Entity domains retain Home Assistant's more specific safety and status colors unless explicitly overridden.

## Scope and compatibility

This is a theme, not a replacement frontend. Home Assistant owns navigation, dialogs, spacing inside cards, icons and responsive behavior. Native Samsung gestures, the Quick Panel, system bars and Samsung animations cannot be recreated through theme variables alone. Some components, including full-screen mobile dialogs, override shared shape settings.

Primary/accent colors, entity state colors and light/dark theme modes use documented Home Assistant interfaces. Other CSS variables, including typography, card shapes, legacy Material controls and section gaps, are frontend implementation details and may change between releases. Custom cards may use their own styles. See the [frontend documentation](https://www.home-assistant.io/integrations/frontend/) and [frontend source](https://github.com/home-assistant/frontend) for compatibility details.

The YAML and palette contrast have been checked locally. Rendering has not yet been verified against a running Home Assistant instance or physical Galaxy device. Before relying on the theme, check your dashboard, sidebar, settings, more-info dialogs, inputs, toggles and sliders in both modes on your device. Check unavailable entities and alarm/lock states as well.

## Troubleshooting

| Symptom | What to check |
| --- | --- |
| Theme is missing | File location, theme include, configuration validation and initial restart |
| HACS says a version cannot be used | Refresh repository information and select the updated default branch or a release containing `hacs.json` and `themes/one_ui.yaml`; confirm the repository type is Theme |
| Theme stays light or dark | Profile color mode, device settings and dashboard theme overrides |
| Some cards look different | Card-specific styles and frontend variable changes |
| Samsung font is missing | WebView font availability; Roboto/system fallback is expected |
| Edits do not appear | Run `frontend.reload_themes`, then refresh the client |

To uninstall, select another theme in your profile, remove `one_ui.yaml`, and reload themes. Remove the configuration include only if no other themes use it.

## License

MIT; see [LICENSE](LICENSE). Samsung, Galaxy and One UI are trademarks of Samsung Electronics. Home Assistant is a trademark of the Open Home Foundation. No Samsung artwork or proprietary fonts are distributed.
