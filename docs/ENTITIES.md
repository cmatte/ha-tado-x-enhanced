# Devices, Entities and Attributes

### Supported Devices

The device-model list below is illustrative, not a guarantee that every feature is available on every model. Some controls depend on the device and Tado API response.

| Model | Device |
| :--- | :--- |
| VA04 | Radiator Valve X |
| SU04 | Temperature Sensor X |
| TR04 | Wireless Receiver X |
| RU04 | Wired Smart Thermostat X |
| IB02 | Bridge X |

Available entities vary with hardware, account capabilities and configuration.

## Tado X Home

| Controls | Type | Description | Version |
| :--- | :--- | :--- | :---: |
| Boost All | Button | Apply a heating boost to all rooms for 30 minutes |  |
| Boost Hot Water | Button | Apply a heating boost to the hot water for 1 hour | 1.0.0 |
| Hot Water Mode | Selector | Off / Schedule / Boost. During Holiday Mode, `HOLIDAY_MODE_OFF` displays Off and `HOLIDAY_MODE_ON` displays Schedule. | 1.0.0; DEV-001 (unreleased) |
| Presence Mode | Selector | Home / Away / Away (Geofencing) |  |
| Holiday Mode | Switch | Enables or disables Holiday Mode for this Tado home. If the saved holiday end date is still in the future, enabling retains the existing dates (Scheduled or Active as appropriate). If the saved period has expired or is invalid, enabling creates a new seven-day holiday period starting immediately. Disabling preserves the stored dates and room/hot-water holiday settings. | DEV-001 (unreleased) |
| Refresh Holiday Mode | Button | Immediately re-reads this home’s Holiday Mode information from Tado, updating the Holiday Status, Start and End sensors. Holiday data is also refreshed periodically and at scheduled start/end boundaries. | DEV-001 (unreleased) |
| Resume Hot Water Schedule | Button | Resume hot water schedule | 1.0.0 |
| Resume Schedules | Button | Resume heating schedules |  |
| Turn Off All | Button | Turn off heating to all rooms |  |
| Turn Off Hot Water | Button | Turn off hot water heating | 1.0.0 |


| Sensors | Description | Version |
| :--- | :--- | :---: |
| API calls today | The number of API calls made in the current 24-hour period. With an auto-assist subscription up to 20,000 calls can be made; without that only 100 calls are allowed. |  |
| API current usage | Shows the percentage of the current daily allowance that has been used. |  |
| API estimated daily usage | Estimates how the current usage rate will impact the daily allowance. Useful when configuring polling frequency and can exceed **100%**. It is deliberately unavailable during the first hour after the daily reset, during which time it will display `Unknown`. | 1.0.0 |
| API quota limit | Depends on whether an auto-assist subscription is present. |  |
| API quota remaining | The number of API calls that remain within the daily allowance. |  |
| API reset time | The daily API allowance is reset each day. The time that this happens varies between different users. This sensor monitors the API calls remaining to determine when the reset time will be. Note that this sensor will read 'Unknown' for up to 24 hours after first being installed, while the integration is determining when the reset time is. | 1.0.0 |
| Hot water boost ends | If water heating is boosted, this sensor records when the boost period will end. It is Unknown if water heating is not boosted. | 1.0.0 |
| Hot water next schedule change | Records the time for the next scheduled water heating change. Available when Tado provides a next-change timestamp in `SCHEDULE_ON` or `SCHEDULE_OFF` state; otherwise Unknown (including Away modes, explicitly Off, or no forthcoming change) | 1.0.0 |
| Hot Water State | Records the hot-water state reported by Tado: `OFF`, `AWAY_OFF`, `AWAY_ON`, `SCHEDULE_ON`, `SCHEDULE_OFF`, `BOOST`, `HOLIDAY_MODE_OFF` or `HOLIDAY_MODE_ON`. Explicitly switching hot water off can produce `OFF` even while Away; `AWAY_OFF` is distinct. During Holiday Mode, `HOLIDAY_MODE_OFF` maps to Hot Water Mode **Off**, while `HOLIDAY_MODE_ON` maps to **Schedule**. | 1.0.0; DEV-001 (unreleased) |
| Holiday Status | The effective status of this home's Holiday Mode, determined from its enabled setting and configured start/end dates. **Off**: disabled, irrespective of stored dates. **Scheduled**: enabled, start date is in the future. **Active**: enabled, current time is within the holiday period. **Ended**: still enabled but the end date has passed; holiday settings are no longer in effect. If the information is missing or inconsistent, the sensor may have no valid state. See the detailed table below. | DEV-001 (unreleased) |
| Holiday Start | The configured start of the holiday period, displayed as an absolute **local** date and time (e.g. `02 Dec 2026, 12:00`). The original UTC instant is available in the `utc_datetime` attribute. A stored date may remain visible even when Holiday Mode is Off or Ended. | DEV-001 (unreleased) |
| Holiday End | The configured end of the holiday period, displayed as an absolute **local** date and time, with the UTC instant in the `utc_datetime` attribute. A stored date may remain visible after the period ends or Holiday Mode is disabled. | DEV-001 (unreleased) |
| Outdoor Temperature | The outdoor temperature as returned by Tado for your location |  |
| Solar Intensity | A percentage as returned by Tado for your location |  |
| Weather State | The weather description as returned by Tado for your location. Depends on weather being selected in the home configuration. |  |


### Holiday Mode status and sensor attributes

Holiday Mode is managed independently for each Tado home. The **Holiday Status** sensor describes the *effective time-based state*; it is not simply a duplicate of the Holiday Mode switch.

| Holiday Status | Holiday Mode enabled | Date conditions | Meaning | Version |
| :--- | :---: | :--- | :--- | :---: |
| Off | No | Any stored dates | Explicitly disabled; the saved dates can still be retained for later use. | DEV-001 (unreleased) |
| Scheduled | Yes | Start is later than now; valid end follows start | Configured to begin automatically at the start time. | DEV-001 (unreleased) |
| Active | Yes | Start is at or before now; end is after now | Holiday Mode is currently in its configured period. | DEV-001 (unreleased) |
| Ended | Yes | End is at or before now; valid start precedes end | The configured period has expired, although the enabled flag remains set. It is no longer operational. | DEV-001 (unreleased) |
| Unknown / unavailable | Unknown or invalid data | API data missing, unavailable, or dates invalid | A reliable status cannot be determined. | DEV-001 (unreleased) |

For example, a holiday set for 1–7 November is **Scheduled** before its start, **Active** during its configured interval, and **Ended** after its end if it remains enabled. Manually switching it off instead produces **Off**. Switching an expired holiday on again creates a fresh seven-day period; if the existing end is still in the future, the original dates are retained.

| Entity | Attribute | Description | Version |
| :--- | :--- | :--- | :---: |
| Holiday Status | `enabled` | Boolean enabled flag reported by Tado. It can remain `true` when status is Ended. | DEV-001 (unreleased) |
| Holiday Status | `active` | Active flag reported by Tado; the sensor's effective status is calculated from the enabled flag and date interval, so it may differ briefly around a scheduled boundary. | DEV-001 (unreleased) |
| Holiday Status | `room_settings` | Room-specific Holiday Mode settings returned by Tado. | DEV-001 (unreleased) |
| Holiday Status | `hot_water_settings` | Hot-water Holiday Mode settings returned by Tado, where supplied. | DEV-001 (unreleased) |
| Holiday Start | `utc_datetime` | Start instant in ISO-8601 UTC-offset form, suitable for templates requiring a machine-readable timestamp. | DEV-001 (unreleased) |
| Holiday End | `utc_datetime` | End instant in ISO-8601 UTC-offset form. | DEV-001 (unreleased) |

The Start and End sensor **states are formatted local text**, not native timestamp sensor values. For setting an explicit holiday period, including past start dates, use the `tado_x.set_holiday_mode` action described in [ACTIONS.md](ACTIONS.md). The action provides date/time selectors for both dates; ISO-8601 values can also be supplied in YAML. Holiday status is refreshed independently of room schedule-profile comparison, and relevant Holiday Mode operations also refresh hot-water information.


#### API usage estimates

These calculations do not require additional API calls. They are estimates, not guarantees of Tado's future quota behaviour.

| Diagnostic | Description | Version |
| :--- | :--- | :---: |
| Tado Home ID | The internal Tado identifier for this home. | 1.0.0 |


## Tado X Room

| Controls | Type | Description | Version |
| :--- | :--- | :--- | :--- |
| ROOMNAME | Climate | Climate device associated with the room. Allows temperature control, HVAC modes (Heat/Off/Auto), Pre-set modes (schedule/Home/Away/auto) |  |
| Open Window | Switch | On if an open window is detected (only available with an auto-assist subscription). Can be set to Off to cancel the open window state if required. |  |


| Sensors | Type | Description | Version |
| :--- | :--- | :--- | :---: |
| Comfort Level | Sensor (text) | As returned by Tado |  |
| Heating | Binary sensor | Normal or Hot (when calling for heat) |  |
| Heating Power | Sensor (percentage) | The level of heat required, from 0% to 100% |  |
| Heating Time Today | Sensor (hours) | The number of hours today when calling for heat |  |
| Humidity | Sensor (percentage) | The humidity of the room |  |
| Humidity Level | Sensor (text) | As returned by Tado |  |
| Manual Control | Binary sensor | Off if the temperature is being set using the current schedule. On if the temperature is set to a custom value. |  |
| Next Schedule Change | Sensor (timestamp) | Date and time when the next schedule will start | 1.0.0 |
| Next Schedule Power | Sensor (text) | Scheduled `ON` or `OFF` state at the next change, when available | 1.0.0 |
| Next Schedule Temperature | Sensor (temperature) | Target temperature at the next scheduled change, when available | 1.0.0 |
| Temperature | Sensor | The current room temperature |  |
| Window Open | Binary sensor | On if an open window is detected (only available with an auto-assist subscription) |  |


| Configuration | Type | Description | Version |
| :--- | :--- | :--- | :---: |
| Refresh Schedule | Button | Click to refresh the heating schedule information. Schedules are refreshed periodically, but if the heating timing is altered from the Tado app, it is possible for the Home Assistant settings to be out of sync. Click this button to update the settings. | 1.0.0 |
| Restore schedule profile | Selector | Loads a saved heating schedule for the room. See Heating Schedules for a full description. | 1.0.0 |
| Save schedule profile | Selector | Saves the current room's heating schedule to one of the named locations. See Heating Schedules for a full description | 1.0.0 |
| Termination Type | Selector | For each room, the Tado app allows setting of how manual device control will alter the temperature. Allowed settings: `TIMER` - the new temperature lasts for Timer Duration; `MANUAL` - the new temperature is set indefinitely, until you resume schedule; `NEXT_TIME_BLOCK` - the new temperature is set until the next scheduled change | 1.0.0 |
| Timer Duration | Number | This sets the length of time associated with Termination Type of Timer. | 1.0.0 |

The Restore schedule profile selector also exposes match information as attributes:

| Restore schedule profile attributes | Values | Description | Version |
| :--- | :--- | :--- | :--- |
| `match_status` | Matched / Custom / Unknown | Whether the live Home/Away schedule matches a known profile, differs from all known profiles, or could not be determined | 1.0.0 |
| `matching_profiles` | List of names | All effective profiles matching the most recently read live Home/Away schedule | 1.0.0 |
| `last_applied_profile` | Name or null | Profile most recently applied through the integration | 1.0.0 |
| `saved_profiles` | List of names | Profiles saved specifically for this room (not all home defaults) | 1.0.0 |


### Room schedule profiles

The four named profiles are **Normal**, **Alternative**, **Eco** and **Not in use**. Each profile can store the room's full Home weekly schedule **and** Away configuration (including Away temperature and preheating where supported).

A room-specific profile takes precedence over its matching home-wide default on ordinary restore. To restore the home-wide template explicitly, use `tado_x.restore_schedule_profile_default` in **Developer Tools → Actions**. The default-profile selectors are not on the room device page. If no custom home default is saved, the integration has a predefined factory fallback. Custom home-wide profile classes can be created with `tado_x.create_schedule_profile`. Saving or resetting a default does **not** immediately alter active room schedules.

The integration initially captures the current room schedule as **Normal** if that room profile is missing. The **Custom** indication is based on the most recent comparison; when automatic checks are off, use **Refresh Schedule** to detect changes made outside Home Assistant.

## Tado X Devices

Applies to supported Tado X devices, including Radiator Valve X, Temperature Sensor X, Wireless Receiver X, Wired Smart Thermostat X and Bridge X

| Device info | Description | Version |
| :--- | :--- | :---: |
| Firmware | Shows the firmware version associated with the device |  |
| Serial number | Gives the serial number of the Tado X device | 1.0.0 |


## Tado Radiator Valve X

| Controls | Type | Description | Version |
| :--- | :--- | :--- | :--- |
| Child lock | Switch | Allows the child lock state to be read or set |  |


| Sensors | Description | Version |
| :--- | :--- | :--- |
| Battery | Normal if battery is charged |  |
| Battery Low | Normal if battery is charged |  |
| Connectivity | Connected if device is connected |  |
| Device Temperature | Temperature as recorded by the device |  |
| Temperature Offset | Temperature offset as applied by the device |  |


## Tado Temperature Sensor X

| Sensors | Type | Description | Version |
| :--- | :--- | :--- | :--- |
| Battery | Sensor | Normal if battery is charged |  |
| Battery Low | Binary sensor (battery) | Normal if battery is charged |  |
| Connectivity | Binary sensor (connectivity) | Connected if device is connected |  |
| Device Temperature | Sensor (temperature) | Temperature as recorded by the device |  |
| Temperature Offset | Sensor (temperature) | Temperature offset as applied by the device |  |


## Tado Bridge X

| Sensors | Type | Description | Version |
| :--- | :--- | :--- | :--- |
| Connectivity | Binary sensor (connectivity) | Connected if device is connected |  |


## Tado Wireless Receiver X (TR04)

| Sensors | Type | Description | Version |
| :--- | :--- | :--- | :--- |
| Connectivity | Binary sensor (connectivity) | Connected if device is connected |  |


---

# Climate attributes

In addition to standard climate properties (`hvac_modes`, `hvac_action`, `min_temp`, `max_temp`, `target_temp_step`, `current_temperature`, `temperature`) the climate entity associated with each room exposes the following additional attributes.

| Attribute | Values | Description | Version |
| :--- | :--- | :--- | :---: |
| `preset_modes` | schedule, home, away, auto | Standard climate entry |  |
| `current_humidity` | Percentage | The humidity reading |  |
| `heating_power` | 0-100 | The current heating power |  |
| `next_schedule_change` | date / time | When the next schedule change will occur | 1.0.0 |
| `next_schedule_temperature` |  | The temperature setting for the next schedule change | 1.0.0 |
| `manual_control_active` | true / false | false if the temperature control is being applied via a schedule |  |
| `manual_control_remaining_minutes` | Minutes or unavailable | Time remaining for a timed override, when available | 1.0.0 |
| `manual_control_type` | `MANUAL` / `TIMER` / `NEXT_TIME_BLOCK` | The type of manual control currently applied if `manual_control_active` is `true` |  |
| `open_window_detected` | true | `true` if a window open state is set |  |
| `open_window_ends_at` | date / time | Present if `open_window_detected` is `true` | 1.0.0 |
| `open_window_remaining_minutes` |  | Set if `open_window_detected` is `true` | 1.0.0 |



## Scope and compatibility

Room entity unique IDs and room actions are home-scoped; the same numeric room ID may occur in different Tado homes. The precise entity ID assigned by Home Assistant can vary for existing installations and user-renamed entities. Device and entity availability depends on the configured options, hardware, subscription and Tado API responses. For action parameters, see [ACTIONS.md](ACTIONS.md).
