# Hacky Air Pollution & Vendor TLV Decoder

This documentation describes a quick-and-dirty (“hacky”) method for:

1. **Interactive decoding** of binary payloads (hex) from an air pollution meter.  
2. **Programmatic retrieval** of vendor TLV data from an unofficial web API using web scraping.

> In this workflow, Part 1 decodes raw hex messages you obtain in Part 2.

---

## Table of Contents

- [Requirements](#requirements)  
- [Installation](#installation)  
- [Part 1: Air Pollution Data Decoder](#part-1-air-pollution-data-decoder)  
  - [Usage](#usage)  
  - [Supported Subtypes](#supported-subtypes)  
- [Part 2: Unofficial API Extraction (Vendor TLV)](#part-2-unofficial-api-extraction-vendor-tlv)  
  - [Usage](#usage-1)  
  - [Login Flow](#login-flow)  
  - [Parsing the Response](#parsing-the-response)  
- [Workflow Summary](#workflow-summary)  
- [Troubleshooting](#troubleshooting)  
- [Security Considerations](#security-considerations)  
- [License](#license)  

---

## Requirements

- **Python 3.6+**  
- **pip** for installing dependencies  
- On the web‑scraping side:
  - `requests`
  - `beautifulsoup4`
- (Optional) `argparse`, `getpass`, and `re` (standard library)

---

## Installation

```bash
# Create a venv (optional but recommended)
python3 -m venv venv
source venv/bin/activate

# Install required packages
pip install requests beautifulsoup4
```

---

## Part 1: Air Pollution Data Decoder

An **interactive CLI** that asks you for a subtype (1–5) and a space‑separated hex string.  
It then dispatches to the appropriate `decode_*` function to print human‑readable values.

### Usage

```bash
python decode_air_pollution_data.py
```

Once running, you’ll see prompts like:

```
Air Pollution Meter Data Decoder
--------------------------------

Enter subtype number (1-5, or 0 to exit): 2
Enter hex value for subtype 2 (space-separated bytes, e.g. '02 01 00 01 01 64 01 00'): 0a 00 14 00 2c 00 1e 00 3c 00 50 00
```

After entering the hex, the script prints:

```
-- Primary Pollutant Readings (Subtype 2) --
PM2.5: 1.0 μg/m³
PM10: 2.0 μg/m³
CO: 44 ppb
NO2: 30 ppb
O3 (Ozone): 60 ppb
SO2: 80 ppb
```

### Supported Subtypes

1. **Device Status** (8 bytes)  
   - Byte 0: Device Type  
   - Byte 1: Online/Offline  
   - Byte 2: Error Code  
   - Byte 3: Operation Mode  
   - Byte 4: Power Source  
   - Byte 5: Battery Level (%)  
   - Byte 6: Sensor Status  
   - Byte 7: Maintenance Required  

2. **Primary Pollutant Readings** (12 bytes)  
   - Bytes 0–1: PM2.5 (value/10)  
   - Bytes 2–3: PM10 (value/10)  
   - Bytes 4–5: CO (ppb)  
   - Bytes 6–7: NO2 (ppb)  
   - Bytes 8–9: O3 (ppb)  
   - Bytes 10–11: SO2 (ppb)  

3. **Secondary Readings** (9 bytes)  
   - Bytes 0–1: Temperature (signed, /10 °C)  
   - Byte 2: Humidity (%)  
   - Bytes 3–4: Barometric Pressure (/10 hPa)  
   - Bytes 5–6: Wind Speed (/10 m/s)  
   - Bytes 7–8: Wind Direction (°)  

4. **Air Quality Index (AQI)** (4 bytes)  
   - Bytes 0–1: AQI Value  
   - Byte 2: Dominant Pollutant (1=PM2.5, 2=PM10, …)  
   - Byte 3: AQI Category (0=Good, 1=Moderate, …)  

5. **String Data** (variable)  
   - Raw UTF‑8 text up to a null terminator or end of message  

---

## Part 2: Unofficial API Extraction (Vendor TLV)

A **"hacky"** Python script that:

1. **Logs in** to a web interface (e.g., Cisco FND) by scraping hidden form fields.  
2. **Extracts** the `netElementId` and calls a REST endpoint to fetch `get_vendorTLV.json`.

### Usage

```bash
python extract_vendor_tlv.py -u YOUR_USERNAME
# It will prompt for your password securely
```

Sample output:

```
200
{'javax.faces.ViewState': '…', 'token': '…', 'login': 'login'}
Attempting login with username: admin
Login successful
Cookies:  { … }
{'subtype': 2, 'valueHex': '0a000a00…'}
```

### Login Flow

1. **GET** the login page at `/login.seam`.  
2. **Parse** all `<input type="hidden">` fields via BeautifulSoup.  
3. **Add** your `login:username`, `login:password`, and `login:login='Log In'`.  
4. **POST** back to `/login.seam` with that payload.  
5. On success (`login_response.ok`), the session cookie is stored.

### Parsing the Response

- After login, fetch the element list JSON:
  ```http
  GET /resource/rest/elements/elements.json?…
  ```
- Extract `netElementId` via regex:
  ```python
  match = re.search(r'netElementId=(\d+)', …)
  ```
- Fetch vendor TLV:
  ```http
  GET /resource/rest/elements/{netElementId}/get_vendorTLV.json?…
  ```
- The JSON `records` array contains items like:
  ```json
  { "subtype": 2, "valueHex": "0a 00 14 00 …" }
  ```

---

## Workflow Summary

1. **Run** the Vendor TLV extractor to get JSON records.  
2. **Note** the `subtype` and its `valueHex`.  
3. **Run** the interactive decoder (`decode_air_pollution_data.py`), enter that subtype and hex.  
4. **Inspect** the printed, human‑readable values.

---

## Troubleshooting

- **Invalid hex string**: ensure you enter valid space‑separated byte pairs.  
- **Login failures**: confirm URL, credentials, and that the page’s hidden inputs haven’t changed.  
- **SSL warnings**: if using self‑signed certs, you may need `verify=False` or install the CA locally.

---

## Security Considerations

- Credentials are read via `getpass` to avoid echoing.  
- This is _not_ a robust API client—it scrapes HTML and relies on page structure.  
- Use only on trusted internal networks; **do not** expose to the Internet.

---