# Installing packages

### Gazebo
```
sudo apt install ros-humble-gazebo-ros-pkgs
```

### colcon
```
sudo apt install python3-colcon-common-extensions
```

### Teleop Twist with topic remapo
```
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/diff_cont/cmd_vel_unstamped
```

# Ros 2 Control
- Controller manager - finds all the codes for divers and links them together. Uses plugins

- Uses command interfaces and state interfaces 
    - state interfaces are read-only
    - command interfaces are read/write. Things that we can control e.g velocity
- Hardware interfaces are managed by resource manager that knows about them using ```<ros2_control>``` on the urdf

### Installation

```
sudo apt install ros-humble-ros2-control ros-humble-ros2-controllers ros-humble-gazebo-ros2-control
```
# SLAM
### installing the toolbox
```
sudo apt install ros-humble-slam-toolbox
```

copy the online async config file to your package
```
cp /opt/ros/humble/share/slam_toolbox/config/mapper_params_online_async.yaml ./src/config/
```

### launching the file

```
ros2 launch slam_toolbox online_async_launch.py slam_params_file:=./src/dojo/config/mapper_params_online_async.yaml use_sim_time:=true

```

save the map

### install nav2

```
sudo apt install ros-humble-navigation2 ros-humble-nav2-bringup
```

### Running nav2

run in the dojo2024 workspace folder

You can get the my_map_save files in https://github.com/ru3ll/dojo2024

```
ros2 run nav2_map_server map_server --ros-args -p yaml_filename:=my_map_save.yaml -p use_sim_time:=true
```

#### Activate the node
```
ros2 run nav2_util lifecycle_bringup map_server
```

### Run AMCL (Monty Carlo Localisarion)

```
ros2 run nav2_amcl amcl --ros-args -p use_sim_time:=true
```

#### Activate the node
```
ros2 run nav2_util lifecycle_bringup amcl
```
### RPLIDAR

#### Installation

follow instructions on  https://github.com/Slamtec/sllidar_ros2 and add it to the repo

# Nav2
### Activation
```
ros2 launch nav2_bringup navigation_launch.py use_sim_time:=true
```

### install twist_mux to allow for parallel control
```
sudo apt install ros-humble-twist-mux
```
### Running twist mux
Start Twist mux
```
ros2 run twist_mux twist_mux --ros-args --params-file ./src/dojo/config/twist_mux.yaml -r cmd_vel_out:=diff_cont/cmd_vel_unstamped
```

Remap ```cmd_vel``` from teleop_twist_keyboard to ```cmd_vel_key```
```
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/cmd_vel_key
```  

# How To run localisation
### Step 1:
launch the robot, gazebo, controllers, and twist_mux from one file.
```
ros2 launch dojo launch_sim.launch.py world:=./src/dojo/worlds/dojo2024
```

### Step 2:
launch rviz with the environment on the folder
```
rviz2 -d ./src/dojo/rviz/default.rviz
```

### Step 3:
launch teleop twist keyboard
```
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/cmd_vel_key
```

### Step 4:
Launch SLAM for navigation
```
ros2 launch dojo localization_launch.py 
```

### Step 5:
Launch Nav2
```
ros2 launch dojo navigation_launch.py use_sim_time:=true map subscribe transient_local:=true
```
