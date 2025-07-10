# EasyStart Home Assistant Integration 
- https://www.microair.net/products/easystart-flex-home-ac-soft-starter

This project integrates the Micro-Air EasyStart soft start device with Home Assistant using ESPHome and Bluetooth Low Energy (BLE) communication. Monitor your AC unit's performance, track connection history, and view real-time electrical data directly in your Home Assistant dashboard.

## Features

- **Real-time monitoring** of AC unit electrical parameters
- **Automatic reconnection** when EasyStart device comes online
- **Connection history timeline** showing on/off patterns
- **Live Current, Line Frequency, Status** and fault monitoring
- **Total starts and faults tracking**
- **Clean dashboard integration** with all data in one card

## Prerequisites

- Home Assistant with ESPHome integration installed
- ESP32 development board (ESP32-Dev recommended)
- Micro-Air EasyStart device installed on AC unit
- Smartphone with Bluetooth capability
- Basic knowledge of YAML configuration

## Installation Steps

### 1. Install EasyStart on Your AC Unit

Follow the manufacturer's instructions to install the Micro-Air EasyStart device on your air conditioning unit. Ensure the device is properly wired and functional before proceeding.

### 2. Acquire EasyStart MAC Address

1. Download the **Micro-Air EasyStart** mobile app
2. Connect your smartphone to the EasyStart device via Bluetooth
3. Navigate to device settings and **record the MAC address** (format: `XX:XX:XX:XX:XX:XX`)
4. Keep this MAC address handy - you'll need it for configuration

### 3. Install ESPHome in Home Assistant

1. Navigate to **Settings** → **Add-ons** → **Add-on Store**
2. Search for and install **ESPHome**
3. Start the ESPHome add-on
4. Open the ESPHome web interface

### 4. Install ESPHome Dashboard

If not already installed:
1. In Home Assistant, go to **Settings** → **Integrations**
2. Click **+ Add Integration**
3. Search for and add **ESPHome**

### 5. Program Your ESP32 Device

1. Visit [ESPHome Projects](https://esphome.io/projects/)
2. Connect your ESP32 to your computer via USB
3. Click **"Connect"** and select your ESP32 device
4. Choose **"Install"** and then **"Skip"** to create a blank project
5. Configure the device with:
   - **Device name**: `esphome-web-52afa0` (or your preference)
   - **WiFi SSID**: Your home WiFi network name
   - **WiFi Password**: Your home WiFi password
6. Click **"Install"** to flash the basic ESPHome firmware

### 6. Add Device to ESPHome Dashboard

1. Open the **ESPHome dashboard** in Home Assistant
2. The ESP32 should appear as **"Discovered"** - click **"Adopt"**
3. If not discovered, click **"+ New Device"** → **"Continue"** → **"Skip"**
4. Enter device name and click **"Next"**
5. Select **"ESP32"** as device type
6. **Verify the device shows "ONLINE"** in ESPHome dashboard ✅

### 7. Extract Configuration Information

1. In ESPHome dashboard, click **"Edit"** on your device
2. **Copy and save the following information** to notepad:
   ```yaml
   # Lines 1-11: Device configuration
   esphome:
     name: esphome-web-52afa0
     friendly_name: micro-air
   
   # Copy these specific values:
   api:
     encryption:
       key: "YOUR_API_KEY_HERE"
   
   ota:
     password: "YOUR_OTA_PASSWORD_HERE"
   
   wifi:
     ap:
       ssid: "Micro-Air Fallback Hotspot"
       password: "YOUR_FALLBACK_PASSWORD_HERE"
   ```

### 8. Apply New Configuration

1. **Replace the entire configuration** in ESPHome editor with the configuration from this repository
2. **Update the following values** with your saved information:
   - Line 45: `mac_address: "C7:5B:B2:8E:D0:EC"` → Replace with **your EasyStart MAC address**
   - API encryption key → Use your saved key
   - OTA password → Use your saved password  
   - Fallback hotspot password → Use your saved password
3. **Save** the configuration
4. Click **"Install"** → **"Wirelessly"**

### 9. Verify Communication

1. Check ESPHome logs for successful BLE connection messages
2. In Home Assistant, navigate to **Settings** → **Devices & Services** → **ESPHome**
3. Your device should show multiple entities (sensors, switches, binary sensors)
4. Toggle the **"Read Status"** switch - you should see data updating

### 10. Enable Device (If Needed)

**Important**: Sometimes the device gets disabled during initial setup.

1. Go to **Settings** → **Devices & Services** → **ESPHome**
2. Find your micro-air device
3. Click the **three dots (⋮)** next to the device name
4. If you see **"Enable"**, click it
5. If already enabled, try **"Disable"** then **"Enable"** to refresh

### 11. Dashboard Setup

1. Navigate to your desired Home Assistant dashboard
2. Click **"Edit Dashboard"** (three dots menu)
3. Click **"+ Add Card"**
4. Select **"Manual"** card type
5. **Copy and paste** the vertical stack configuration from this repository:

```yaml
type: vertical-stack
cards:
  - type: entities
    title: AC Unit - SoftStart
    entities:
      - entity: switch.esphome_web_52afa0_read_status
        name: "Read Status"
      - entity: binary_sensor.esphome_web_52afa0_easystart_connected
        name: "EasyStart Connected"
      - entity: sensor.esphome_web_52afa0_easystart_diag
        name: "EasyStart Diag"
      - entity: sensor.esphome_web_52afa0_last_start_peak
        name: "Last Start Peak"
      - entity: sensor.esphome_web_52afa0_line_frequency
        name: "Line Frequency"
      - entity: sensor.esphome_web_52afa0_live_current
        name: "Live Current"
      - entity: sensor.esphome_web_52afa0_scpt_delay
        name: "SCPT Delay"
      - entity: sensor.esphome_web_52afa0_status
        name: "Status"
      - entity: sensor.esphome_web_52afa0_total_faults
        name: "Total Faults"
      - entity: sensor.esphome_web_52afa0_total_starts
        name: "Total Starts"
  
  - type: history-graph
    entities:
      - entity: binary_sensor.esphome_web_52afa0_easystart_connected
    hours_to_show: 24
    refresh_interval: 0
    title: "Connection History (24 Hours)"
```

6. Click **"Save"**

### 12. Final Verification

✅ **Toggle the "Read Status" switch** - should turn ON and start collecting data  
✅ **Verify sensor values** are updating (Current, Frequency, Status, etc.)  
✅ **Check connection timeline** shows activity in the history graph  
✅ **Confirm automatic reconnection** works when EasyStart cycles  

## Troubleshooting

### Device Shows as Disabled
- Go to ESPHome integration → Find device → Click three dots → Enable

### No Data/Unknown Values
- Verify MAC address is correct in configuration
- Check ESPHome logs for BLE connection errors
- Ensure EasyStart device is powered and nearby

### Connection Issues
- Restart ESPHome device
- Check WiFi signal strength
- Verify Home Assistant ESPHome integration is working

### Timeline Not Showing
- Confirm entity names match in dashboard YAML
- Check if binary sensor is updating in Developer Tools → States

## Configuration Files

- **`esphome-config.yaml`** - Complete ESPHome device configuration
- **`dashboard-card.yaml`** - Home Assistant dashboard card configuration

## Hardware Requirements

- **ESP32 Development Board** (ESP32-WROOM-32 recommended)
- **USB Cable** for initial programming
- **Stable 3.3V/5V power supply** for permanent installation

## Contributing

Feel free to submit issues, feature requests, or improvements via GitHub issues and pull requests.

## License

This project is provided as-is for educational and personal use. Please ensure compliance with your local electrical codes and manufacturer warranties when installing AC unit modifications.

## Support

For questions specific to this integration, please open a GitHub issue. For EasyStart device support, contact Micro-Air directly.
