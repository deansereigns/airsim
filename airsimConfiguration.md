# AirSim Manual Multirotor Control  
### API Commands, Alternatives, and Usage Guide

This document explains the **AirSim Python APIs used in the manual drone control script**, why they are used, and **available alternatives**, especially for **movement and vehicle control**.  
It also provides **comparison tables** and guidance on **when each command should be used**.

---

## 1. Connection and Control APIs

### `airsim.MultirotorClient()`

Creates a client object used to communicate with an AirSim multirotor vehicle.

```python
client = airsim.MultirotorClient()
```

**Use when**
- Controlling drones (quadrotor, hexacopter, etc.)

**Alternative**
- `airsim.CarClient()` → ground vehicles

---

### `client.confirmConnection()`

Waits until the simulator is reachable.

```python
client.confirmConnection()
```

**Why**
- Prevents sending commands before AirSim is ready
- Should always be called at startup

---

### `client.enableApiControl(True | False)`

Enables or disables API control over the vehicle.

```python
client.enableApiControl(True)
```

**Notes**
- Required before any movement command
- Disable on exit to release vehicle control

---

### `client.armDisarm(True | False)`

Arms or disarms the drone motors.

```python
client.armDisarm(True)
```

**Rules**
- Drone must be armed before takeoff
- Disarm after landing for safety

---

## 2. Takeoff and Landing APIs

### `client.takeoffAsync().join()`

Initiates drone takeoff.

```python
client.takeoffAsync().join()
```

---

### `client.moveToZAsync(z, velocity)`

Moves the drone to a target altitude (NED frame).

```python
client.moveToZAsync(-10, 2)
```

**Important**
- Negative Z means upward movement

---

### `client.landAsync().join()`

Safely lands the drone.

```python
client.landAsync().join()
```

---

## 3. Primary Movement Command

### `moveByVelocityBodyFrameAsync()`

```python
client.moveByVelocityBodyFrameAsync(
    vx, vy, vz, duration,
    drivetrain=airsim.DrivetrainType.MaxDegreeOfFreedom,
    yaw_mode=airsim.YawMode(True, yawRate)
)
```

---

## 4. Movement API Comparison

| API | Frame | Control | Use Case |
|----|----|----|----|
| moveByVelocityBodyFrameAsync | Body | Velocity | Manual control |
| moveByVelocityAsync | World | Velocity | Mapping |
| moveToPositionAsync | World | Position | Waypoints |
| moveOnPathAsync | World | Path | Trajectories |
| moveByAngleRatesThrottleAsync | Body | Attitude | Research |
| hoverAsync | — | Stabilize | Emergency |

---

## 5. State & Sensor APIs

### `client.simGetVehiclePose()`

```python
pose = client.simGetVehiclePose()
```

---

### `airsim.to_eularian_angles()`

```python
roll, pitch, yaw = airsim.to_eularian_angles(pose.orientation)
```

---

### `client.getGpsData()`

```python
gps = client.getGpsData()
```

---

## 6. Clean Shutdown

```python
client.landAsync().join()
client.armDisarm(False)
client.enableApiControl(False)
```

---

