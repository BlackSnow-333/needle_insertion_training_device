# Needle insertion training device (NITD)
Second year master project concerning the development of a haptic training device for needle insertion, based on the 2-Dof pantograph used at the ICube laboratory.

Documentation: <br/>
[Report](doc/placeholder.txt) <br/>
[Videos](doc/videos) <br/>

## Installation

### Prepare the environment

1) Install ROS2 Humble and the usual tools (Gazebo, Colcon, etc.)

2) Install EtherLab as specified in the [documentation of ethercat_driver_ros2](https://icube-robotics.github.io/ethercat_driver_ros2/).

> [!TIP]
> use the `ethercat slaves` CLI command to check that all is OK andf that you can see your device.

### Install this package

```bash
WS_PANTOGRAPH=~/dev/ws_pantograph_ros2/
mkdir -p $WS_PANTOGRAPH/src
cd $WS_PANTOGRAPH/src

git clone https://github.com/BlackSnow-333/needle_insertion_training_device.git
vcs import . < needle_insertion_training_device/nitd.repos
rosdep install --ignore-src --from-paths . -y -r

cd ..
colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release --symlink-install
source install/setup.bash
```

## Getting started

```bash
cd $WS_PANTOGRAPH
source install/setup.bash
```

```bash
# Launch with mock hardware
ros2 launch nitd_bringup nitd.launch.py
```

```bash
# With the actual robot
sudo /etc/init.d/ethercat start  # start ETherLab daemon
ros2 launch nitd_bringup nitd.launch.py use_fake_hardware:=false
```
```bash
# Load haptic_controller
ros2 control load_controller haptic_controller --set-state active
```

## TODO: finish readme
