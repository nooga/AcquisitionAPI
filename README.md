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

**Length:** Exactly 128 bits.

Based on Universally Unique IDentifier ([rfc4122](https://tools.ietf.org/html/rfc4122)) URN Namespace for SUID.

**Example SUID:** `123e4567-e89b-12d3-a456-426655440000`

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

**timestamp** - Unix Epoch Time GMT+0000

### Reading types 

- `CO` – Carbon Monoxide (unit: **mg/m³**)
- `PB` – Lead (unit: **µg/m³**)
- `NO2` – Nitrogen Dioxide (unit: **µg/m³**)
- `O3` – Ozone (unit: **µg/m³**)
- `PM10` – PM10 (unit: **µg/m³**)
- `PM25` – PM2.5 (unit: **µg/m³**)
- `SO2` – Sulfur Dioxide (unit: **µg/m³**)

All Float values.

## MQTT

(TBD)

## REST API - ROUGUE variant

This variant is to be used in DIY devices, prototypes and sensor with poor accuracy. No authentication is used, using `SUID` for the first time will register the device.

### Reading request

`POST \sensors\[SUID]\readings`

**Payload:**

See JSON Payload section.

**Response:**

Status 200, empty response

## REST API - SECURE variant

This protocol is to be used by certified devices and readings should be considered accurate therefore sensor identity must be validated.

SSL/TLS Secure transport is required.

#### Authentication

Sensor will receive `SECRET` during registration.
For secure identification of the device `HMAC` will be used to send hash with every payload. The `SECRET` length is 256 bytes.

### Registration 

Registration is automatic and will be performed during first request (or next if failed).

### Reading request

`POST \sensors\[SUID]\readings`

**Headers:**

`OpenSmogHash [HASH]`

If no such header is sent the server should register the device and sent the `SECRET`. 

**WARNING:** If device is reflashed and forgot the Secret it should provide different `SUID`.

**Payload:**

See JSON Payload section.

**Response:**

Status 200, empty response

## OpenSmogHash (HMAC)

Entire payload hashed (SHA256) with concatenated `SECRET` at the end.

Pseudocode: 

```
SHA256(CONCAT(PAYLOAD,SECRET))
```
