# Mars Rover Terrain Navigation and Mapping

A comprehensive ROS 2-based simulation environment for Mars rover navigation and terrain mapping. This project enables autonomous navigation, obstacle avoidance, and 3D mapping capabilities in a simulated Mars environment using Gazebo and RViz.

## Project Overview

This project simulates a Mars rover that can:
- Navigate autonomously in a Mars-like terrain environment
- Generate and utilize 3D maps using RTAB-Map
- Avoid obstacles using integrated sensors
- Collect and store mapping data for analysis
- Visualize robot state and sensor data in RViz

## System Requirements

### Hardware Requirements
- Minimum 8GB RAM (16GB recommended)
- Multi-core processor
- GPU with OpenGL support
- 20GB free disk space

### Software Requirements
- Ubuntu 22.04 LTS
- ROS 2 Humble
- Gazebo 11
- Python 3.10+
- OpenCV 4.x

## Project Structure

```
rover_terrain/
├── models/                 # Gazebo model files (can be global or within simple_rover)
├── src/
│   └── simple_rover/
│       ├── config/        # Configuration files (e.g., nav2_params.yaml, rtabmap_params.yaml)
│       ├── launch/        # Launch files (e.g., rover_sim.launch.py)
│       ├── maps/          # Map files
│       ├── models/        # Rover specific models/meshes (if not in global models/)
│       ├── urdf/          # Rover URDF files (e.g., rover.urdf.xacro)
│       ├── worlds/        # World files (e.g., mars_terrain.world)
│       ├── scripts/       # Python scripts
│       ├── python/        # Python module files
│       ├── lib/           # C++ library files
│       ├── CMakeLists.txt # Build script for C++
│       └── package.xml    # Package manifest
├── build/                 # Build directory
├── install/               # Install directory
└── log/                   # Log files
```

## Installation

### 1. Install ROS 2 Humble
Follow the official ROS 2 Humble installation instructions:
```bash
# Add ROS 2 apt repository
sudo apt update && sudo apt install software-properties-common
sudo add-apt-repository universe
sudo apt update && sudo apt install curl
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
sudo apt update
sudo apt install ros-humble-desktop-full
```

### 2. Install Required Dependencies
```bash
sudo apt update && sudo apt install -y \\
    ros-humble-gazebo-ros-pkgs \\
    ros-humble-navigation2 \\
    ros-humble-cv-bridge \\
    ros-humble-tf2-tools \\
    ros-humble-xacro \\
    python3-opencv \\
    python3-rosdep
```

### 3. Set Up the Workspace
```bash
# Create and build the workspace
mkdir -p ~/rover_terrain/src
# If you haven't already, clone your 'simple_rover' package into ~/rover_terrain/src/
# For example: git clone <your-repo-url> ~/rover_terrain/src/simple_rover
cd ~/rover_terrain
colcon build --symlink-install --packages-select simple_rover

# Source the workspace
echo "source ~/rover_terrain/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

## Usage

### Basic Operation

1. Launch the simulation (assuming a launch file like `rover_sim.launch.py` exists in `src/simple_rover/launch/`):
```bash
ros2 launch simple_rover rover_sim.launch.py
```
*Note: Replace `rover_sim.launch.py` with the actual name of your main launch file if different.*

2. Control the rover manually (if teleop_twist_keyboard is installed and configured):
```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

### Launch File Arguments

The main launch file (e.g., `rover_sim.launch.py`) might support arguments like:
- `use_sim_time` (default: true): Use simulation time
- `use_rviz` (default: true): Launch RViz visualization
- `use_gazebo_gui` (default: true): Launch Gazebo GUI
- `world_file` (default: `mars_terrain.world` or similar from `src/simple_rover/worlds/`): Specify world file
- `use_mapping` (default: true): Enable RTAB-Map mapping

Example with custom arguments:
```bash
ros2 launch simple_rover rover_sim.launch.py use_rviz:=false use_gazebo_gui:=true
```

## Configuration

### Modifying Robot Parameters
- URDF configuration: `src/simple_rover/urdf/your_rover_model.urdf.xacro` (replace `your_rover_model.urdf.xacro`)

### Customizing the Environment
- World files: `src/simple_rover/worlds/`
- Model files: `models/` (global) or `src/simple_rover/models/` (package-specific)

## Data Collection and Analysis


### Accessing Sensor Data
```bash
# List available topics
ros2 topic list

# Common topics (actual topic names may vary based on your URDF and plugins):
# - /camera/rgb/image_raw
# - /camera/depth/image_raw
# - /scan
# - /odom
# - /tf
# - /tf_static
```

## Troubleshooting

### Common Issues and Solutions

1. Gazebo fails to launch:
   - Check GPU drivers
   - Verify Gazebo installation: `gazebo --version`
   - Reset Gazebo: `killall gzserver && killall gzclient`
   - Check for errors in the terminal when launching.

2. RViz display issues:
   - Verify TF tree: `ros2 run tf2_tools view_frames.py`. Then `evince frames.pdf`
   - Check topic connections: `ros2 topic info /tf`
   - Ensure correct fixed frame is set in RViz.
   - Reset RViz configuration.

3. Rover not moving or behaving unexpectedly:
   - Check controller and plugin configurations in your URDF.
   - Verify `cmd_vel` topic is being published to by teleop or navigation stack.
   - Check joint states and effort controllers if applicable.

### Debug Commands
```bash
# Check ROS 2 node status
ros2 node list
ros2 node info /<node_name>

# Monitor topics
ros2 topic echo /<topic_name>
ros2 topic hz /<topic_name>

# View TF tree (generates a frames.pdf file)
ros2 run tf2_tools view_frames.py
evince frames.pdf

# Check Gazebo plugins and models
# (Look at Gazebo console output)

# Colcon build with specific package and verbosity
colcon build --symlink-install --packages-select simple_rover --event-handlers console_direct+
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit changes
4. Push to the branch
5. Create a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Contact

For issues and support:
- Create an issue in the repository
- Contact the maintainers
- Check the ROS 2 community forums 
