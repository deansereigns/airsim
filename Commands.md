# AirSim Multirotor API Usage Overview

This document provides a structured overview of the AirSim Multirotor APIs used in the script, including their purpose, usage context, and common alternatives.  
It is intended as a quick-reference for perception, control, and simulator lifecycle management.

---

## API Reference Table

| Category | Command / API | Description | Usage in Script | Related / Alternatives |
|--------|---------------|-------------|-----------------|------------------------|
| **Client** | `MultirotorClient()` | Creates a client for controlling multirotor vehicles (drones) | Initializes the AirSim drone interface | `CarClient()`, `VehicleClient()` |
| **Connection** | `confirmConnection()` | Blocks execution until the simulator is ready | Ensures AirSim is running before issuing commands | — |
| **Segmentation** | `simSetSegmentationObjectID()` | Assigns semantic IDs to Unreal Engine objects | Maps buildings, roads, vehicles, trees for labeling | `simGetSegmentationObjectID()`, `simSetSegmentationObjectIDByName()` |
| **Imaging** | `simGetImages()` | Captures multiple image types in a single request | Retrieves RGB and segmentation images together | `simGetImage()` |
| **Camera** | `ImageType.Scene` | Standard RGB camera output | Used for training and visualization images | `Infrared`, `OpticalFlow` |
| **Camera** | `ImageType.Segmentation` | Color-coded semantic segmentation output | Used for YOLO / semantic label generation | `DepthPlanar`, `DepthPerspective` |
| **API Control** | `enableApiControl(True)` | Transfers vehicle control from simulator to script | Required before issuing movement commands | `enableApiControl(False)` |
| **Safety** | `armDisarm(True)` | Arms drone motors | Mandatory before takeoff | `armDisarm(False)` |
| **Takeoff** | `takeoffAsync()` | Performs autonomous vertical takeoff | Starts flight sequence safely | `hoverAsync()` |
| **Altitude Control** | `moveToZAsync(z, v)` | Moves drone to a target altitude at a given speed | Sets initial flight height | `moveToPositionAsync()` |
| **Motion** | `moveByVelocityBodyFrameAsync()` | Moves drone using body-frame velocity commands | Enables keyboard-style drone control | `moveByVelocityAsync()`, `moveByVelocityZAsync()` |
| **Drivetrain** | `DrivetrainType.MaxDegreeOfFreedom` | Enables full 6-DOF motion control | Allows vertical and lateral movement | `ForwardOnly` |
| **Yaw Control** | `YawMode(is_rate, value)` | Controls yaw via angle or angular rate | Used for keyboard-based yaw control | `rotateToYawAsync()`, `rotateByYawRateAsync()` |
| **Landing** | `landAsync()` | Performs autonomous landing | Ends flight safely | `hoverAsync()` |
| **Cleanup** | `enableApiControl(False)` | Returns control back to the simulator | Ensures clean shutdown | `reset()` |

---

## Notes

- APIs ending with `Async()` are **non-blocking** and should be synchronized with `.join()` when sequential execution is required.
- Prefer `simGetImages()` over multiple `simGetImage()` calls for **better performance**.
- Segmentation IDs must be assigned **before** image capture to ensure correct semantic labeling.