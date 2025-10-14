# 🎨 Home Assistant Dashboard Cards

This folder contains screenshots of the Apollo Pump-1 Home Assistant dashboard card in various states and configurations.

## Card Overview

### Main Section

![Updated HASS Card - Main Section](Updated%20Hass%20Card%20001%20-%20Main%20Section.png)
_Main dashboard card showing all available controls and status information with the new three-mode sensor system_

### Sensor Configuration Section

![Updated HASS Card - Sensor Section](Updated%20Hass%20Card%20002%20-%20Sensor%20Section.png)
_Sensor configuration section showing the new INPUT/OUTPUT sensor mode selectors (Disabled/Safety/Trigger)_

### Active Sensors View

![Updated HASS Card - Active Sensors](Updated%20Hass%20Card%20when%20Sensor%20are%20Active.png)
_Dashboard view when sensors are actively enabled and monitoring_

## Settings and Configuration

### Interval and Runtime Configuration

![HASS Card Set Interval and Runtime](<HASS%20Card%20(Dual%20Mode%20Shown)%20Set%20Interval%20and%20runtime.png>)
_Configure automatic pump intervals and run duration_

### Timezone and WiFi Monitoring

![HASS Card Setup Timezone and Monitor WiFi](<HASS%20Card%20(Dual%20Mode%20Shown)%20Setup%20Timezone%20and%20Monitor%20Wifi%20Connection.png>)
_Configure device timezone and monitor WiFi connection status_

### Testing and Maintenance

![HASS Card Basic Test and Factory Reset or Reboot](<HASS%20Card%20(Dual%20Mode%20Shown)%20Basic%20Test%20and%20Factory%20Reset%20or%20Reboot.png>)
_Access to boot test sequence, factory reset, and device reboot functions_

## Card Features

- **Three-Mode Sensor System**: Choose between Disabled, Safety, or Trigger modes for each sensor
- **Collapsible Sections**: Keep your dashboard clean by collapsing settings when not needed
- **Dynamic Controls**: Buttons and options change based on operating mode and sensor configuration
- **Real-Time Status**: Monitor pump status, diagnostic information, and next scheduled run
- **Trigger Mode Indicator**: Visual confirmation when trigger mode is active
- **Safety Controls**: Easy access to safety limits and emergency stop functions
- **Sensor Integration**: Visual feedback when INPUT/OUTPUT sensors are enabled
- **System Management**: Quick access to testing, reset, and reboot functions

## New in Version jadehawk-2025.10.13.1

- ✨ **Sensor Mode Selectors**: Choose Disabled, Safety, or Trigger mode for each sensor
- 📊 **Diagnostic Status Sensor**: Real-time pump state reporting with blocking conditions
- ⚡ **Trigger Mode Indicator**: Shows when trigger mode is active
- 🧹 **Cleaner UI**: Removed legacy sensor switches for a modern interface

## Installation

See the main [README.md](../README.md#-home-assistant-dashboard-cards) for detailed installation instructions for both single-pump and multi-pump dashboard cards.
