ROS 2 Exploration Project Handoff
1. Project Status
Status: Operational. We have successfully set up an autonomous exploration pipeline using ROS 2 Humble. The robot (TurtleBot3 Waffle) navigates a headless Gazebo simulation, builds a map using SLAM, and autonomously seeks out unknown areas using `explore_lite`.

• Key Achievement: Resolved TF/Time synchronization issues between the simulation and Nav2 by strictly enforcing `use_sim_time:=True`.

• Current Mode: Headless simulation (No GUI) to ensure stability. Visualization is handled via RViz.

2. Repositories & Components Used
• m-explore-ros2 (Cloned Source):

  • Role: The "Brain". It identifies frontiers (edges between known and unknown space) and sends navigation goals.

  • Config: Custom `params.yaml` tuned with `min_frontier_size: 0.5` for tighter spaces.

• Navigation2 (System Install + Custom Config):

  • Role: The "Driver". Handles path planning and obstacle avoidance.

  • Customization: We use a local `nav2_params.yaml` (in `src/config`) patched to fix plugin syntax (`::` vs `/`) and force simulation time.

• SLAM Toolbox (System Install):

  • Role: The "Cartographer". Generates the 2D occupancy map from laser scans.

• TurtleBot3 Gazebo (System Install):

  • Role: The "Body" & "World". Provides the physics simulation and sensor data.

3. Setup Instructions (How to Build)
Prerequisites: Ubuntu 22.04, ROS 2 Humble.

1. Create Workspace & Clone:

```

mkdir -p ~/ros2_ws/src

cd ~/ros2_ws/src

# [Clone this repository here]

```

2. Install Dependencies:

```

cd ~/ros2_ws

# Install system packages

sudo apt install ros-humble-navigation2 ros-humble-nav2-bringup ros-humble-slam-toolbox ros-humble-turtlebot3-gazebo

# Install package dependencies

rosdep install --from-paths src --ignore-src -y -r

```

3. Build:

```

colcon build --symlink-install

source install/setup.bash

```


4. Launch Sequence (5 Terminals)
Note: Run `source install/setup.bash` in every terminal.

Terminal 1: Simulation (Headless)

```

export TURTLEBOT3_MODEL=waffle

ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py gui:=false

```

Terminal 2: SLAM

```

ros2 launch slam_toolbox online_async_launch.py use_sim_time:=True

```

Terminal 3: Nav2

```

ros2 launch nav2_bringup navigation_launch.py use_sim_time:=True params_file:=~/ros2_ws/src/config/nav2_params.yaml

```

Terminal 4: Explore Lite

```

ros2 launch explore_lite explore.launch.py use_sim_time:=True params_file:=~/ros2_ws/src/m-explore-ros2/explore/config/params.yaml

```

Terminal 5: Visualization

```

ros2 run rviz2 rviz2 -d $(ros2 pkg prefix nav2_bringup)/share/nav2_bringup/rviz/nav2_default_view.rviz

```
