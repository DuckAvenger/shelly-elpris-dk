# shelly-elpris-dk

## DK spot price control for Shelly devices

**shelly-elpris-dk** is a DK-only Shelly script for controlling Shelly outputs based on Danish hourly spot prices, manual Danish grid tariffs, energy tax and VAT.

The script is intended for Danish price areas **DK1** and **DK2** and uses the Danish spot price API from **elprisenligenu.dk**. This version is limited to Danish price areas and Danish setup values.

[License: AGPL v3](https://www.gnu.org/licenses/agpl-3.0)

---

## Current script version

| Item | Value |
|---|---|
| Script name | `shelly-elpris-dk.js` |
| Runtime version | `3.1.5_12` |
| Build header | `shelly-elpris-dk 3.1.5 build 12` |
| Supported price areas | `DK1`, `DK2` |
| Price API | `https://www.elprisenligenu.dk/api/v1/prices/` |
| Price key | `DKK_per_kWh` |

---

## Main features

- **DK-only support** for Danish price areas `DK1` and `DK2`.
- **Hourly spot prices** from `elprisenligenu.dk`.
- **Manual Tarifmodel 3.0 support** with separate values for:
  - Lavlast: `00:00-06:00`
  - Højlast: `06:00-17:00`
  - Spidslast: `17:00-21:00`
  - Højlast: `21:00-24:00`
- **Manual energy tax** in `kr/kWh`.
- **VAT handling** via separate VAT percentage field.
- **Status UI** showing current price, spot/fee split, Tarifmodel 3.0 values and energy tax.
- **History UI** for logged output actions and state changes.
- **Setup UI** for region, tariffs, energy tax, VAT, outputs, fallback hours, overrides and operating mode.
- **Help UI** with embedded guidance for the most important setup fields.
- **DK-only logic** without foreign price zone routing.

---

## Price calculation

The script calculates the effective hourly price by combining spot price, manual grid tariff, energy tax and VAT.

```text
Effective price = spot price + Tarifmodel 3.0 grid tariff + energy tax + VAT
```

The tariff period is selected from the hour of the day:

| Time period | Internal config field | Meaning |
|---|---|---|
| `00:00-06:00` | `night` | Lavlast |
| `06:00-17:00` | `day` | Højlast |
| `17:00-21:00` | `dayw` | Spidslast |
| `21:00-24:00` | `nightw` | Højlast |
| All hours | `tax` | Energiafgift |
| All hours | `vat` | VAT / moms |

---

## Price data source

The DK-only script uses:

```text
https://www.elprisenligenu.dk/api/v1/prices/
```

The script expects the API to return hourly price data using the key:

```text
DKK_per_kWh
```

The current implementation is hour-based and expects a normal 24-hour price structure.

---

## Manual nettarif and energy tax

The script does not automatically fetch grid tariffs from DataHub or from Danish grid companies. Tariffs must be entered manually in the Setup UI.

Useful reference for identifying your Danish grid company and nettarif:

```text
https://selectra.dk/netselskaber
```

Typical Tarifmodel 3.0 periods:

| Period | Time range |
|---|---|
| Lavlast | `00:00-06:00` |
| Højlast | `06:00-17:00` |
| Spidslast | `17:00-21:00` |
| Højlast | `21:00-24:00` |

Energy tax is entered as `kr/kWh`, for example:

```text
0.90
```

VAT is entered as a percentage, for example:

```text
25
```

If manually entered tariffs and taxes already include VAT, set VAT to `0` to avoid adding VAT twice.

---

## Supported Shelly platform

The script is primarily intended for Shelly Gen2 devices and may also work on selected Gen3/Gen4 devices when Matter is disabled.

| Device | Gen2 | Gen3 | Gen4 |
|---|---:|---:|---:|
| Plus 1 | OK | Not tested | Not tested |
| Plus 1PM | OK | Not tested | OK |
| Plus 2PM | OK | Not tested | Not tested |
| Pro 2 | OK | Not tested | Not tested |
| Pro 3 | OK | Not tested | Not tested |
| Plug S | OK | OK | Not tested |
| Mini PM | Not tested | Shows instability | Not tested |

> Device compatibility depends on firmware version, script memory, enabled Shelly features and local configuration.

---

## Getting started

1. Connect your Shelly device to the network.
2. Update Shelly firmware to a recent stable version.
3. Set timezone to:

```text
Europe/Copenhagen
```

4. Open the Shelly Web UI.
5. Create a new script.
6. Paste the content of:

```text
shelly-elpris-dk.js
```

7. Save and start the script.
8. Open the script console and look for output similar to:

```text
elpris: v.3.1.5_12
elpris: URL http://<device-ip>/script/<script-id>
```

9. Open the displayed URL in a browser.

---

## Built-in web interface

The Shelly device exposes a lightweight HTTP UI with four tabs.

| Tab | Description |
|---|---|
| Status | Current state, price, tariff, energy tax, output state and active logic |
| History | Logged actions and output state changes |
| Setup | Region, Tarifmodel 3.0, energy tax, VAT, outputs, modes and overrides |
| Help | Embedded guidance for the main setup fields |

---

## Important setup fields

### Region

Choose `DK1` or `DK2`.

### Tarifmodel 3.0

Enter the current nettarif values in `kr/kWh` for the four tariff periods.

### Energiafgift

Enter the current Danish energy tax in `kr/kWh`.

### VAT / moms

Enter VAT percentage. Use `25` for normal Danish VAT if values are entered excluding VAT.

### Mode

The script supports multiple operating modes:

- **Manual mode**: output is manually controlled.
- **Price limit**: output is activated when the calculated price is below the selected limit.
- **Cheapest hours**: output is activated during the cheapest hours in the selected period.

### Shelly outputs spec

Use output `0` for a single relay, or comma-separated output numbers such as:

```text
0,1
```

### ON-time

Limits how long the output may remain on during an hour.

### Backup hours

Fallback hours used if price data is unavailable.

### Overrides

Fixed hourly overrides can force output ON, OFF or normal control.

---

## KVS configuration

The script stores common configuration under:

```text
elpris
```

Instance-specific configuration is stored under:

```text
elpris-1
elpris-2
```

If upgrading from older or experimental builds, it is recommended to clear old KVS keys before first start to avoid stale configuration values.

---

## Notes and limitations

- The script is DK-only and supports only `DK1` and `DK2`.
- The script does not fetch Danish grid tariffs automatically.
- The script assumes hour-based price and control logic.
- If the spot price API changes format, the script may need an update.
- If calculated prices look wrong, check region, VAT, nettarif and energy tax first.

---

## Background and credits

Developed and adapted by **Soviet9773Red** and contributors.

Based on ideas and architecture from **shelly-porssisahko** by **Jussi Isotalo**.

Additional DK-specific adaptation and testing: **S. Impagliazzo**.

---

## Repository

```text
https://github.com/Soviet9773Red/shelly-elpris-dk
```

---

## License

This project is licensed under the GNU Affero General Public License v3.0.

```text
AGPL-3.0
```
