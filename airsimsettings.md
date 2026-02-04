# AirSim Settings.json
A self-contained guide for camera-centric AirSim simulations with weather effects.

---

## Architecture: Two Layers

| Layer | Purpose | Mutable at Runtime? |
|-------|---------|---------------------|
| Configuration (`settings.json`) | Defines vehicles, cameras, environment | ❌ Restart required |
| Runtime Control (Python API) | Controls motion, capture, weather | ✅ Dynamic |
---

## Core Configuration (`settings.json`)

**Location:** `~/Documents/AirSim/settings.json`

```
{
  "SettingsVersion": 1.2,
  "SimMode": "Multirotor",
  "ClockSpeed": 1,
  "Vehicles": {},
  "ExternalCameras": {},
  "Wind": { "X": 0, "Y": 0, "Z": 0 }
}

```
---

## Simulation Modes

| Mode | Physics | Weather |
|------|---------|---------|
| `Multirotor` | ✅ | ✅ |
| `Car` | ✅ | ✅ |
| `ComputerVision` | ❌ | ❌ |

> Use `Multirotor` for cameras + weather.

---
## Vehicle & Camera Configuration

```json
"Vehicles": {
  "Drone1": {
    "VehicleType": "SimpleFlight",
    "AutoCreate": true,
    "Cameras": {
      "FrontCam": {
        "X": 0.3,
        "Y": 0.0,
        "Z": -0.1,
        "Pitch": 0,
        "Roll": 0,
        "Yaw": 0,
        "CaptureSettings": [
          { "ImageType": 0, "Width": 1280, "Height": 720 }
        ]
      }
    }
  }
}

```

---
## Camera Image Types

| ID | Type |
|----|------|
| 0 | Scene (RGB) |
| 1 | DepthPlanar |
| 2 | DepthPerspective |
| 3 | DepthVis |
| 4 | DisparityNormalized |
| 5 | Segmentation |
| 6 | SurfaceNormals |
| 7 | Infrared |
| 8 | OpticalFlow |
| 9 | OpticalFlowVis |

---
## External Camera

```
"ExternalCameras": {
  "ObserverCam": {
    "X": -8,
    "Y": 0,
    "Z": -4,
    "Pitch": -10,
    "CaptureSettings": [
      { "ImageType": 0, "Width": 1920, "Height": 1080 }
    ]
  }
}
```

---
## Runtime Control (Python)

```
import airsim

client = airsim.MultirotorClient()
client.confirmConnection()

client.enableApiControl(True, "Drone1")
client.armDisarm(True, "Drone1")
client.takeoffAsync(vehicle_name="Drone1").join()
client.moveToZAsync(-10, 2, vehicle_name="Drone1").join()
```

---
## Capturing Images

```
# Onboard camera
img = client.simGetImage("FrontCam", airsim.ImageType.Scene, vehicle_name="Drone1")
if img:
    open("front.png", "wb").write(img)

# External camera
img = client.simGetImage("ObserverCam", airsim.ImageType.Scene)
if img:
    open("observer.png", "wb").write(img)
```

---
## Weather System

```
client.simEnableWeather(True)
client.simSetWeatherParameter(airsim.WeatherParameter.Rain, 0.8)
client.simSetWeatherParameter(airsim.WeatherParameter.Fog, 0.6)
client.simSetWeatherParameter(airsim.WeatherParameter.Roadwetness, 0.9)
```
---
## Wind Control

Static (config):
```
"Wind": { "X": 3, "Y": 0, "Z": 0 }

client.simSetWind(airsim.Vector3r(5, 0, 0))
```

---
## Minimal Working Example (`settings.json`)

```
{
  "SettingsVersion": 1.2,
  "SimMode": "Multirotor",
  "Vehicles": {
    "Drone1": {
      "VehicleType": "SimpleFlight",
      "AutoCreate": true,
      "Cameras": {
        "FrontCam": {
          "X": 0.3,
          "Z": -0.1,
          "CaptureSettings": [
            { "ImageType": 0, "Width": 1280, "Height": 720 }
          ]
        }
      }
    }
  },
  "ExternalCameras": {
    "ObserverCam": {
      "X": -8,
      "Y": 0,
      "Z": -4,
      "Pitch": -10,
      "CaptureSettings": [
        { "ImageType": 0, "Width": 1920, "Height": 1080 }
      ]
    }
  }
}

```

---
## Reality Checks

- ✅ Weather requires physics mode (`Multirotor`/`Car`)
- ✅ Wind (physics) ≠ Weather (visual)
- ❌ Cameras cannot be added after launch
- ❌ External cameras are static
- ⚠️ NED coordinates: +Z is down
---
## References

- https://github.com/microsoft/AirSim
- https://microsoft.github.io/AirSim/apis/
- https://microsoft.github.io/AirSim/settings/
---