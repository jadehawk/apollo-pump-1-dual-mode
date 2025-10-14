# 💦 Apollo Pump-1 (Dual Mode) - Custom ESPHome Firmware

> **Enhanced firmware for Apollo Automation's Pump-1 device featuring dual operating modes,
> intelligent time synchronization with fallback, cycle counting, and advanced automation capabilities.**

<div align="center">

![Version](https://img.shields.io/badge/version-jadehawk--2025.10.13.1-blue)
![Hardware](https://img.shields.io/badge/hardware-ESP32--C6-green)
![Framework](https://img.shields.io/badge/framework-ESPHome-orange)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

### 🌟 Support This Project

[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-☕-yellow?style=for-the-badge&logo=buy-me-a-coffee)](https://buymeacoffee.com/jadehawk)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?style=for-the-badge&logo=youtube)](https://www.youtube.com/@jadehawk)
[![Blog](https://img.shields.io/badge/Blog-techy--notes.com-blue?style=for-the-badge&logo=blogger)](https://techy-notes.com)

**If this project helps you, consider supporting my work!** ☕

</div>

---

## 📋 Table of Contents

- [✨ Features](#-features)
- [🚀 What's New](#-whats-new)
- [🔌 Hardware Pinout](#-hardware-pinout)
- [📦 Requirements](#-requirements)
- [⚡ Quick Start](#-quick-start)
- [📥 Installation](#-installation)
- [🎨 Home Assistant Dashboard Cards](#-home-assistant-dashboard-cards)
- [⚙️ Configuration Guide](#️-configuration-guide)
- [🔄 Operating Modes](#-operating-modes)
- [🔬 Sensor Configuration & Use Cases](#-sensor-configuration--use-cases)
- [🛠️ Example Projects](#️-example-projects)
- [🔧 Troubleshooting](#-troubleshooting)
- [👤 About the Author](#-about-the-author)
- [🤝 Contributing](#-contributing)
- [📝 Version History](#-version-history)

---

## ✨ Features

### 🎯 Core Functionality

- **🔄 Dual Operating Modes**
  - **Continuous Mode**: Time-based pumping for water evacuation
  - **Cycle Mode (Dispensing)**: Counted dispensing with low-fluid alerts
- **🏷️ Automatic Device Naming**: MAC address suffix ensures unique device IDs
- **🔐 Flexible Security**: Auto-generated or manual OTA password and API encryption
- **💾 Persistent Cycle Counting**: Tracks pump cycles across reboots
- **🚨 Smart Alerts**: Visual (RGB LED) and audio (buzzer) notifications

### 🚀 Advanced Features

- **⏰ Intelligent Time Synchronization** _(NEW!)_
  - Expanded NTP server list (6 servers: hostname + IP pairs)
  - Explicit DNS configuration for reliability
  - **Fallback timer system** - Ensures operation even without SNTP sync
  - Automatic transition between SNTP and uptime-based timing
- **🔍 Dual Sensor Support**: INPUT/OUTPUT fluid level monitoring
- **🛡️ Safety Timeouts**: Configurable maximum runtime protection
- **📡 WiFi Setup**: Captive portal for easy initial configuration
- **🧪 Boot Test Sequence**: Automatic hardware verification on startup
- **🌐 Web Interface**: Built-in web server for monitoring
- **🏠 Home Assistant Integration**: Full API support with auto-discovery

---

## 🚀 What's New

### Version jadehawk-2025.10.13.1 (Current)

#### 🎉 Major Enhancements

- **🎯 Three-Mode Sensor System**

  - **Disabled Mode**: Sensor completely ignored
  - **Safety Mode**: Continuous monitoring with automatic pump control
    - INPUT Safety: Pump runs while WET, stops when DRY
    - OUTPUT Safety: Pump stops when FULL, resumes when DRY (priority override)
  - **Trigger Mode**: Event-driven operation
    - Pump activates for configured duration when sensor detects WET
    - Perfect for on-demand pumping scenarios

- **📊 Enhanced Status Monitoring**

  - **Diagnostic Status Sensor**: Real-time pump state reporting
    - Shows current operating state
    - Displays blocking conditions
    - Mode-aware status messages
  - **Trigger Mode Indicator**: Visual confirmation when trigger mode is active
  - **Improved UI**: Clean sensor mode selectors in Home Assistant cards

- **🛡️ Advanced Safety Logic**
  - Mode-aware pump control for all scenarios
  - Separate logic paths for Continuous vs Cycle modes
  - 2-second monitoring interval for trigger detection
  - Enhanced safety checks with priority override system

### Version jadehawk-2025.10.11.1

#### ✨ Key Features

- **⏰ Bulletproof Time Synchronization**

  - 3 NTP servers (1 hostname + 2 IP) for maximum redundancy
  - Explicit DNS configuration (dns1, dns2)
  - **Fallback timer system** using device uptime when SNTP unavailable
  - Automatic rollover handling (49.7-day uptime support)
  - **Pumps never miss scheduled intervals** regardless of network issues

- **🏷️ Dynamic Device Naming**

  - Automatic MAC suffix appending
  - User-customizable friendly names
  - Unique identification for multiple pumps

- **🔐 Flexible Security**

  - Optional auto-generated credentials (Except for API Encryption Key)
  - Manual credential support for existing devices
  - Simplified multi-pump deployment

- **📚 Comprehensive Documentation**
  - Detailed sensor use cases
  - Step-by-step configuration guides
  - Troubleshooting section
  - Template-based dashboard cards

---

## 🔌 Hardware Pinout

### ESP32-C6 Pin Assignments

| GPIO Pin   | Function            | Description                     | Configuration            |
| ---------- | ------------------- | ------------------------------- | ------------------------ |
| **GPIO3**  | RGB LED             | WS2812 status indicator         | Output, 1 LED, GRB order |
| **GPIO4**  | Fluid Input Sensor  | Monitors source container level | Input, Pull-up, Inverted |
| **GPIO5**  | Buzzer              | RTTTL audio alerts              | LEDC Output              |
| **GPIO7**  | Pump Control        | Main pump relay/control         | Output                   |
| **GPIO9**  | Reset Button        | Physical control button         | Input, Pull-up, Inverted |
| **GPIO14** | Fluid Output Sensor | Monitors destination level      | Input, Pull-up, Inverted |

### 📍 Pin Details

<details>
<summary><b>GPIO3 - RGB LED (WS2812)</b></summary>

- **Type:** Addressable RGB LED
- **Chipset:** WS2812
- **Purpose:** Visual status indication
- **States:**
  - 🔵 Blue: AP mode (WiFi setup needed)
  - 🟢 Green: WiFi connected
  - 🟣 Purple: Home Assistant connected
  - 🟡 Yellow (pulsing): Warning - near cycle limit
  - 🔴 Red (pulsing): Alert - refill needed

</details>

<details>
<summary><b>GPIO4 - Fluid Input Sensor</b></summary>

- **Type:** Digital input (moisture sensor)
- **Pull-up:** Enabled
- **Logic:** Inverted (LOW = fluid detected)
- **Purpose:** Monitors source container fluid level
- **Use Case:** Prevents pump operation when source is empty

</details>

<details>
<summary><b>GPIO5 - Buzzer</b></summary>

- **Type:** LEDC PWM output
- **Purpose:** Audio alerts and notifications
- **Supports:** RTTTL (Ring Tone Text Transfer Language)
- **Alerts:**
  - Boot test sequence
  - Low fluid warnings
  - Cycle limit reached
  - Safety timeout

</details>

<details>
<summary><b>GPIO7 - Pump Control</b></summary>

- **Type:** Digital output
- **Purpose:** Controls main pump relay
- **Safety:** Monitored by safety timeout system
- **Max Runtime:** Configurable (default 60 seconds)

</details>

<details>
<summary><b>GPIO9 - Reset Button</b></summary>

- **Type:** Digital input with multi-click detection
- **Pull-up:** Enabled
- **Logic:** Inverted (LOW = pressed)
- **Functions:**
  - **Single tap** (< 1s): Run pump manually
  - **3-second hold**: Run boot test sequence
  - **10-second hold**: Factory reset

</details>

<details>
<summary><b>GPIO14 - Fluid Output Sensor</b></summary>

- **Type:** Digital input (moisture sensor)
- **Pull-up:** Enabled
- **Logic:** Inverted (LOW = fluid detected)
- **Purpose:** Monitors destination container level
- **Use Case:** Auto-stop pump when destination is full

</details>

---

## 📦 Requirements

### Hardware

- ✅ **Apollo Automation Pump-1** device
- ✅ ESP32-C6-DevKitM-1 (8MB Flash) - _included in Pump-1_
- ✅ USB-C cable for initial flashing

### Software

- ✅ **ESPHome** (latest version recommended)
  - Install via: [ESPHome Installation Guide](https://esphome.io/guides/getting_started_command_line.html)
- ✅ **Home Assistant** (optional, for full integration)
- ✅ **HACS** (optional, for dashboard cards)

---

## ⚡ Quick Start

```bash
# 1. Download the firmware
git clone https://github.com/yourusername/apollo-pump-dual-mode.git

# 2. Customize friendly_name in apollo-pump-1-dual-mode.yaml

# 3. Compile and flash
esphome run apollo-pump-1-dual-mode.yaml

# 4. Configure WiFi via captive portal

# 5. Add to Home Assistant (auto-discovered)
```

---

## 📥 Installation

### ESPHome Setup

#### Option A: ESPHome Dashboard (Recommended)

```bash
# Install ESPHome
pip3 install esphome

# Start dashboard
esphome dashboard config/
```

#### Option B: Command Line

```bash
# Install ESPHome
pip3 install esphome

# Validate configuration
esphome config apollo-pump-1-dual-mode.yaml

# Compile firmware
esphome compile apollo-pump-1-dual-mode.yaml
```

### Firmware Configuration

Open `apollo-pump-1-dual-mode.yaml` and customize the substitutions section:

```yaml
substitutions:
  # REQUIRED: Unique identifier for this pump (for organization)
  device_id: "pump1" # Change for each pump: pump1, pump2, pump3, etc.

  # Customize the display name
  friendly_name: "Kitchen Cleaner Pump"

  # Timezone (see full list in YAML file)
  timezone: "America/New_York"

  # OTA password (can use substitution)
  ota_password: ""
```

#### 🔐 Security Credentials

> **⚠️ Important:** When using custom firmware, you MUST manually provide credentials.
> ESPHome only auto-generates credentials when using the wizard, not with custom YAML files.

**For New Devices (First-Time Setup):**

Generate new credentials manually:

```bash
# Generate OTA password (any random 32+ character string)
openssl rand -hex 16

# Generate API encryption key (base64, 32 Byte >>> exactly 44 characters)
openssl rand -base64 32
```

**Alternative: Online Key Generators**

- [Random 32+ String Generator](https://key-generator.com/random-string-generator) - Generate a 32+ Character Password online
- [Random Base64 String Generator](https://key-generator.com/random-base64-string-generator) - Generate a 32-byte base64 key online

**1. OTA Password** - Add to substitutions section:

```yaml
substitutions:
  ota_password: "your-generated-password-here"
```

**2. API Encryption Key** - Must be added directly in the `api:` section (around line 223):

```yaml
api:
  encryption:
    key: "your-generated-base64-key-here="
```

> **⚠️ Note:** API encryption key CANNOT use substitutions in current ESPHome versions (2025.9.3).
> It must be placed directly in the `api:` section.

**For Existing Devices (Firmware Update):**

Copy credentials from your existing device to prevent re-pairing with Home Assistant:

1. OTA password in substitutions
2. API key directly in `api:` section (line ~223)

> **💡 Tip:** Each pump MUST have unique credentials. Never reuse credentials across multiple devices.

**WiFi Configuration:**

Create `secrets.yaml` in your ESPHome config directory:

```yaml
wifi_ssid: "YourWiFiNetwork"
wifi_password: "YourWiFiPassword"
```

### Flashing the Device

#### First-Time Flash (USB)

```bash
# Connect Pump-1 via USB-C
esphome run apollo-pump-1-dual-mode.yaml

# Select serial port when prompted
```

#### Subsequent Updates (OTA - Over The Air)

```bash
# Update wirelessly
esphome run apollo-pump-1-dual-mode.yaml

# Select network device when prompted
```

### Home Assistant Integration

#### Automatic Discovery

1. Device will appear in **Settings** > **Devices & Services** > **Discovered**
2. Click **Configure**
3. Device will be added automatically

#### Manual Addition

1. Go to **Settings** > **Devices & Services**
2. Click **+ Add Integration**
3. Search for **ESPHome**
4. Enter device IP address

---

## 🎨 Home Assistant Dashboard Cards

<img src="HomeAssistant Card/Updated Hass Card 001 - Main Section.png" alt="Home Assistant Card" width="800">

**📸 [View All Card Screenshots](HomeAssistant%20Card/Cards.md)** - See the dashboard card in various states and configurations

### Prerequisites

Install required custom cards via HACS:

1. **Install HACS**: [HACS Installation Guide](https://hacs.xyz/docs/use/download/download/)
2. **Install fold-entity-row**:
   - HACS > Frontend > Search "fold-entity-row" > Install
3. **Install auto-entities** (optional, for multi-pump card):
   - HACS > Frontend > Search "auto-entities" > Install

### Option 1: Template Card (Single Pump)

**File:** `HASS-Apollo-Pump-Card-Template.yaml`

**Setup Steps:**

1. Find your device's MAC suffix:

   - In ESPHome: Look for name like `apollo-pump-1-a1b2c3`
   - In Home Assistant: Settings > Devices > Apollo Pump
   - Suffix is last 6 characters (e.g., `a1b2c3`)

2. Open `HASS-Apollo-Pump-Card-Template.yaml` in text editor

3. Find & Replace:

   - **Find:** `{DEVICE_SUFFIX}`
   - **Replace:** `a1b2c3` (your actual suffix)

4. Copy entire file content

5. In Home Assistant:
   - Edit Dashboard > Add Card > Manual
   - Paste the modified YAML

### Option 2: Auto-Entities Card (Multiple Pumps)

**File:** `HASS-Apollo-Pump-Card-Auto-Entities.yml`

**Setup Steps:**

1. Ensure `auto-entities` custom card is installed
2. Copy entire file content (no editing needed!)
3. In Home Assistant:
   - Edit Dashboard > Add Card > Manual
   - Paste the YAML

**Features:**

- ✅ Automatically detects all Apollo Pump devices
- ✅ No manual editing required
- ✅ Updates automatically when adding new pumps
- ✅ Perfect for installations with multiple pumps

---

## ⚙️ Configuration Guide

### Device Identification

```yaml
substitutions:
  friendly_name: "Apollo Pump" # Customize for each pump
```

**Examples:**

- `"Kitchen Cleaner Pump"`
- `"Garage Water Pump"`
- `"Bathroom Soap Dispenser #1"`

**Device Naming:**

- Internal ID: `apollo-pump-1-[MAC]` (e.g., `apollo-pump-1-a1b2c3`)
- Display Name: Your custom `friendly_name`

### WiFi Configuration

Create `secrets.yaml` in your ESPHome config directory:

```yaml
wifi_ssid: "YourWiFiNetwork"
wifi_password: "YourWiFiPassword"
```

### Static IP Configuration (Optional)

Uncomment and configure in the YAML file:

```yaml
manual_ip:
  static_ip: 192.168.50.43
  gateway: 192.168.50.1
  subnet: 255.255.255.0
  dns1: 192.168.50.1 # Primary DNS (router)
  dns2: 8.8.8.8 # Secondary DNS (Google)
```

---

## 🔄 Operating Modes

### Continuous Mode (Water Evacuation)

**Use Case:** Pumping water from areas that continuously accumulate fluid

**Features:**

- ⏰ Time-based automatic pumping
- ⚙️ Configurable interval (hours)
- ⏱️ Configurable run duration (seconds)
- 🔢 No cycle counting

**Configuration:**

1. **Switch to Continuous Mode:** Turn OFF "Cycle: Enable Limiting" switch
2. Set "Cont: Interval (Hours)" to desired interval (e.g., 6 hours)
3. Set "Cont: Run Duration (Sec)" to desired runtime (e.g., 300 seconds)

**Example Use Cases:**

- AC drain line maintenance
- Basement sump pump backup
- Condensate removal
- Any continuous water evacuation scenario

### Cycle Mode (Dispensing)

**Use Case:** Dispensing finite fluids with low-level alerts

**Features:**

- 🔢 Cycle counting with persistent storage
- 📊 Estimated remaining fluid calculation
- 🚨 Visual and audio alerts when low
- 🔄 Auto-refill confirmation workflow
- 📏 Configurable cycle limits

**Configuration:**

1. **Switch to Cycle Mode:** Turn ON "Cycle: Enable Limiting" switch
2. Set "Cycle: Refill Size (oz)" to container size
3. Click "Cycle: Confirm Refill" to auto-calculate cycle limit
4. Set "Cont: Interval (Hours)" and "Cont: Run Duration (Sec)"

**Example Use Cases:**

- Soap/detergent dispensing
- Cleaning solution distribution
- Chemical dosing systems
- Any finite fluid dispensing application

**Alert System:**

- 🟡 **Warning** (within 10 cycles of limit): Yellow pulsing LED
- 🔴 **Alert** (limit reached): Red pulsing LED + hourly buzzer
- 🔕 **Auto-clear**: Alerts clear after refill confirmation

---

## 🔬 Sensor Configuration & Use Cases

### INPUT Sensor (GPIO4)

**Purpose:** Monitors source container fluid level

**Configuration:**

- Enable via "Sensor: Enable INPUT" switch
- Sensor reads LOW when fluid detected
- Sensor reads HIGH when dry

**Use Cases:**

#### Continuous Mode + INPUT Sensor

- **Behavior:** Enables "Run Until Empty" operation
- **Button:** "Manual: Run Until Empty" appears when INPUT sensor is enabled in Continuous Mode
- **Operation:** Pump runs continuously until INPUT sensor detects empty (no fluid)
- **Safety:** Still respects maximum runtime timeout
- **Use Case:** Pump water from source container until completely empty
- **Note:** Auto-run is disabled when INPUT sensor is enabled in Continuous Mode

#### Cycle Mode + INPUT Sensor

- **Behavior:** Blocks pump operation when source is empty
- **Alert:** Buzzer sounds when attempting to run with empty source
- **Auto-increment:** If fluid detected at cycle limit, automatically adds 10 cycles
- **Use Case:** Prevents dry-running, extends cycle limit when refilled

### OUTPUT Sensor (GPIO14)

**Purpose:** Monitors destination container level

**Configuration:**

- Enable via "Sensor: Enable OUTPUT" switch
- Sensor reads LOW when fluid detected
- Sensor reads HIGH when dry

**Use Cases:**

#### "Run Until Full" Operation

- **Trigger:** Press "Manual: Run Until Full" button
- **Behavior:** Pump runs continuously until OUTPUT sensor detects fluid
- **Safety:** Still respects maximum runtime timeout
- **Use Case:** Fill destination container to exact level

#### Auto-Stop During Manual Run

- **Behavior:** Any pump operation auto-stops when OUTPUT sensor detects full
- **Use Case:** Prevents overflow in destination container

### Sensor Wiring

Both sensors use the same wiring configuration:

- **Type:** Moisture/water level sensors
- **Connection:** Digital input with internal pull-up
- **Logic:** Active LOW (sensor grounds the pin when fluid detected)
- **Compatibility:** Works with most moisture sensors and float switches

---

## 🛠️ Example Projects

This custom firmware enables a wide variety of real-world applications. Below are example projects demonstrating how to use the Apollo Pump-1 in different scenarios.

### 🌊 AC Drain Line Purge System

<img src="Projects/AC Drain Line Purge/01 - AC Drain Line Purge 001.jpg" alt="AC Drain Line Purge System" width="800">

**Project Type:** Continuous Mode - Automated Water Evacuation

A solar-powered, automated system for maintaining AC drain lines and preventing clogs. This project demonstrates:

- ✅ Solar panel + powerbank setup for off-grid operation
- ✅ Waterproof enclosure for outdoor installation
- ✅ Scheduled automatic purging to prevent buildup
- ✅ Complete parts list with pricing
- 🔄 **Planned Upgrade:** Water level sensor integration for on-demand pumping (parts on order)

**📋 [View Complete Parts List & Setup Guide](Projects/AC%20Drain%20Line%20Purge/Parts.md)**

**Key Features:**

- Solar-powered operation (no electrical outlet needed)
- Weatherproof installation
- Automated maintenance schedule
- **Future:** Sensor-triggered operation for power efficiency
- Total cost: ~$170-$190 USD

**Upcoming Enhancement:**

The system will be upgraded with a water level sensor to trigger the pump only when water reaches the top of the drain line. This will:

- Reduce power consumption by eliminating interval-based pumping
- Extend powerbank runtime between solar charges
- Provide more efficient on-demand operation

---

### 🧪 AC Drain Pan Cleaning Dispenser

<img src="Projects/AC Drain Pan Cleaning Dispenser/01 - Cleaning Agent Dirspenser 001.jpg" alt="AC Drain Pan Cleaning Dispenser" width="800">

**Project Type:** Cycle Mode - Automated Cleaning Agent Dispensing

An automated system for dispensing cleaning solution into AC drain pans to prevent algae growth and maintain cleanliness. This project demonstrates:

- ✅ Cycle counting for tracking cleaning agent usage
- ✅ Water sensor integration for fluid level monitoring
- ✅ Scheduled automatic dispensing
- ✅ Low-cost, simple installation
- ✅ Complete parts list with pricing

**📋 [View Complete Parts List & Setup Guide](Projects/AC%20Drain%20Pan%20Cleaning%20Dispenser/Parts.md)**

**Key Features:**

- Automated cleaning agent dispensing
- Cycle tracking to monitor solution usage
- Water sensor detects low fluid levels
- Simple zip-tie and duct tape installation
- Total cost: ~$72 USD (without cleaning solution)
- Total cost: ~$98 USD (with recommended cleaning solution)

---

### 🚀 More Projects Coming Soon

Have you built something cool with the Apollo Pump-1? Share your project:

- Open a GitHub Issue with the "project showcase" label
- Include photos, parts list, and configuration details
- Help inspire the community!

---

## 🔧 Troubleshooting

### Time Synchronization Issues

**Problem:** SNTP shows "✗ Not Synced"

**Solutions:**

1. **Check DNS Configuration:**

   - Ensure `dns1` and `dns2` are configured in static IP settings
   - Verify DNS servers are reachable

2. **Verify NTP Server Access:**

   - Check firewall allows UDP port 123
   - Ensure router doesn't block NTP traffic

3. **Use Fallback Timer:**

   - Firmware automatically uses uptime-based timing if SNTP fails
   - Pumps will continue operating on schedule
   - Check logs for "Fallback timer initialized" message

4. **Force Time Sync:**
   - Use "System: Force Time Sync" button
   - Wait 30 seconds and check "Time: SNTP Status"

### WiFi Connection Issues

**Problem:** Device stuck in AP mode (blue LED)

**Solutions:**

1. Connect to "Apollo-Pump-1 Setup" WiFi network
2. Navigate to `192.168.4.1` in browser
3. Enter WiFi credentials
4. Device will reboot and connect

**Problem:** Device disconnects frequently

**Solutions:**

1. Check WiFi signal strength (RSSI sensor)
2. Move device closer to router or add WiFi extender
3. Configure static IP to prevent DHCP issues

### Pump Not Running Automatically

**Problem:** Pump doesn't run at scheduled intervals

**Checklist:**

- ✅ "System: Enable Pump & Logic" is ON
- ✅ "Status: Next Auto Run" shows valid time (not "Disabled")
- ✅ In Cycle Mode: Check cycle count hasn't reached limit
- ✅ In Cycle Mode with INPUT sensor: Verify source has fluid
- ✅ Check "Status: Last Auto Run" to confirm timestamp is updating

### Cycle Alerts Not Clearing

**Problem:** Red LED and buzzer persist after refill

**Solution:**

1. Set "Cycle: Refill Size (oz)" to actual refill amount
2. Press "Cycle: Confirm Refill" button
3. This resets counter and clears alerts

### Home Assistant Integration Issues

**Problem:** Device not discovered in Home Assistant

**Solutions:**

1. Verify device is on same network as Home Assistant
2. Check API encryption key matches between device and HA
3. Manually add via Settings > Devices > Add Integration > ESPHome
4. Enter device IP address

---

## 👤 About the Author

**JadeHawk** - IoT Enthusiast & Home Automation Hobbyist

I created this custom firmware and automation solutions to fix 2 issues I personally needed to get working.
This project represents a couple of weekends of painful trial & error, testing
and documentation formatting to provide the community with a robust, feature-rich custom pump firmware.

### 🌐 Connect With Me

- 📺 **YouTube:** [@jadehawk](https://www.youtube.com/@jadehawk) - Tutorials, reviews, and project showcases
- 📝 **Blog:** [techy-notes.com](https://techy-notes.com) - In-depth guides and technical articles
- ☕ **Support:** [Buy Me a Coffee](https://buymeacoffee.com/jadehawk) - Help fund future projects

### 💖 Support This Project

If this firmware has helped you, please consider:

- ⭐ **Star this repository** on GitHub
- ☕ **Buy me a coffee** to support development
- 📺 **Subscribe** to my YouTube channel (Love you to do this!)
- 📢 **Share** this project with others

Your support enables me to create more open-source projects and comprehensive documentation!

---

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

### Reporting Issues

- Use GitHub Issues to report bugs
- Include firmware version, hardware details, and logs
- Describe steps to reproduce the problem

### Suggesting Features

- Open a GitHub Issue with the "enhancement" label
- Describe the use case and expected behavior
- Explain how it benefits the community

### Pull Requests

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Code Style

- Follow existing code formatting
- Add comments for complex logic
- Update documentation for new features
- Test thoroughly before submitting

---

## 📝 Version History

### jadehawk-2025.10.13.1 (Current)

- 🎯 Added three-mode sensor system (Disabled/Safety/Trigger)
- 📊 Added diagnostic status sensor with real-time reporting
- ⚡ Added trigger mode indicator
- 🛡️ Implemented mode-aware safety logic
- 🔄 Added 2-second monitoring interval for trigger detection
- 🎨 Enhanced Home Assistant cards with sensor mode selectors
- 🧹 Removed legacy sensor switches for cleaner UI

### jadehawk-2025.10.11.1

- ✨ Added intelligent time synchronization with fallback timer
- ✨ Expanded NTP server list (6 servers: hostname + IP pairs)
- ✨ Added explicit DNS configuration support
- ✨ Implemented uptime-based fallback timing system
- 🏷️ Added dynamic device naming with MAC suffix
- 🔐 Added flexible security credential system
- 📚 Comprehensive documentation overhaul
- 🎨 Enhanced Home Assistant dashboard cards
- 🔧 Improved sensor configuration and use cases

### Previous Versions

- Initial release with dual-mode operation
- Basic time synchronization
- Cycle counting and alerts
- Sensor support

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 🙏 Acknowledgments

- **Apollo Automation** - For creating the excellent Pump-1 hardware
- **ESPHome Community** - For the amazing framework and support
- **Home Assistant Community** - For inspiration and feedback
- **Contributors** - Everyone who has helped improve this project

---

## ⚠️ Disclaimer

This firmware is provided "as is" without warranty of any kind. Use at your own risk.
Always test thoroughly in a safe environment before deploying in critical applications.

---

<div align="center">

### Made with ❤️ by JadeHawk

[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-☕-yellow?style=flat-square)](https://buymeacoffee.com/jadehawk)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?style=flat-square)](https://www.youtube.com/@jadehawk)
[![Blog](https://img.shields.io/badge/Blog-Visit-blue?style=flat-square)](https://techy-notes.com)

⭐ **Star this repo if it helped you!** ⭐

</div>
