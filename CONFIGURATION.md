# AirSim Configuration and Usage – Complete Reference

This document is a **single, self-contained Markdown file** that explains **everything end-to-end**:
configuration, concepts, and runtime usage of AirSim for drones and vehicles.

It includes:
- Descriptions (what & why)
- Configuration examples (`settings.json`)
- Runtime Python API usage
- Best practices and reality checks

This file is intended to be **read linearly**.

---

AirSim is a simulator for drones and vehicles used in:
- autonomy research
- perception and machine learning
- control systems
- synthetic dataset generation

AirSim has **two layers**:
1. **Configuration layer** (`settings.json`, loaded at startup)
2. **Runtime control layer** (Python / C++ APIs)

---

## Core Configuration File (`settings.json`)

`settings.json` defines the entire simulation.  
It is loaded **once when AirSim starts**.

```json
{
  "SettingsVersion": 1.2,
  "SimMode": "Multirotor",
  "ClockSpeed": 1,
  "Vehicles": {},
  "CameraDefaults": {},
  "ExternalCameras": {},
  "Wind": { "X": 0, "Y": 0, "Z": 0 }
}
```

Key points:
- JSON must be strict (no comments, no NaN)
- Restart the simulator after changing this file
- Missing fields use safe internal defaults

---

## Simulation Modes

```json
"SimMode": "Multirotor"
```

Supported values:
- `Multirotor` – full drone physics
- `Car` – ground vehicle
- `ComputerVision` – cameras only, no physics

---

## Vehicle Configuration

```json
"Vehicles": {
  "Drone1": {
    "VehicleType": "SimpleFlight",
    "AutoCreate": true,
    "EnableCollisions": true,
    "AllowAPIAlways": true,
    "X": 0,
    "Y": 0,
    "Z": 0,
    "Pitch": 0,
    "Roll": 0,
    "Yaw": 0
  }
}
```

---

## Runtime Vehicle Control (Python)

```python
import airsim

client = airsim.MultirotorClient()
client.confirmConnection()

client.enableApiControl(True, "Drone1")
client.armDisarm(True, "Drone1")
client.takeoffAsync(vehicle_name="Drone1").join()
client.moveToZAsync(-10, 2, vehicle_name="Drone1").join()
```

---

## Camera Image Types

| ID | Type |
|----|-----|
| 0 | Scene (RGB) |
| 1 | DepthPlanar |
| 2 | DepthPerspective |
| 3 | Segmentation |
| 4 | SurfaceNormals |
| 5 | Infrared |
| 6 | OpticalFlow |

---

## Camera Mounting (NED)

```json
"Cameras": {
  "FrontCam": {
    "X": 0.3,
    "Y": 0.0,
    "Z": -0.1,
    "Pitch": 0,
    "Roll": 0,
    "Yaw": 0
  }
}
```

---

## LiDAR Configuration

```json
"Sensors": {
  "LidarFront": {
    "SensorType": 6,
    "Enabled": true,
    "NumberOfChannels": 16,
    "Range": 100,
    "RotationFrequency": 10
  }
}
```

---

## Weather Control

```json
"Wind": {
  "X": 5,
  "Y": 0,
  "Z": 0
}
```

Runtime API:
```python
client.simSetWeatherParameter(
    airsim.WeatherParameter.Rain, 0.7
)
```

---

## Minimal Working Example

```json
{
  "SettingsVersion": 1.2,
  "SimMode": "Multirotor",
  "Vehicles": {
    "Drone1": {
      "VehicleType": "SimpleFlight",
      "AutoCreate": true,
      "Cameras": {
        "FrontCam": { "X": 0.3, "Z": -0.1 }
      },
      "Sensors": {
        "LidarFront": { "SensorType": 6, "Enabled": true }
      }
    }
  }
}
```

---
