# WLED_Firmware_Files

Custom [WLED](https://github.com/wled/WLED) firmware builds for my devices.

## Devices

| Device | Boards | Usermods | Current WLED |
|---|---|---|---|
| [Lazy Grid Clock](#lazy-grid-clock) | ESP32, ESP8266 (NodeMCU v2) | Seven Segment Display Reloaded, SN Photoresistor | 16.0.1 |
| [Word Clock](#word-clock) | ESP32 | Word Clock | 16.0.1 |

All 16.0.1 builds come preconfigured to join the WiFi network `WLED`.

## Repository layout

| Folder | Contents |
|---|---|
| `17.0.0-dev-wordclock-fixes/` | Test builds (WLED 17.0 development, not a release) with the word clock usermod fixes: Lazy Grid Clock (ESP32, ESP8266), Word Clock (ESP32) |
| `16.0.1/` | Lazy Grid Clock (ESP32, ESP8266), Word Clock (ESP32), plus `platformio_override.ini` with the build configs |
| `0.14.0-b2/` | Word Clock (ESP32) |
| `0.14.0-b0/` | Lazy Grid Clock (ESP8266), Word Clock |
| `0.13.2-a0/` | Word Clock (ESP8266) |
| `0.13.0-b7/` | Auto Save usermod (ESP32, ESP8266) |
| `0.13.0-b6/` | Lazy Grid Clock (ESP32, ESP8266) |

## Building

**Option A – WLED Discord bot**

1. Enter `./build custom`
2. Paste the environment definition of the device (see below)

**Option B – locally with PlatformIO**

1. Check out WLED at tag `v16.0.1`
2. Copy `16.0.1/platformio_override.ini` into the WLED root folder
3. Build one device, e.g. `pio run -e lazy_grid_clock_esp32` (or `pio run` to build all)

Since WLED 16.0, usermods are selected with `custom_usermods = ...` instead of `-D USERMOD_xxx` flags.

## Flashing / upgrading

- **ESP8266:** can be updated via OTA from older versions.
- **ESP32:** devices on 0.13/0.14 **cannot** be updated via OTA to 16.x. Erase the flash first (`esptool erase_flash`), then flash via USB.

## Lazy Grid Clock

```ini
[env:lazy_grid_clock_esp32]
extends = env:esp32dev
build_flags = ${env:esp32dev.build_flags} -D CLIENT_SSID='"WLED"' -D CLIENT_PASS='"***REMOVED***"'
custom_usermods = seven_segment_display_reloaded SN_Photoresistor

[env:lazy_grid_clock_esp8266]
extends = env:nodemcuv2
build_flags = ${env:nodemcuv2.build_flags} -D WLED_DISABLE_ALEXA -D CLIENT_SSID='"WLED"' -D CLIENT_PASS='"***REMOVED***"'
custom_usermods = seven_segment_display_reloaded SN_Photoresistor
```

**After flashing:** enter the segment layout in the Seven Segment Display Reloaded usermod settings:

```
H: 0,22,21;0,1,2;2,3,4;4,17,26;24,25,26;22,23,24;2,19,24:44,65,66;44,45,46;46,47,48;48,61,70;68,69,70;66,67,68;46,63,68
M: 6,15,28;6,7,8;8,9,10;10,11,32;30,31,32;28,29,30;8,13,30:50,59,72;50,51,52;52,53,54;54,55,76;74,75,76;72,73,74;52,57,74
```

## Word Clock

```ini
[env:wordclock_esp32]
extends = env:esp32dev
build_flags = ${env:esp32dev.build_flags} -D CLIENT_SSID='"WLED"' -D CLIENT_PASS='"***REMOVED***"'
custom_usermods = usermod_v2_word_clock
```

## Legacy configs (WLED 0.13 / 0.14)

<details>
<summary>Lazy Grid Clock</summary>

```ini
[env:esp32dev_mod]
board = esp32dev
platform = ${esp32.platform}
build_unflags = ${common.build_unflags}
build_flags = ${common.build_flags_esp32} -D WLED_RELEASE_NAME=ESP32 -D USERMOD_SSDR -D USERMOD_SN_PHOTORESISTOR #-D WLED_DISABLE_BROWNOUT_DET
lib_deps = ${esp32.lib_deps}
monitor_filters = esp32_exception_decoder
board_build.partitions = ${esp32.default_partitions}

[env:nodemcuv2_mod]
board = nodemcuv2
platform = ${common.platform_wled_default}
platform_packages = ${common.platform_packages}
board_build.ldscript = ${common.ldscript_4m1m}
build_unflags = ${common.build_unflags}
build_flags = ${common.build_flags_esp8266} -D WLED_RELEASE_NAME=ESP8266 -D WLED_DISABLE_ALEXA -D USERMOD_SSDR -D USERMOD_SN_PHOTORESISTOR
lib_deps = ${esp8266.lib_deps}
```
</details>

<details>
<summary>Word Clock</summary>

```ini
[env:esp32dev_mod]
board = esp32dev
platform = ${esp32.platform}
build_unflags = ${common.build_unflags}
build_flags = ${common.build_flags_esp32} -D WLED_RELEASE_NAME=ESP32 -D USERMOD_WORDCLOCK #-D WLED_DISABLE_BROWNOUT_DET
lib_deps = ${esp32.lib_deps}
monitor_filters = esp32_exception_decoder
board_build.partitions = ${esp32.default_partitions}
```
</details>

<details>
<summary>WiFi settings (ESP8266)</summary>

```ini
[env:nodemcuv2]
board = nodemcuv2
platform = ${common.platform_wled_default}
platform_packages = ${common.platform_packages}
board_build.ldscript = ${common.ldscript_4m1m}
build_unflags = ${common.build_unflags}
build_flags = ${common.build_flags_esp8266} -D WLED_RELEASE_NAME=ESP8266 -D CLIENT_SSID='"WLED"' -D CLIENT_PASS='"***REMOVED***"'
lib_deps = ${esp8266.lib_deps}
```
</details>
