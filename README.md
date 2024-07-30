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
## Haptic controller usage

```bash
# Load haptic_controller
ros2 control load_controller haptic_controller --set-state active
```

```bash
# Change kp gain
ros2 param set /haptic_controller model_parameters.kp 1.5
```


```bash
# Change kd gain
ros2 param set /haptic_controller model_parameters.kd_tip 0.5
```

## Controller logic
The controler `update` method computes the torque that has to be sent to the pantograph active joints, according to the virtual work principle : <br/>
**Virutal work principle**
$$ \tau = J^T(Q). F_{mech}$$

With $F_{mech}$ the force that the pantograph needs to apply to produce the desired guiding force $F_{guide}$ at the point the user holds the needle. $F_{guide}$ is proportional to the angle error $\varepsilon$ , the difference between the predefined trajectory and the current needle position : 

$$F_{guide} = K_p. \varepsilon . v_u$$

$F_{mech}$ is a function of $F_{guide}$ and is calculated as follows : <br/>
**Pantograph force calculation**

$$F_{mech} = \lVert F_{mech} \rVert . v_{mech}  $$

Finaly we add a dampening coefficient to the force used to calculate the joint torques : 
$$ F_{mech}^{'} = \lVert F_{mech} \rVert . v_{mech}- K_d . J.\dot{Q}$$

For more details about the calculations for $\lVert F_{mech} \rVert$ , $v_{mech}$ and $v_{u}$ check the report pf the master project by Luis MALDONADO (TODO: add report) 


