# W9Pro

Web Bluetooth controller (`index.html`). Chrome/Edge on desktop; Bluefy on iOS (Safari has no Web Bluetooth).

## BLE
| | |
|---|---|
| Name prefix | `SKG` |
| Service | `0000ffe0-3c17-d293-8e48-14fe2e4da212` |
| Write | `0000ffe1-3c17-d293-8e48-14fe2e4da212` |
| Notify | `0000ffe2-3c17-d293-8e48-14fe2e4da212` |

## Frame
`A5 C0 <len> <cmd> <value> 03 <bcc>`
- `len` = payload length + 5
- `bcc` = XOR of all preceding bytes

## Commands
| cmd | function | payload |
|---|---|---|
| 0x31 | mode | `[index, 03]` |
| 0x32 | pulse level | `[0-15, 03]` |
| 0x33 | heat | `[0-3, 03]` |
| 0x35 | vibration mode | `[0-3, 03]` |
| 0x36 | vibration level | `[0-15, 03]` |
| 0x3A | timer (min) | `[minutes, 03]` |
| 0x3C | pause / resume | `[2 / 1, 03]` |
| 0x01 | power off | `[2, 03]` |
| 0x11 | heartbeat | `[yy-2000, mm, dd, hh, mm, ss]` |

## Modes (cmd 0x31, value = index)
Built-in 1-4: Soothing Activation, Muscle Exercise, Deep Beating, Heat.
Online 5-12: recipe waveforms, present only if synced by the vendor app.

## Heartbeat
cmd 0x11 -> reply 0x91: bytes[4:6] run seconds (big-endian), byte[6] battery %.
