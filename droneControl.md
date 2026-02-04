# AirSim Drone Manual Control with Flight Logging

This script enables real-time manual control of a drone in **AirSim** using keyboard inputs and logs flight telemetry to a CSV file.

---

## Table of Contents
- [Dependencies](#dependencies)
- [Initialization](#initialization)
- [Flight Logging Setup](#flight-logging-setup)
- [Control Parameters](#control-parameters)
- [Main Control Loop](#main-control-loop)
- [Command Handling](#command-handling)
- [Telemetry Logging](#telemetry-logging)
- [Cleanup](#cleanup)

---

## Dependencies

```
import airsim
import keyboard
import time
import csv
import math
import os
from datetime import datetime
```

| Package     | Purpose                                      |
|-------------|----------------------------------------------|
| `airsim`    | Interface with AirSim simulator              |
| `keyboard`  | Capture real-time keyboard input             |
| `csv`      | Write structured flight logs                 |
| `os`        | Handle file system operations                |
| `datetime`  | Generate timestamped log filenames           |

---

## Initialization
```
client = airsim.MultirotorClient()
client.confirmConnection()
```
- Connects to the AirSim simulation server.
- `confirmConnection()` blocks until a successful connection is established.

---

## Flight Logging Setup

```
os.makedirs("flightLogs", exist_ok=True)
log_path = f"flightLogs/drone_path_{datetime.now().strftime('%Y%m%d_%H%M%S')}.csv"
print(f"[LOG] Saving flight to: {log_path}")

logFile = open(log_path, "w", newline="")
writer = csv.writer(logFile)

writer.writerow([
    "time", "command", "vx","vy","vz","yawRate",
    "x","y","z", "roll","pitch","yaw",
    "gpsLat","gpsLon","gpsAlt"
])
```

- Creates a `flightLogs` directory if it doesn’t exist.
- Generates a unique filename using current timestamp.
- Writes a header row with telemetry field names.

---

## Control Parameters

```
VEL = 2.5        # m/s
YAW_RATE = 25    # degrees per second

vx = vy = vz = 0.0
yawRate = 0.0
currentCmd = "idle"
flying = False
running = True
```

| Variable      | Description                              |
|---------------|------------------------------------------|
| `VEL`         | Linear velocity magnitude (m/s)          |
| `YAW_RATE`    | Angular yaw rate (deg/s)                 |
| `flying`      | Flag indicating airborne state           |
| `running`     | Main loop control flag                   |

---

## Main Control Loop

```
while running:
    vx = vy = vz = 0.0
    yawRate = 0.0
    currentCmd = "idle"

    time.sleep(0.05)
```

- Runs at ~20 Hz (`0.05 s` sleep).
- Resets velocity/yaw each iteration for momentary control.

---

## Command Handling

### Takeoff / Land

```
if keyboard.is_pressed("y") and not flying:
    client.enableApiControl(True)
    client.armDisarm(True)
    client.takeoffAsync().join()
    client.moveToZAsync(-10, 2).join()
    flying = True
    currentCmd = "takeoff"

if keyboard.is_pressed("l") and flying:
    client.landAsync().join()
    client.armDisarm(False)
    client.enableApiControl(False)
    flying = False
    currentCmd = "land"
```

- **Takeoff**: Arms drone, takes off, then moves to `10 m` altitude (NED frame).
- **Land**: Lands and disarms.

### Movement Commands

```
if keyboard.is_pressed("w"): vx = VEL; currentCmd = "forward"
elif keyboard.is_pressed("s"): vx = -VEL; currentCmd = "backward"

if keyboard.is_pressed("a"): vy = -VEL; currentCmd = "left"
elif keyboard.is_pressed("d"): vy = VEL; currentCmd = "right"

if keyboard.is_pressed("z"): vz = -VEL; currentCmd = "up"
elif keyboard.is_pressed("c"): vz = VEL; currentCmd = "down"

if keyboard.is_pressed("q"): yawRate = -YAW_RATE; currentCmd = "yawLeft"
elif keyboard.is_pressed("e"): yawRate = YAW_RATE; currentCmd = "yawRight"
```

| Key | Action        | Body Frame Direction |
|-----|---------------|----------------------|
| W   | Forward       | +X                   |
| S   | Backward      | -X                   |
| A   | Left          | -Y                   |
| D   | Right         | +Y                   |
| Z   | Up            | -Z (NED)             |
| C   | Down          | +Z (NED)             |
| Q   | Yaw Left      | Negative rotation    |
| E   | Yaw Right     | Positive rotation    |

> **Note**: AirSim uses **NED (North-East-Down)** coordinate system. Hence, “up” is negative Z.

### Exit

```
if keyboard.is_pressed("esc"):
    running = False
    break
```

---

## Telemetry Logging

```
client.moveByVelocityBodyFrameAsync(
    vx, vy, vz, 0.1,
    drivetrain=airsim.DrivetrainType.MaxDegreeOfFreedom,
    yaw_mode=airsim.YawMode(True, yawRate)
)

pose = client.simGetVehiclePose()
gps = client.getGpsData()

pos = pose.position
q = pose.orientation
roll, pitch, yaw = airsim.to_eularian_angles(q)

writer.writerow([
    time.time(), currentCmd,
    vx, vy, vz, yawRate,
    pos.x_val, pos.y_val, pos.z_val,
    roll, pitch, yaw,
    gps.gnss.geo_point.latitude,
    gps.gnss.geo_point.longitude,
    gps.gnss.geo_point.altitude
])
```

- Sends velocity command in **body frame** with specified yaw rate.
- Logs:
  - Timestamp and command name
  - Commanded velocities and yaw rate
  - Position (NED)
  - Orientation (Euler angles in radians)
  - GPS coordinates (WGS84)

---

## Cleanup

```
if flying:
    client.landAsync().join()
    client.armDisarm(False)
    client.enableApiControl(False)

logFile.close()
print("Session Ended Safely")
```

- Ensures safe landing if still airborne.
- Disarms and releases API control.
- Closes the CSV log file.

--- 

> **Best Practice**: Always disarm and disable API control on exit to prevent simulation lockups.
'''
with open('AirSim_Drone_Manual_Control.md', 'w') as f:
    f.write(content)
print('File saved: AirSim_Drone_Manual_Control.md')
"
---