# Command Line Workflow for Registration

This section explains the command line steps involved in registering a third-party device using IoT FND. The process leverages the `addGenericEndpoints.sh` script, which automates the creation and customization of metadata files.

## Step-by-Step Workflow

1. **Listing Available Templates**:  
   Navigate to `/opt/cgms/server/cgms/conf` to verify the presence of the template files under the `endpoint-meta-templates` directory.

2. **Running the Registration Script**:  
   Execute the following command:
   ```bash
   /opt/cgms/bin/addGenericEndpoints.sh
   ```
   When prompted, enter the device type name (e.g., `endpointdevice1`). This creates a new directory:
   ```
   /opt/cgms/server/cgms/conf/endpoint-meta/endpointdevice1
   ```

3. **File Copy & Renaming**:  
   The script copies template files from the `endpoint-meta-templates` directory and renames them based on the provided device type. For example:
   `defaultDeviceTypeTemplate.json.template` → `defaultendpointdevice1Template.json`
   `defaultDeviceTypeTemplateNoIPRoute.json.template` → `defaultendpointdevice1TemplateNoIPRoute.json`
   ... and similar changes for the XML templates.

4. **Editing the JSON Templates for VendorTlv**:  
   By default, the JSON templates do not include the **VendorTlv** entry in the `tlvid` list. Since our agent devices require this (case-sensitive), you must update both:
   `defaultendpointdevice1Template.json`
   `defaultendpointdevice1TemplateNoIPRoute.json`
   
   Update them to include the following JSON block:
   
   ```json
   [{
       "name": "ReportSubscribe",
       "value": {
           "interval": 28800,
           "tlvid": [
               "InterfaceMetrics",
               "IPRoute",
               "IPRouteRPLMetrics",
               "GroupInfo",
               "FirmwareImageInfo",
               "Uptime",
               "LowpanPhyStats",
               "DiffServMetrics",
               "ReportSubscribe",
               "VendorTlv"
           ]
       }
   }]
   ```
   
   > **Important:**  
   > The `"VendorTlv"` entry must be exactly as shown (case-sensitive). If the casing is off, the registration process will not work correctly.

5. **Editing the Metadata File**:  
   Next, update the metadata file (e.g., `endpointdevice1Meta.json`) with the necessary device-specific details:
   
   ```json
   {
     "device_info": {
       "device_type": "endpointdevice1",
       "device_function": "meter",
       "device_description": "A sample endpoint device for monitoring.",
       "display_string": "METER-ENDPOINTDEVICE1",
       "pids": [
         "spid1",
         "spid2"
       ],
       "vendorId": "1234",
       "vendorName": "ExampleVendor",
       "configure_vendortlv": "true",
       "device_actions": [
         "reboot",
         "ping",
         "traceroute",
         "inventory"
       ],
       "hw_info": "HW12345"
     }
   }
   ```

6. **Restart IoT FND**:  
   Restart the IoT FND server so that it reads the updated metadata and processes the changes.

7. **Importing Device Data**:  
   Finally, import the CSV file containing the device EID and type mappings to complete the registration process.

![CLI Workflow Diagram](images/cli_workflow.png)

*Figure 2: Command line workflow depicting script execution, template updates, and metadata editing.*
