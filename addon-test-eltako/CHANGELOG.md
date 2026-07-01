## 0.0.14

- Fix: bridge did not recover from a prolonged peer outage (Pi power loss) - the
  addon ended up completely stopped. Now: reachability-probe before starting the
  daemon (no daemon against a dead link), hard SIGTERM->SIGKILL cleanup instead of
  a blocking `wait` (daemon does not always die on SIGTERM -> loop froze), and the
  supervision loop can never exit. Recovers automatically once the Pi is back.

## 0.0.13

- Fix: remove socat `-T30` inactivity timeout. EnOcean radio silence >30s is normal
  and was tearing down the bridge every quiet period (Serial port exception ->
  reconnect loop). Dead peers are still detected via TCP keepalive (~25s).

## 0.0.12

- Add optional `enocean_tcp` (host:port) mode: bridge a remote EnOcean serial
  gateway (e.g. ser2net) via socat into a local PTY, with keepalive-based
  reconnect and PTY/daemon pair-supervision. Replaces fragile USBIP passthrough.
- Falls back to the unchanged serial `enocean_port` path when `enocean_tcp` is empty.
- `boot: auto`; loosen `enocean_port` schema to `str?`.

## 0.0.11

- Merge develop + Add FJ62, TF61L, TF61J, FDG14

## 0.0.10

- Fix `unknown sensor` when using `sender` with model

## 0.0.9

- Fix issue with sender when using model

## 0.0.8

Fixing some issues:
- Missing `unit_of_measurement` for some voltage entities
- Missing `sender` and `default_data` with models

## 0.0.7

Minor update

## 0.0.6

- Fix issue on date and RSSI
- Fix issue in DB
- Add cover to FSB14 and FSB61
- Rework FUD14
- Add FUD61

## 0.0.5

Add:
- FSR61
- FHD60SB
- FSR14
- FUD14

## 0.0.4

- Fix minor issue
- Case insensitive model declaration

## 0.0.3

- Fix minor issue

## 0.0.2

- Add support for models
- Add FSB14 & FSB61

## 0.0.1

- First version with support for multiple EEP per address
