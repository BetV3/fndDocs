# Importing Devices via CSV

This guide explains how to download, edit, and upload the `endpoint.csv` file to add new devices to the Field Network Director (FND). Follow the steps below to ensure that the device is correctly added and appears in the devices list.

## Steps

1. **Download the Sample CSV File:**
   - Log into the FND GUI.
   - Navigate to the **Devices** section.
   - Click on the **Download Sample CSV** button (or similar option) to download the `endpoint.csv` file.

2. **Edit the CSV File:**
   - Open the downloaded `endpoint.csv` file in a spreadsheet editor (e.g., Excel) or a text editor.
   - Update the **deviceType** column:
     - Change the value to either `streetlight` or `airpollution`, depending on the type of device you wish to add.
   - Update the **eid** column:
     - Provide any valid 16-character hexadecimal string (for example: `0123456789ABCDEF`).
   - Save the changes to the `endpoint.csv` file.

3. **Upload the Edited CSV File:**
   - Return to the FND GUI and navigate to the **Add Devices** section.
   - Click on **Upload CSV** (or the equivalent option) and select your edited `endpoint.csv` file.
   - Click the **Add** button to submit the file.

4. **Verify Device Registration:**
   - After the upload, the new device should appear in the devices list.
   - Confirm that the device is listed with the correct type (`streetlight` or `airpollution`) and that its identifier (eid) matches the value you entered.

## Troubleshooting

- **CSV Format Issues:**  
  Ensure that the CSV file maintains the proper format after editing and that no extra characters or formatting errors were introduced.

- **Validation Errors:**  
  Verify that the **eid** is exactly a 16-character hexadecimal string and that **deviceType** is one of the allowed values.

- **Upload Failures:**  
  If the file fails to upload, check your network connection and try again. Refer to the FND troubleshooting guidelines if the problem persists.

Following these steps should allow you to successfully add a new device via the CSV file. For further details, refer to the complete FND documentation or contact support.
