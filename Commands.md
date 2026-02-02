| Category | Command / API | What It Does | How It Is Used in Your Script | Related / Alternative Commands |
|--------|---------------|--------------|-------------------------------|--------------------------------|
| Client | MultirotorClient() | Creates a client to control a drone (multirotor) | Initializes AirSim drone control | CarClient(), VehicleClient() |
| Connection | confirmConnection() | Waits until simulator is ready | Ensures AirSim is running before commands | — |
| Segmentation | simSetSegmentationObjectID() | Assigns semantic IDs to Unreal objects | Maps buildings, trees, vehicles, roads | simGetSegmentationObjectID(), simSetSegmentationObjectIDByName() |
| Imaging | simGetImages() | Captures multiple images in one call | Gets RGB + segmentation together | simGetImage() |
| Camera | ImageType.Scene | RGB camera output | Used for training images | Infrared, OpticalFlow |
| Camera | ImageType.Segmentation | Color-coded semantic labels | Used for YOLO label generation | DepthPlanar, DepthPerspective |
| API Control | enableApiControl(True) | Transfers vehicle control to script | Required before movement | enableApiControl(False) |
| Safety | armDisarm(True) | Arms drone motors | Required before takeoff | armDisarm(False) |
| Takeoff | takeoffAsync() | Performs autonomous takeoff | Starts flight safely | hoverAsync() |
| Altitude | moveToZAsync(z,v) | Moves drone to specific altitude | Sets initial flying height | moveToPositionAsync() |
| Motion | moveByVelocityBodyFrameAsync() | Moves drone using body-frame velocity | Keyboard-based drone control | moveByVelocityAsync(), moveByVelocityZAsync() |
| Drivetrain | DrivetrainType.MaxDegreeOfFreedom | Enables full 6-DOF motion | Allows vertical + lateral movement | ForwardOnly |
| Yaw | YawMode(is_rate, value) | Controls yaw rate or yaw angle | Keyboard yaw control | rotateToYawAsync(), rotateByYawRateAsync() |
| Landing | landAsync() | Performs safe autonomous landing | Ends flight cleanly | hoverAsync() |
| Cleanup | enableApiControl(False) | Returns control to simulator | Safe shutdown | reset() |
