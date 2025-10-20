# CRISP Controllers Demos

<img src="https://github.com/user-attachments/assets/284983f8-2311-4699-86ab-06fc2ea9d5af" alt="CRISP Controllers Logo" width="160" align="right"/>

<a href="https://github.com/utiasDSL/crisp_controllers_demos/actions/workflows/docker_build.yml"><img src="https://github.com/utiasDSL/crisp_controllers_demos/actions/workflows/docker_build.yml/badge.svg"/></a>
<img alt="Static Badge" src="https://img.shields.io/badge/arxiv-cite-b31b1b?style=flat&link=google.com">
<a href="https://utiasDSL.github.io/crisp_controllers/"><img alt="Static Badge" src="https://img.shields.io/badge/docs-passing-blue?style=flat&link=https%3A%2F%2FutiasDSL.github.io%2Fcrisp_controllers%2F"></a>

This repo provides Docker containers to provide directly test the [crisp_controllers](https://github.com/utiasDSL/crisp_controllers) with real hardware or in simulation with a simple [MuJoCo](https://github.com/google-deepmind/mujoco) `ros2_control` interface provided in this repository.

Check the [docs](https://utiasdsl.github.io/crisp_controllers/misc/demos/) on how to get started with the demos and with CRISP in general.

## Workspace Setup

```bash
# 1. Create a new workspace
mkdir my_crisp_ws && cd my_crisp_ws

# 2. Clone repositories
git clone https://github.com/kimhc6028/crisp_controllers_demos.git src/crisp_controllers_demos
git clone https://github.com/danielsanjosepro/franka_ros2.git src/franka_ros2

# 3. Source ROS 2
source /opt/ros/humble/setup.zsh

# 4. Install dependencies
sudo apt-get update 
vcs import src < src/franka_ros2/franka.repos --recursive --skip-existing
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro $ROS_DISTRO -y

# 5. Initial build
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
```
## Disable Unused Packages
```bash
# Ignore unnecessary packages for the Crisp Controllers setup
touch src/franka_ros2/COLCON_IGNORE
touch src/libfranka/COLCON_IGNORE
touch src/franka_description/COLCON_IGNORE
```

## Install Crisp Controllers
```bash
# Clone Crisp Controllers v1.1.0
git clone --branch v1.1.0 --depth 1 https://github.com/utiasDSL/crisp_controllers.git src/crisp_controllers
```

## Environment Setup & Dependencies
```bash
# Source ROS 2 and workspace setup
source /opt/ros/$ROS_DISTRO/setup.zsh
source ~/my_crisp_ws/install/setup.zsh
```

## Update dependencies
```bash
sudo apt update
rosdep update
rosdep install -q --from-paths src --ignore-src -y
```

## Final Build
```bash
colcon build --symlink-install \
    --cmake-args -DCMAKE_BUILD_TYPE=Release -DCMAKE_EXPORT_COMPILE_COMMANDS=ON \
    --packages-select crisp_controllers crisp_controllers_robot_demos franka_force_feedback_controllers

source install/setup.zsh
```

## Launch Demo
```bash
ros2 launch crisp_controllers_robot_demos franka.launch.py \
    arm_id:=fr3 \
    use_fake_hardware:=false \
    robot_ip:=<Your.Robot.IP> \
    use_rviz:=true \
    namespace:=gripper
```

## Control Interface
After launching, you can move the robot by pushing to:
* /target_pose:
```bash
ros2 topic pub /target_pose geometry_msgs/msg/PoseStamped "{header: {frame_id: 'base_link'}, pose: {position: {x: 0.3, y: 0.1, z: 0.5}, orientation: {x: 1.0, y: 0.0, z: 0.0, w: 0.0}}}"
```
* /target_wrench:
```bash
ros2 topic pub /target_wrench geometry_msgs/msg/WrenchStamped "{header: {stamp: {sec: 0, nanosec: 0}, frame_id: 'base'}, wrench: {force: {x: 0.0, y: 0.0, z: 0.0}, torque: {x: 0.0, y: 0.0, z: 0.0}}}"
```

## Dynamic Parameter Tuning
```bash
You can dynamically change controller parameters such as stiffness or damping. If you need guidance on dynamically adjusting these parameters in runtime, please contact heecheol.
```
