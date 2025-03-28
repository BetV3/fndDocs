# Device Registration and Configuration

This section provides a high-level overview of how a device announces its entry onto the network (registration) and how the Network Management System (NMS) responds with configuration details.

## Why Devices Register

A device **MUST** register with the NMS for any of the following reasons:

- **Reboot**: The device is power cycled or receives a `RebootRequest TLV` from the NMS.
- **IP Change**: The device’s IP address changes (e.g., re-join of the network).
- **Mesh Parent Change**: In mesh networks, the device’s mesh parent changes.
- **NMS IP Address Change**: The device detects a new NMS IP address.
- **NMS Redirect**: The device receives an `NMSRedirectRequest TLV`, indicating it should register with a different NMS.

Once registration is triggered, the device initiates the messaging flow described below.

## Registration Flow Overview

The registration exchange follows a CoAP `CON POST` request/ACK pattern, as illustrated in **Figure 2** (from the specification). The key points:

1. **Device → NMS**:  
   - `CON POST /r` (registration endpoint).  
   - **Must** include mandatory TLVs such as `DeviceID`, `CurrentTime`, and device-specific TLVs (hardware, interface, IP, etc.).  
   - **May** include previously stored `SessionID`, `GroupInfo`, and `ReportSubscribe` TLVs (if the device had them from a prior registration).

2. **NMS → Device**:  
   - `ACK` with response code `2.03 (Valid)` if registration is successful.  
   - **May** include updated `SessionID`, `GroupAssign`, and `ReportSubscribe` TLVs.  
   - **Must** include signing TLVs.  
   - **May** include an `NMSRedirectRequest TLV` if the device should register elsewhere.

3. **First Metrics Report**:  
   - After receiving a valid registration ACK (and no redirect), the device sends an initial metrics report to the NMS (usually via `NON POST /c`).  
   - The device includes the `SessionID`, `CurrentTime`, and any metrics specified by the `ReportSubscribe TLV`.

4. **Registration Complete**:  
   - The NMS considers registration complete once it has received a valid ACK for the registration POST **and** the first metrics report from the device.

## Registration Request (4.3.1)

The device controls its registration attempts using two configurable parameters:

- **tIntervalMin** (defaults to 300 seconds)  
- **tIntervalMax** (defaults to 3600 seconds)

The device waits an initial random period, then repeatedly attempts registration (doubling the interval each time) until it receives a valid ACK. Once the device has registered successfully, it resets its internal timers for any future triggers (reboot, IP change, etc.).

## Registration POST Payload (4.3.2)

A **mandatory** set of TLVs must appear in the registration POST:

- **DeviceID** (primary identifier)
- **CurrentTime** (ensures device and NMS have synchronized time)
- **Device Information TLVs** (e.g., `HardwareDesc`, `InterfaceDesc`, `IPAddress`, `NMSStatus`, `WPANStatus`, `RPLSettings`)

If the device already has valid data for `SessionID`, `GroupInfo`, or `ReportSubscribe`, it **SHOULD** include these as well. This allows the device to maintain configuration continuity across reboots or network changes.

## NMS Registration Response (4.3.3)

Upon receiving a valid registration POST, the NMS:

1. **Validates** the `DeviceID` and any existing `SessionID`.
2. **Returns an ACK** with response code `2.03 (Valid)` if authorized and recognized. The response may contain:
   - **SessionID** (if the one in the request was missing or incorrect)
   - **GroupAssign** (to place the device in one or more groups)
   - **ReportSubscribe** (defines which metrics the device should periodically report)
   - **Signing TLVs** (cryptographic material for message signing)
   - **NMSRedirectRequest** (if the device should re-register with a different NMS)
3. **Redirects** the device if necessary, prompting the device to restart the registration process with a new NMS.

## Registration Complete (4.3.4)

Registration is considered complete when:

1. The device receives an ACK with response code `2.03 (Valid)` (no redirect).  
2. The device sends its **first metrics report** (non-confirmable POST) containing `SessionID` and the TLVs specified by the current `ReportSubscribe`.

Once the device is fully registered, it will continue sending metrics reports at intervals defined by the `ReportSubscribe TLV`.

---

**Key Takeaways**  
- Registration ensures the device is recognized by the NMS and receives up-to-date configuration (e.g., `SessionID`, `GroupInfo`, `ReportSubscribe`).
- The device uses a backoff and retry mechanism until it receives a valid ACK.
- The device may be redirected to another NMS via `NMSRedirectRequest`.
- Registration completes after the device’s first metrics report is received.

For further details on error handling, TLV formats, or CoAP response codes, refer to the specific sections (4.3.1 – 4.3.4) in the full specification.
