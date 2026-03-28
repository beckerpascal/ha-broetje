# Supported Entities

This document covers the ISR Plus module in detail. For IWR/GTW-08 entities, see the register map CSV referenced at the bottom.

## Legend

**Visibility** column:
| Symbol | Meaning |
|--------|---------|
| ✅ | Primary — visible on the device page by default |
| 🔧 | Diagnostic — visible under the Diagnostics tab by default |
| — | Diagnostic — hidden by default; must be enabled manually in the entity registry |

**R/W** column reflects the Brötje ISR specification. Write access is not currently implemented for ISR entities in Home Assistant — all ISR entities are read-only in practice.

---

## IWR / GTW-08 Summary

- IWR includes a **climate subsystem** with per-zone thermostat entities (`climate`), including current temperature, target setpoint, and HVAC mode/action mapping.
- IWR supports **write access for selected holding registers** via `number` and `select` entities:

  | Register | Entity | Description |
  |----------|--------|-------------|
  | 649–651 | Zone comfort setpoints | Comfort room temperature setpoints (per zone) |
  | CP02X | Zone control mode | Operating mode per zone (scheduling / manual / off) |
  | CP20X | Zone room setpoint (manual) | Manual room temperature setpoint |
  | 665 | DHW comfort setpoint | DHW target temperature in comfort mode (10–80 °C) |
  | 666 | DHW reduced setpoint | DHW target temperature in reduced mode (10–80 °C) |
  | 667 | DHW holiday setpoint | DHW target temperature in holiday mode (10–80 °C) |
  | 668 | DHW anti-legionella setpoint | DHW target temperature for legionella protection (10–80 °C) |
  | 686 | DHW calorifier hysteresis | DHW storage hysteresis (0.5–20 °C) |
  | 1105 | Zone room temp measured | External room temperature sensor injection |

- IWR entities are grouped under **zone devices** and **functional sub-devices** (boiler / service / solar / buffer / hybrid). Sub-devices are auto-detected; orphaned entries are removed on reload.

For the complete IWR register inventory and read/write status, see:
[`custom_components/broetje_heating/register_map.csv`](custom_components/broetje_heating/register_map.csv)

---

## ISR — Heating Circuit 1 (Heizkreis 1)

### Sensors

| Entity | Description | Register | Unit | R/W | Visibility |
|--------|-------------|----------|------|-----|------------|
| HC1 Operating mode | Operating mode (Protection / Auto / Reduced / Comfort) | 1024 | — | R/W | ✅ |
| HC1 Flow temperature | Current flow temperature | 1046 | °C | R | ✅ |
| HC1 Room temperature | Current room temperature | 1042 | °C | R | ✅ |
| HC1 Flow setpoint | Current flow temperature setpoint | 1048 | °C | R | ✅ |
| HC1 Room setpoint | Current room temperature setpoint | 1044 | °C | R | ✅ |
| HC1 Status | Heating circuit status code | 1054 | — | R | ✅ |
| HC1 Comfort setpoint | Comfort mode room temperature setpoint | 1025 | °C | R/W | 🔧 |
| HC1 Reduced setpoint | Reduced mode room temperature setpoint | 1026 | °C | R/W | 🔧 |
| HC1 Mixer boost | Mixer overshoot temperature | 1077 | °C | R/W | 🔧 |
| HC1 Pump speed | Current pump speed | 1101 | % | R | 🔧 |
| HC1 Frost protection setpoint | Frost protection temperature | 1027 | °C | R/W | — |
| HC1 Heating curve slope | Heating curve steepness | 1028 | — | R/W | — |
| HC1 Heating curve offset | Heating curve parallel shift | 1029 | °C | R/W | — |
| HC1 Summer/Winter threshold | Outside temp threshold for summer mode | 1030 | °C | R/W | — |
| HC1 Day heating threshold | Day heating limit temperature | 1032 | °C | R/W | — |
| HC1 Flow setpoint minimum | Minimum flow temperature setpoint | 1034 | °C | R/W | — |
| HC1 Flow setpoint maximum | Maximum flow temperature setpoint | 1035 | °C | R/W | — |
| HC1 Flow setpoint room thermostat | Flow setpoint when using room thermostat | 1036 | °C | R/W | — |
| HC1 Room influence | Room temperature influence factor | 1038 | % | R/W | — |
| HC1 Pump speed minimum | Minimum pump speed setting | 1128 | % | R/W | — |
| HC1 Pump speed maximum | Maximum pump speed setting | 1129 | % | R/W | — |

### Binary Sensors

| Entity | Description | Register | Values | Visibility |
|--------|-------------|----------|--------|------------|
| HC1 Enabled | Heating circuit active | 1055 | 0=Off, 1=On | ✅ |
| HC1 Pump | Heating circuit pump running | 1095 | 0=Off, 1=On | ✅ |
| HC1 Room thermostat demand | Heat demand from room thermostat | 1050 | 0=No demand, 1=Demand | ✅ |
| HC1 Mixer open | Mixer valve opening | 1097 | 0=Off, 1=On | 🔧 |
| HC1 Mixer close | Mixer valve closing | 1099 | 0=Off, 1=On | 🔧 |

### Operating Modes

| Value | German | English |
|-------|--------|---------|
| 0 | Schutzbetrieb | Protection |
| 1 | Automatik | Automatic |
| 2 | Reduziert | Reduced |
| 3 | Komfort | Comfort |

---

## ISR — Domestic Hot Water (Trinkwasser / TWW)

### Sensors

| Entity | Description | Register | Unit | R/W | Visibility |
|--------|-------------|----------|------|-----|------------|
| DHW Operating mode | Operating mode (Off / On / Eco) | 10240 | — | R/W | ✅ |
| DHW Setpoint | Nominal setpoint temperature | 10241 | °C | R/W | ✅ |
| DHW Status | DHW status code | 10273 | — | R | ✅ |
| DHW Current setpoint | Current active DHW setpoint | 11379 | °C | R | 🔧 |
| DHW Reduced setpoint | Reduced setpoint temperature | 10242 | °C | R/W | 🔧 |
| DHW Release mode | Release mode (24h / heating program / DHW program) | 10243 | — | R/W | — |
| DHW Legionella mode | Legionella function (Off / Periodic / Fixed day) | 10244 | — | R/W | — |
| DHW Legionella interval | Legionella periodic interval | 10245 | days | R/W | — |
| DHW Legionella weekday | Fixed weekday for legionella function | 10246 | — | R/W | — |
| DHW Legionella time | Time of day for legionella function | 10247 | min | R/W | — |
| DHW Legionella setpoint | Legionella temperature setpoint | 10249 | °C | R/W | — |
| DHW Legionella dwell time | Dwell time at legionella setpoint | 10250 | min | R/W | — |
| DHW Circulation setpoint | Circulation temperature setpoint | 10263 | °C | R/W | — |

### DHW Operating Modes

| Value | German | English |
|-------|--------|---------|
| 0 | Aus | Off |
| 1 | Ein | On |
| 2 | Eco | Eco |

### DHW Release Modes

| Value | German | English |
|-------|--------|---------|
| 0 | 24h/Tag | 24h/day |
| 1 | Zeitprogramme Heizkreise | Heating circuit program |
| 2 | Zeitprogramm 4/TWW | DHW program |

### Legionella Modes

| Value | German | English |
|-------|--------|---------|
| 0 | Aus | Off |
| 1 | Periodisch | Periodic |
| 2 | Fixer Wochentag | Fixed weekday |

### Weekdays

| Value | German | English |
|-------|--------|---------|
| 1 | Montag | Monday |
| 2 | Dienstag | Tuesday |
| 3 | Mittwoch | Wednesday |
| 4 | Donnerstag | Thursday |
| 5 | Freitag | Friday |
| 6 | Samstag | Saturday |
| 7 | Sonntag | Sunday |

---

## ISR — DHW Storage Tank (Trinkwasserspeicher)

### Sensors

| Entity | Description | Register | Unit | R/W | Visibility |
|--------|-------------|----------|------|-----|------------|
| DHW Tank temperature 1 | DHW tank temperature sensor 1 | 11264 | °C | R | ✅ |
| DHW Tank temperature 2 | DHW tank temperature sensor 2 | 11266 | °C | R | ✅ |
| DHW Circulation temperature | Circulation temperature | 11381 | °C | R | ✅ |
| DHW Charging temperature | Charging temperature | 11383 | °C | R | ✅ |
| DHW Pump speed | DHW pump speed | 11373 | % | R | 🔧 |
| DHW Intermediate circuit pump speed | Intermediate circuit pump speed | 11375 | % | R | — |
| DHW Charging time limit | Maximum charging time | 11280 | min | R/W | — |
| DHW Flow setpoint boost | Flow temperature boost | 11290 | °C | R/W | — |
| DHW Switching differential | Switching differential | 11294 | °C | R/W | — |
| DHW Max charging temperature | Maximum charging temperature | 11299 | °C | R/W | — |

### Binary Sensors

| Entity | Description | Register | Values | Visibility |
|--------|-------------|----------|--------|------------|
| DHW Pump | DHW pump state | 11369 | 0=Off, 1=On | ✅ |
| DHW Circulation pump | Circulation pump Q4 state | 11395 | 0=Off, 1=On | ✅ |
| DHW Intermediate pump | Intermediate circuit pump Q33 state | 11411 | 0=Off, 1=On | 🔧 |

---

## ISR — Buffer Storage Tank (Pufferspeicher)

### Sensors

| Entity | Description | Register | Unit | R/W | Visibility |
|--------|-------------|----------|------|-----|------------|
| Buffer temperature 1 | Buffer tank temperature sensor 1 (B4) | 17410 | °C | R | ✅ |
| Buffer temperature 2 | Buffer tank temperature sensor 2 (B41) | 17412 | °C | R | ✅ |
| Buffer status | Buffer storage status code | 17465 | — | R | ✅ |
| Buffer temperature 3 | Buffer tank temperature sensor 3 (B42) | 17463 | °C | R | — |
| Buffer setpoint | Buffer storage setpoint | 17466 | °C | R | — |

### Binary Sensors

| Entity | Description | Register | Values | Visibility |
|--------|-------------|----------|--------|------------|
| Buffer generator valve | Generator blocking valve Y4 | 17458 | 0=Off, 1=On | ✅ |
| Buffer return valve | Buffer return valve Y15 | 17468 | 0=Off, 1=On | ✅ |

---

## ISR — Boiler (Kessel)

### Sensors

| Entity | Description | Register | Unit | R/W | Visibility |
|--------|-------------|----------|------|-----|------------|
| Boiler temperature | Current boiler temperature | 24600 | °C | R | ✅ |
| Boiler setpoint | Current boiler setpoint | 24604 | °C | R | ✅ |
| Boiler status | Boiler status code | 24592 | — | R | ✅ |
| Burner status | Burner status code | 24593 | — | R | ✅ |
| Boiler return temperature | Return temperature | 24608 | °C | R | ✅ |
| Boiler relative power | Relative power | 24616 | % | R | ✅ |
| Total gas energy | Total gas energy (cumulative) | 24633 | kWh | R/W | ✅ |
| Total gas energy heating | Total gas energy for heating (cumulative) | 24629 | kWh | R/W | ✅ |
| Total gas energy DHW | Total gas energy for DHW (cumulative) | 24631 | kWh | R/W | ✅ |
| Operating hours stage 1 | Operating hours stage 1 (cumulative) | 24620 | h | R/W | ✅ |
| Boiler pump speed | Boiler pump speed | 24596 | % | R | 🔧 |
| Boiler fan speed | Current fan speed | 24612 | 1/min | R | 🔧 |
| Firing automaton phase | Current firing automaton phase (1–21) | 24641 | — | R | 🔧 |
| Start counter stage 1 | Start counter stage 1 (cumulative) | 24621 | — | R/W | 🔧 |
| Operating hours heating | Operating hours in heating mode (cumulative) | 24623 | h | R/W | 🔧 |
| Operating hours DHW | Operating hours in DHW mode (cumulative) | 24625 | h | R/W | 🔧 |
| Gas energy heating | Gas energy for heating (current period) | 24635 | kWh | R/W | 🔧 |
| Gas energy DHW | Gas energy for DHW (current period) | 24637 | kWh | R/W | 🔧 |
| Gas energy | Gas energy (current period) | 24639 | kWh | R/W | 🔧 |
| Boiler manual setpoint | Manual operation setpoint | 24576 | °C | R/W | — |
| Boiler nominal temp lift | Nominal temperature lift | 24577 | °C | R/W | — |
| Boiler nominal power | Nominal power | 24581 | kW | R/W | — |
| Boiler base stage power | Base stage power | 24582 | kW | R/W | — |
| Burner hours maintenance interval | Maintenance interval in hours | 24583 | h | R/W | — |
| Burner hours since maintenance | Hours since last maintenance | 24585 | h | R/W | — |
| Burner starts interval | Burner starts maintenance interval | 24586 | — | R/W | — |
| Burner starts since maintenance | Starts since last maintenance | 24588 | — | R/W | — |
| Fan speed service threshold | Fan speed threshold for service message | 24589 | 1/min | R/W | — |
| Boiler fan setpoint | Fan setpoint | 24613 | 1/min | R | — |
| Boiler fan control | Current fan control | 24614 | % | R | — |
| Ionization current | Ionization current | 24618 | µA | R | — |

### Binary Sensors

| Entity | Description | Register | Values | Visibility |
|--------|-------------|----------|--------|------------|
| Boiler pump Q1 | Boiler pump state | 24594 | 0=Off, 1=On | ✅ |
| Ion current message | Ion current alarm | 24591 | 0=Off, 1=On | ✅ |
| Generator lock | Generator lock via H-contact | 24644 | 0=Off, 1=On | — |

---

## ISR — General Functions (Allgemeine Funktionen)

### Sensors

| Entity | Description | Register | Unit | R/W | Visibility |
|--------|-------------|----------|------|-----|------------|
| Outdoor temperature | Outside air temperature | 35851 | °C | R | ✅ |
| Burner power mode | Burner power mode (Partial / Full / Max) | 35903 | — | R/W | 🔧 |
| Controller stop setpoint | Controller stop setpoint | 35906 | % | R/W | — |

### Binary Sensors

| Entity | Description | Register | Values | Visibility |
|--------|-------------|----------|--------|------------|
| Alarm relay status | Alarm relay state | 35887 | 0=Off, 1=On | ✅ |
| Chimney sweep function | Chimney sweep mode active | 35901 | 0=Off, 1=On | — |
| Manual operation | Manual operation mode | 35904 | 0=Off, 1=On | — |
| Controller stop function | Controller stop function active | 35905 | 0=Off, 1=On | — |

### Burner Power Modes

| Value | German | English |
|-------|--------|---------|
| 1 | Teillast | Partial load |
| 2 | Volllast | Full load |
| 3 | Maximale Heizlast | Maximum heating load |

---

## Scale Factors (ISR)

| Factor | Value | Used for |
|--------|-------|----------|
| Temperature | 1/64 ≈ 0.015625 | All temperature values |
| Heating curve slope | 1/50 = 0.02 | Heating curve steepness |
| Power | 1/10 = 0.1 | Power in kW |
| Percent (scaled) | 1/100 = 0.01 | Fan control %, ionization current |
| Hours | 1/3600 | Operating hours stored as seconds (32-bit registers only) |

---

## Entity Naming

Entity IDs follow the pattern:
- Sensors: `sensor.broetje_<key>`
- Binary sensors: `binary_sensor.broetje_<key>`

Example: `sensor.broetje_hc1_flow_temperature`

---

## IWR Entity Reference

For IWR/GTW-08 entities, see [`custom_components/broetje_heating/register_map.csv`](custom_components/broetje_heating/register_map.csv) for the complete register map with addresses, data types, descriptions (EN/DE), units, scaling factors, categories, and read/write status.

---

## Planned

- Additional heating circuits for ISR (HC2, HC3)
- Further expansion of IWR write support
