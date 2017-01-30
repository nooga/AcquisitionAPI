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

## Protocols (to be selected)

- MQTT (ISO/IEC PRF 20922)
- REST HTTP(S)

### Sensor authentication and identification

#### Sensor Unique Identifier (SUID)

This identifier is generated by the manufacturer.
Manufacturer name in SUID should be used after prior contact with `suid@opensmog.org`.

**Length:** Exactly 32 bytes.

- [MANUFACTURER NAME] - max 10chars [a-Z0-9] ex. `SONY`
- [MANUFACTURER UNIQUE NUMBER] - [a-Z0-9] variable length to fill 32 bytes.

**Example SUID:** `SONYHRBHURBLbhQloO487pi612345601`

#### Authentication

Sensor will receive `Secret Token` during registration.
For secure identification of the device `HMAC` will be used to send hash with every payload.

Secret Token length is 256 bytes.

### New sensor auto-registration

Desired scenario:

- Sensor is powered and connected to the internet.
- Device should use DHCP and start data acuisition.
- User goes to `sensor.opensmog.org`
- Enters the SUID (printed on package as well as o the device sticker). 
- User enters the address (or lat/lon) of sensor location.
- Device is ready.

## JSON Payload

Example:

```json
{
	"timestamp": 1485778030,
	"readings": [
		"PM25": 201.1
	]
}

```

### Reading types

- `CO` – Carbon Monoxide (unit: **mg/m³**)
- `PB` – Lead (unit: **µg/m³**)
- `NO2` – Nitrogen Dioxide (unit: **µg/m³**)
- `O2` – Ozone (unit: **µg/m³**)
- `PM10` – PM10 (unit: **µg/m³**)
- `PM25` – PM2.5 (unit: **µg/m³**)
- `SO2` – Sulfur Dioxide (unit: **µg/m³**)


## MQTT

(TBD)

## REST API - ROUGUE variant

This variant is to be used in DIY devices, prototypes and sensor with poor accuracy.

### Reading request

`POST \sensors\[SUID]\readings`

**Payload:**

See JSON Payload section.

**Response:**

Status 200, empty response

## REST API - SECURE variant

This protocol is to be used by certified devices and readings should be considered accurate therefore sensor identity must be validated.

SSL/TLS Secure transport is required.

### Registration 

Registration is automatic and will be performed during first request (or next if failed).

### Reading request

`POST \sensors\[SUID]\readings`

**Headers:**

`OpenSmogHash [HASH]`

If no such header is sent the server should register the device and sent the SECRET. 

**WARNING:** If device is reflashed and forgot the Secret it should provide different SUID.

**Payload:**

See JSON Payload section.

**Response:**

Status 200, empty response

## OpenSmogHash (HMAC)

Entire payload hashed (SHA256) with concatenated Secret at the end.

Pseudocode: 

```
SHA256(CONCAT(PAYLOAD,SECRET))
```
