# Home Assistant Add-on: EnOcean to MQTT Bridge (FSB14 position-persistence fork)

![Supports aarch64 Architecture][aarch64-shield] ![Supports amd64 Architecture][amd64-shield] ![Supports armhf Architecture][armhf-shield] ![Supports armv7 Architecture][armv7-shield] ![Supports i386 Architecture][i386-shield]

Access your EnOcean devices from Home Assistant through MQTT.

This is a fork of the experimental `support-eltako` add-on with a fix for the
Eltako **FSB14/FSB61/FJ62 shutter/blind position** being lost on restart.

---

## 🇩🇪 Was dieser Fork ändert

**Problem:** Bei den Eltako-FSB14-Beschattungsaktoren ging die **Zwischenposition**
der Rolläden/Raffstores bei jedem Home-Assistant-Neustart verloren (sie fielen
auf 0 % / geschlossen). Ursache: Der FSB14 meldet per Funk nur die *Laufzeit der
letzten Teilfahrt* (relativ), nicht die absolute Position. Diese wurde bisher nur
im HA-Template aufsummiert und war nach einem Neustart weg.

**Lösung (Wurzel-Fix im Daemon):**
- Der Daemon **akkumuliert die Position selbst**, persistiert sie in der Geräte-DB
  (TinyDB) und veröffentlicht sie als absolutes `POS`-Feld (retained). Das
  `position_template` liest nur noch `POS`. Dadurch übersteht die Position sowohl
  **HA-Neustarts** (retained MQTT) als auch **Add-on-Neustarts** (TinyDB).
- Die Laufzeit pro Aktor (`shut_time`) wird **nur noch in der Geräte-Datei**
  (`enoceanmqtt.devices`, z. B. `shut_time=25`) gepflegt. Der Daemon veröffentlicht
  sie als Cover-Attribut; die früheren `shut_time`-`number`-Entities entfallen.
- **An/Aus-Aktoren (FSR14) sind nicht betroffen** – deren Schaltzustand ist absolut.

**Wichtig (Build):** Das Add-on baut lokal. Das Basis-Image ist bewusst auf
`*-base:3.20` (Python 3.12) gepinnt und `beautifulsoup4<4.13` festgelegt. Mit
`:latest` (Python 3.14) bricht das EEP-Parsing der enocean-Bibliothek für manche
Profile (z. B. F6-Taster) – dann funktionieren z. B. Lichtschalter nicht mehr.

---

## 🇬🇧 What this fork changes

**Problem:** With Eltako FSB14 shutter actuators the **intermediate position** of
the blinds/shutters was lost on every Home Assistant restart (they dropped to
0 % / closed). Reason: the FSB14 only reports the *running time of the last
movement* (relative), not an absolute position. That position was only summed up
in the HA template and gone after a restart.

**Fix (root cause, in the daemon):**
- The daemon now **accumulates the position itself**, persists it in the device DB
  (TinyDB) and publishes it as an absolute, retained `POS` field. The
  `position_template` just reads `POS`. The position therefore survives both
  **HA restarts** (retained MQTT) and **add-on restarts** (TinyDB).
- Each actuator's run time (`shut_time`) is maintained **only in the devices file**
  (`enoceanmqtt.devices`, e.g. `shut_time=25`). The daemon publishes it as a cover
  attribute; the former `shut_time` `number` entities are gone.
- **On/off actuators (FSR14) are not affected** – their switch state is absolute.

**Important (build):** The add-on builds locally. The base image is intentionally
pinned to `*-base:3.20` (Python 3.12) and `beautifulsoup4<4.13`. With `:latest`
(Python 3.14) the enocean library's EEP parsing breaks for some profiles (e.g. F6
rocker switches) – which e.g. stops light switches from working.

---

Based on [mak-gitdev/HA_enoceanmqtt](https://github.com/mak-gitdev/HA_enoceanmqtt)
(`support-eltako`). Daemon fork: [t-ice/HA_enoceanmqtt @ `fsb14-position-persist`](https://github.com/t-ice/HA_enoceanmqtt/tree/fsb14-position-persist).

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[armhf-shield]: https://img.shields.io/badge/armhf-yes-green.svg
[armv7-shield]: https://img.shields.io/badge/armv7-yes-green.svg
[i386-shield]: https://img.shields.io/badge/i386-yes-green.svg
