# AirQuality Data Acquisition API (discussion)

## Challenges

- Sensors can have limited buffers and cannot store a lot of data in case of server downtime, some of them can store no data at all.
- Some sensors can be misconfigured or compromised and could pose risk of incidental or intentional DDoS.
- Sensor ability to transfer data securely and/or solve security challenges can be limited.
- Sensor location is a sensitive information.

**Our final design should address all of the above.**

## STANDARDS

### Pollutants and their measurement units

- Carbon Monoxide - Concentration, unit: **mg/m³**
- Lead - Concentration, unit: **µg/m³**
- Nitrogen Dioxide - Concentration, unit: **µg/m³**
- Ozone - Concentration, unit: **µg/m³**
- Particulate Matter (PM10) - Concentration, unit: **µg/m³**
- Particulate Matter (PM2.5) - Concentration, unit: **µg/m³**
- Sulfur Dioxide - Concentration, unit: **µg/m³**

## Protocol (to be selected)

- MQTT (ISO/IEC PRF 20922)
- HTTP(S)
- UDP

### HTTP Payload (to be selected)

- JSON (HTTP POST)
- BSON (HTTP POST)
- BINARY (HTTP POST)

### Sensor authentication and identification

(TBD)

### New sensor auto-registration

(TBD)