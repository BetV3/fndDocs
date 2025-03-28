# Registration Process Overview

This section documents the steps required to register a third-party device using IoT FND. The process is based on the guidelines provided in the "Third Party Generic Endpoint Onboarding in FND" document :contentReference[oaicite:0]{index=0}&#8203;:contentReference[oaicite:1]{index=1}.

## Process Summary

1. **Template Review**:  
   - Navigate to `/opt/cgms/server/cgms/conf` and review the available metadata templates (e.g., `defaultDeviceTypeTemplate.json.template`, `deviceTypeEventTypes.xml.template`, etc.).

2. **Script Execution**:  
   - Run the registration script `addGenericEndpoints.sh` located in `/opt/cgms/bin`.  
   - When prompted, provide the device type name (e.g., `endpointdevice1`).

3. **Template Copy & Rename**:  
   - The script creates a new directory under `/opt/cgms/server/cgms/conf/endpoint-meta/` and copies the templates while renaming them to match the device type.

4. **Editing the Templates**:  
   - **JSON Templates:**  
     After the script runs, you must update the JSON templates:
     - `defaultDeviceTypeTemplate.json`
     - `defaultDeviceTypeTemplateNoIPRoute.json`
     
     to include the **VendorTlv** in the ReportSubscribe configuration (see the CLI Workflow for details).

   - **Metadata File:**  
     Next, edit the metadata file (e.g., `endpointdevice1Meta.json`) to fill in the required fields such as `device_type`, `device_function`, `device_description`, etc.

5. **Restart IoT FND**:  
   - Restart the IoT FND server so it reads the updated metadata and creates the necessary tables.

6. **Device Import**:  
   - Import the CSV file containing device EID and type information to add the new devices, which will appear under the Endpoints category in the Field Devices page.

![Registration Flow Diagram](images/registration_flow.png)

*Figure 1: Overview of the registration process.*
