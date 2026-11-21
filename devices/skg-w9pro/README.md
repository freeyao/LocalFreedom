# W9Pro

Web Bluetooth controller (`index.html`). Chrome/Edge on desktop; Bluefy on iOS (Safari has no Web Bluetooth). The belt accepts one BLE connection at a time, so disconnect the official app first.

## BLE
| | |
|---|---|
| Name prefix | `SKG` |
| Service | `0000ffe0-3c17-d293-8e48-14fe2e4da212` |
| Write   | `0000ffe1-3c17-d293-8e48-14fe2e4da212` |
| Notify  | `0000ffe2-3c17-d293-8e48-14fe2e4da212` |

## Frame
`A5 C0 <len> <cmd> <value> 03 <bcc>`
- `len` = payload length + 5
- `03` = operation source (APP)
- `bcc` = XOR of all preceding bytes
- Control path is plaintext; no pairing / auth.

## Session
Pulse / mode / vibration only take effect after **StartUse (0x12)** — without it the device ACKs but keeps its button-default program. Flow: connect -> Start -> adjust. Timer (0x3A) applies without Start.

## Commands
| cmd | function | payload |
|---|---|---|
| 0x12 | start session | `[mode, level, heat, voice, minutes]` |
| 0x31 | pulse technique | `[cmdIndex, 03]` |
| 0x32 | pulse level | `[0-9, 03]` |
| 0x33 | heat | `[0 off / 1 low / 2 mid / 3 high, 03]` |
| 0x35 | vibration mode | `[1-3, 03]` |
| 0x36 | vibration level | `[0-3, 03]` |
| 0x3A | timer (min) | `[minutes, 03]` |
| 0x01 | power off | `[2, 03]` |
| 0x11 | heartbeat | `[yy-2000, mm, dd, hh, mm, ss]` |

Replies echo the device's actual current value, so a clamp or a rejected value shows as a mismatching echo. Pause (0x3C) returns an exception (0xEF) on this model — unsupported.

## Pulse techniques (cmd 0x31)
`cmdIndex` selects a technique. The loaded set is **device-specific** (synced from the owner's account), so the index -> name map is per device. Derive it by sweeping ChangeModel 1..N and keeping the indices that echo back unchanged. This unit: 1-8 loaded.

## Heartbeat / battery
cmd 0x11 -> reply 0x91: bytes[4:6] run seconds (big-endian), byte[6] battery. Battery is approximate on this model.
