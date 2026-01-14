# 🤖 Sampling Robot: ROS 2 & Gazebo Simulation

A custom-designed **Differential Drive Robot** featuring a 3-DOF robotic arm, simulated within the **ROS 2 Humble** environment and **Gazebo**. This project demonstrates the integration of URDF modeling, ROS 2 control architectures, and physics-based simulation tuning.

 

## 🛠 Features
* **Detailed URDF Modeling**: Developed a complex robot hierarchy including a mobile base, revolute arm joints, and a prismatic tool link.
* **ROS 2 Control Integration**: Implemented `diff_drive_controller` for mobility and `joint_state_broadcaster` for real-time telemetry.
* **Physics-Based Tuning**: 
    * Managed a **6.0kg** robot mass by optimizing `max_wheel_torque` to 100.0.
    * Balanced ground contact physics by aligning caster wheel offsets ($z = -0.045$) with drive wheel depth ($z = -0.065$).
    * Reduced friction coefficients ($mu1/mu2$) to 0.1 on the base to ensure smooth translation.

## 🏗 Robot Architecture

### Joint Configuration
| Joint | Type | Purpose | Key Parameter |
| :--- | :--- | :--- | :--- |
| `left_wheel_joint` | Continuous | Driving | `mu1: 1.0` |
| `right_wheel_joint` | Continuous | Driving | `mu1: 1.0` |
| `joint_1` | Fixed | Arm Base | `xyz: 0 0 0.025` |
| `joint_2` | Revolute | Arm Pivot | `limit: +/- 1.57` |
| `joint_3` | Prismatic | Tool Extension | `limit: 0.05` |

## 🚀 How to Run
1. **Launch the Simulation**:
   ```bash
   ros2 launch sampling_robot rsp.launch.py
2. **Move the robot**
   ros2 topic pub /diff_drive_controller/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 0.5}, angular: {z: 0.0}}"
3. **Control prismatic arm**
   ros2 topic pub /set_joint_trajectory trajectory_msgs/msg/JointTrajectory "{joint_names: ['joint_3'], points: [{positions: [0.03]}]}" -1
## 🧠Technical Lessons Learned

    The "Invisible Robot" Bug: Discovered that Gazebo models fail to spawn if revolute joints are missing their <limit> tags, resulting in model parsing errors.

    Physics Overlap: Solved a "physics explosion" where the robot would jitter/rotate by shrinking the base_link collision box to avoid overlapping with caster wheel geometry.

    Telemetry Analysis: Utilized ros2 topic echo /dynamic_joint_states to confirm controller activity even when physical friction was preventing visible movement from a standstill.
