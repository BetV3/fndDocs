# Complete Air Pollution Meter Device Properties Documentation

This document details how properties for the Air Pollution Meter device are populated within the NMS. It includes explanations of basic identification, mesh device health, mesh link settings, vendor-specific air pollution data from vendor TLVs, and the data generation/retrieval flow.

The source code for this functionality is maintained in the [CSMP Agent Library Repository](https://github.com/BetV3/csmp-agent-lib).

---

## 1. Basic Device Identification Properties

| **Property**                | **Source in Code**                | **Details**                                                                                                                                                                  |
|-----------------------------|-----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name/EID**                | `hardware_desc_get()`             | _Lines 76-79:_ Derived from the device's EUI-64 identifier (`g_eui64`) and formatted as a hexadecimal string.                                                             |
| **Domain**                  | Default setting                   | Set to `"root"` as the default domain.                                                                                                                                       |
| **Device Category**         | `hardware_desc_get()`             | Set as `"ENDPOINT"` based on the hardware description.                                                                                                                       |
| **Device Type**             | `hardware_desc_get()`             | _Line 81:_ Set as `"AIRPOLLUTION"` (modified from `"OPENCSMP"`).                                                                                                              |
| **Mesh Function**           | `hardware_desc_get()`             | _Line 82:_ Assigned via `g_hardwareDesc.entphysicalfunction = FUNCTION_METER`.                                                                                                 |
| **Manufacturer**            | `hardware_desc_get()`             | _Line 80:_ Populated via `sprintf(g_hardwareDesc.entphysicalmfgname, "Cisco IoTG")`.                                                                                          |
| **Status**                  | `sample_get_vendorTlv()`          | _Line 2130:_ Display status from Subtype 1 data using `g_vendorTlv[0].value.data[1]` (1 = online, 0 = offline).                                                             |
| **IP Address**              | Internal networking code          | Populated from the device's network interface configuration.                                                                                                                 |
| **Meter ID**                | Not set in code                   | Displays as `"unset"` in the inventory.                                                                                                                                      |
| **PHY Type**                | Not set in code                   | Displays as `"unset"` in the inventory.                                                                                                                                      |
| **First Heard**             | NMS tracking                      | Recorded by the NMS when the device first connects.                                                                                                                          |
| **Last Heard**              | NMS tracking                      | Updated by the NMS each time it communicates with the device.                                                                                                                |
| **Last Property Heard**     | NMS tracking                      | Updated when the NMS last received property data from the device.                                                                                                             |
| **Last Metric Heard**       | NMS tracking                      | Updated when the NMS last received metric data from the device.                                                                                                               |
| **Model Number**            | `hardware_desc_get()`             | _Line 81:_ Set via `sprintf(g_hardwareDesc.entphysicalmodelname, "OPENCSMP")`.                                                                                                 |
| **Serial Number**           | `hardware_desc_get()`             | _Lines 76-79:_ Generated from `g_eui64` and formatted as a hexadecimal string.                                                                                                |
| **Vendor Hardware ID**      | Not set in code                   | Displays as `"N/A"` in the inventory.                                                                                                                                         |
| **Firmware Version**        | `hardware_desc_get()`             | _Line 75:_ Populated via `memcpy(g_hardwareDesc.entphysicalfirmwarerev, g_slothdr[RUN_IMAGE].version, ...)`.                                                                  |
| **Config Group**            | Default setting                   | Set to `"default-airpollution"` by the NMS based on device type.                                                                                                               |
| **Firmware Group**          | Default setting                   | Set to `"default-airpollution"` by the NMS based on device type.                                                                                                               |
| **Location**                | Not set in code                   | Displays as `"unset"` in the inventory.                                                                                                                                         |
| **Labels**                  | Not set in code                   | Displays as `"none"` in the inventory.                                                                                                                                           |
| **Meter Certificate**       | Not set in code                   | Displays as `"unknown"` in the inventory.                                                                                                                                        |
| **Groups**                  | Not set in code                   | Displays as `"none"` in the inventory.                                                                                                                                           |

---

## 2. Mesh Device Health

| **Property**                | **Source in Code**                | **Details**                                                                                                                            |
|-----------------------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| **Uptime**                  | `csmp_service_stats()`            | _Lines 2113-2124:_ Retrieved from the stats pointer and shown in seconds.                                                              |
| **Last Registration Reason**| Not set in code                   | Displays as `"unknown"` in the inventory.                                                                                               |

---

## 3. Mesh Link Settings

| **Property**                | **Source in Code**                | **Details**                                                                                                                            |
|-----------------------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| **SSID**                    | Configuration                     | Set to `"CISCO"` in the network configuration settings.                                                                               |
| **PANID**                   | Configuration                     | Set to `"1234"` in the network configuration settings.                                                                                 |
| **Transmit Power**          | Configuration                     | Set to `"28 dBm"` in the network configuration settings.                                                                               |
| **Security Mode**           | Configuration                     | Set to `"0"` in the network configuration settings.                                                                                    |

---

## 4. Mesh Link Metrics

| **Property**                      | **Source in Code**                | **Details**                                                                                                                            |
|-----------------------------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| **Mesh Link Transmit Speed**      | Network metrics                   | Dynamically collected from network interface statistics.                                                                              |
| **Mesh Link Receive Speed**       | Network metrics                   | Dynamically collected from network interface statistics.                                                                              |
| **Mesh Link Transmit Packet Drops**| Network metrics                  | Dynamically collected from network interface statistics.                                                                              |
| **Mesh Route RPL Hops**           | Network metrics                   | Dynamically collected from network routing information.                                                                               |
| **Mesh Route RPL Link Cost**      | Network metrics                   | Dynamically collected from network routing information.                                                                               |
| **Mesh Route RPL Path Cost**      | Network metrics                   | Dynamically collected from network routing information.                                                                               |
| **Mesh Route RSSI**               | Network metrics                   | Dynamically collected from network interface statistics.                                                                              |
| **Mesh Route Reverse RSSI**       | Network metrics                   | Dynamically collected from network interface statistics.                                                                              |
| **Mesh Parents**                  | Not set in code                   | Displays as `"unknown"` in the inventory.                                                                                               |
| **Mesh Children**                 | Not set in code                   | Displays as `"unknown"` in the inventory.                                                                                               |
| **Mesh Descendants**              | Not set in code                   | Displays as `"unknown"` in the inventory.                                                                                               |
| **Mesh Link Queue Jump Count**    | Not set in code                   | Displays as `"unknown"` in the inventory.                                                                                               |
| **Mesh Link Queue Jump Rate**     | Not set in code                   | Displays as `"unknown"` in the inventory.                                                                                               |
| **Mesh Link Queue Eviction Count**| Not set in code                   | Displays as `"unknown"` in the inventory.                                                                                               |
| **Mesh Link Queue Eviction Rate** | Not set in code                   | Displays as `"unknown"` in the inventory.                                                                                               |

---

## 5. Air Pollution Data (Vendor TLVs)

### 5.1. Subtype 1: Device Status

**Source in Code:** _Line 2130_  
Displays the device status:
```c
printf("Device Status: %s\n", g_vendorTlv[0].value.data[1] == 0x01 ? "Online" : "Offline");
```

---

### 5.2. Subtype 2: Primary Pollutant Readings

**Source in Code:** _Lines 2133-2148_  
Extracts primary pollutant readings:
```c
if (g_vendorTlv[1].has_subtype && g_vendorTlv[1].subtype == 2) {
  uint8_t *readings = g_vendorTlv[1].value.data;
  uint16_t pm25 = readings[0] | (readings[1] << 8);
  uint16_t pm10 = readings[2] | (readings[3] << 8);
  uint16_t co = readings[4] | (readings[5] << 8);
  uint16_t no2 = readings[6] | (readings[7] << 8);
  uint16_t o3 = readings[8] | (readings[9] << 8);
  uint16_t so2 = readings[10] | (readings[11] << 8);

  printf("PM2.5: %.1f μg/m³\n", pm25 / 10.0);
  printf("PM10: %.1f μg/m³\n", pm10 / 10.0);
  printf("CO: %d ppb\n", co);
  printf("NO2: %d ppb\n", no2);
  printf("O3: %d ppb\n", o3);
  printf("SO2: %d ppb\n", so2);
}
```

---

### 5.3. Subtype 3: Secondary Environmental Readings

**Source in Code:** _Lines 2151-2164_  
Processes secondary environmental data:
```c
if (g_vendorTlv[2].has_subtype && g_vendorTlv[2].subtype == 3) {
  uint8_t *sec_readings = g_vendorTlv[2].value.data;
  int16_t temp = sec_readings[0] | (sec_readings[1] << 8);
  uint8_t humidity = sec_readings[2];
  uint16_t pressure = sec_readings[3] | (sec_readings[4] << 8);
  uint16_t wind_speed = sec_readings[5] | (sec_readings[6] << 8);
  uint16_t wind_dir = sec_readings[7] | (sec_readings[8] << 8);

  printf("Temperature: %.1f °C\n", temp / 10.0);
  printf("Humidity: %d%%\n", humidity);
  printf("Pressure: %.1f hPa\n", pressure / 10.0);
  printf("Wind Speed: %.1f m/s\n", wind_speed / 10.0);
  printf("Wind Direction: %d°\n", wind_dir);
}
```

---

### 5.4. Subtype 4: Air Quality Index (AQI)

**Source in Code:** _Lines 2167-2182_  
Extracts and displays AQI information:
```c
if (g_vendorTlv[3].has_subtype && g_vendorTlv[3].subtype == 4) {
  uint8_t *aqi_data = g_vendorTlv[3].value.data;
  uint16_t aqi = aqi_data[0] | (aqi_data[1] << 8);
  uint8_t category = aqi_data[3];

  printf("Air Quality Index (AQI): %d\n", aqi);

  const char *aqi_categories[] = {
    "Good", "Moderate", "Unhealthy for Sensitive Groups",
    "Unhealthy", "Very Unhealthy", "Hazardous"
  };

  if (category < 6) {
    printf("AQI Category: %s\n", aqi_categories[category]);
  }
}
```

---

### 5.5. Subtype 5: String Data (Location/Advisory)

**Source in Code:** _Lines 2184-2186_  
Displays any advisory or location data:
```c
if (g_vendorTlv[4].has_subtype && g_vendorTlv[4].subtype == 5) {
  printf("Advisory: %s\n", (char*)g_vendorTlv[4].value.data);
}
```

---

## 6. Data Generation and Retrieval Flow

1. **Initialization:**  
   - `sample_data_init()` (line 2089) initializes device data.

2. **Service Start:**  
   - `csmp_service_start()` (line 2092) starts the CSMP agent service.

3. **Polling Loop:**  
   - The main loop (lines 2101-2188) regularly:
     - Checks for reboot requests,
     - Sleeps for the registration interval,
     - Retrieves service status via `csmp_service_status()`,
     - Retrieves service statistics via `csmp_service_stats()`,
     - Displays air pollution data from vendor TLVs.

4. **Data Generation:**  
   - `init_airpollution_meter_simulation()` (line 1532) is responsible for generating realistic sensor data.

5. **Data Retrieval:**  
   - `sample_get_vendorTlv()` (lines 1531-1546) fetches vendor TLV data when the NMS requests it.

6. **Data Display:**  
   - Collected data is output in human-readable format (lines 2127-2187) for monitoring and debugging.