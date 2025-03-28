# Data Flow in the Registration Process

This document describes the data exchange between the Agent and IoT FND during the registration process.

## Data Exchange Overview

1. **Metadata Generation**:  
   The `addGenericEndpoints.sh` script creates metadata files by copying and renaming template files. These files contain critical configuration data, including device type, functions, and hardware identifiers.

2. **JSON Template Updates**:  
   The JSON templates (e.g., `defaultendpointdevice1Template.json` and `defaultendpointdevice1TemplateNoIPRoute.json`) are updated to include the `VendorTlv` in the `tlvid` list. This is essential for our agent devices.

3. **Metadata Editing**:  
   The metadata file (e.g., `endpointdevice1Meta.json`) is edited to provide necessary details such as `device_type`, `device_function`, and `device_description`. This file serves as the primary payload for the registration process.

4. **IoT FND Processing**:  
   Upon restarting, IoT FND reads the metadata from the `/endpoint-meta/` directory, updates its database, and configures the Field Devices page accordingly.

5. **CSV Import**:  
   The final step involves importing the CSV file (containing device EID and device type mappings) to complete the registration and display the new device in the Field Devices inventory.

![Data Flow Diagram](images/data_flow.png)

*Figure 3: Diagram showing the flow of data from template generation to device registration within IoT FND.*