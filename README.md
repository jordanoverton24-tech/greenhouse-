# Greenhouse IoT Data Integration

This project combines soil sensor, weather, and irrigation data collected on
July 15, 2026. The integrated output is
[`source-data/integrated-report.xml`](source-data/integrated-report.xml).

## Source files

| File | Data used |
| --- | --- |
| `source-data/batch102.csv` | Reading date and time, soil sensor ID, and bed ID |
| `source-data/feed102.xml` | Relative humidity |
| `source-data/stream102.json` | Irrigation pump status and flow rate |

## Data dictionary

Each `<reading>` in `integrated-report.xml` represents one soil sensor reading
at a batch-file timestamp. Humidity and irrigation data are joined by exact
date and time.

| XML field | Location / type | Meaning | Units or values | Source |
| --- | --- | --- | --- | --- |
| `greenhouseReport` | Root element | Contains all integrated readings | — | Integrated output |
| `reading` | Repeating element | One record for a sensor at a timestamp | — | Integrated output |
| `date` | `reading` attribute | Date of the record | `MM/DD/YYYY` | `batch102.csv` |
| `time` | `reading` attribute | Time of the record | 12-hour time with AM/PM | `batch102.csv` |
| `sensorId` | `reading` attribute | Soil sensor identifier | For example, `SN-4821` | `batch102.csv` |
| `bedId` | `reading` attribute | Greenhouse bed identifier | For example, `BED-1` | `batch102.csv` |
| `humidity` | Element text; `unit` attribute | Relative humidity at the matching timestamp | Percent (`percent`) | `feed102.xml` |
| `irrigation` | Nested element | Irrigation data at the matching timestamp | See `zone`, `pumpStatus`, and `status` below | `stream102.json` |
| `zone` | `irrigation` attribute | Irrigation zone number | Integer, such as `2` | `stream102.json` |
| `pumpStatus` | `irrigation` attribute | Whether the pump was running | `on` or `off` | `stream102.json` |
| `status` | `humidity` or `irrigation` attribute when source data is unavailable | Indicates there was no source observation at the batch timestamp | `not-recorded-at-batch-time` | Integrated output |
| `flowRateGpm` | Element text; `unit` attribute | Irrigation water flow rate | Gallons per minute (`gpm`) | `stream102.json` |

When humidity or irrigation data is available for the batch timestamp, its
value is included. When no matching source observation exists, the relevant
element has `status="not-recorded-at-batch-time"` and no measurement value.
This indicates missing timestamp coverage, not a zero reading. Humidity values
are repeated across sensor records that share a timestamp because the weather
feed is not specific to an individual soil sensor.
