# Installation instructions

## Install ROS Melodic (ros-desktop-full)

Follow the installation guide of ROS Melodic from here(https://wiki.ros.org/melodic/Installation/Ubuntu).
Make sure to follow all the steps described there.
## Install catkin_tools
In this guide is assumed that the workspace is catkin_ws/ , if it is different change it with the actual name of your workspace, i.e. catkin_ws/ --> my_catkin_ws/  
```
sudo apt update
sudo apt install python-catkin-tools

mkdir -p catkin_ws/src
cd catkin_ws/
wstool init src
```

## Setup ROS workspace
### Update .rosinstall
```
cd ~/catkin_ws/src
gedit .rosinstall
```
Paste the following:
```
- git:
    local-name: px4
    uri: https://github.com/danielduberg/Firmware.git
    version: v1.8.2.1

- git:
    local-name: inkonova_stl_aep
    uri: https://github.com/TadielloM/Inkonova-stl-aep.git
    version: master

#- git:
#    local-name: catkin_simple
#    uri: https://github.com/catkin/catkin_simple.git
#    version: master

- git:
    local-name: octomap_mapping
    uri: https://github.com/OctoMap/octomap_mapping.git
    version: kinetic-devel

- git:
    local-name: dd_gazebo_plugins
    uri: https://github.com/danielduberg/dd_gazebo_plugins.git
    version: master

- git:
    local-name: dd_control
    uri: https://github.com/danielduberg/dd_control.git
    version: master

- git:
    local-name: dd_nav_goal
    uri: https://github.com/danielduberg/dd_nav_goal.git
    version: master
 
- git:
    local-name: velodyne_simulator
    uri: https://bitbucket.org/DataspeedInc/velodyne_simulator.git
    version: master

- git:
    local-name: servo_ax12a 
    uri: https://github.com/Moes96/servo_pkg.git
    version: master

```
Then continue the installation:
```
wstool update
cd ..
rosdep install --from-paths src --ignore-src -r -y
sudo apt install ros-melodic-octomap-*
sudo apt-get install cmake python-pip python-rosinstall python-rosinstall-generator python-wstool
sudo apt-get install build-essential python-catkin-tools libprotobuf-dev libprotoc-dev protobuf-compiler
sudo apt-get install libeigen3-dev libgstreamer1.0-* libimage-exiftool-perl python-jinja2 libgeographic-dev geographiclib-tools ros-melodic-mav*
pip install numpy toml future
cd ~/catkin_ws
wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
chmod +x install_geographiclib_datasets.sh
sudo ./install_geographiclib_datasets.sh
cd src/inkonova_stl_aep/simulation/models/servo_ax12a/
mkdir build
cd ~/catkin_ws/src/inkonova_stl_aep/simulation/models/servo_ax12a/build/
cmake ..
make
```
Add this to your .bashrc file found at ~/.bashrc:
```
#Gazebo variable for location of the models,worlds and plugins
export GAZEBO_RESOURCE_PATH=~/catkin_ws/src/inkonova_stl_aep/simulation/worlds:${GAZEBO_RESOURCE_PATH}
export GAZEBO_MODEL_PATH=~/catkin_ws/src/px4/Tools/sitl_gazebo/models:~/catkin_ws/src/inkonova_stl_aep/simulation/models:${GAZEBO_MODEL_PATH}
export GAZEBO_PLUGIN_PATH=~/catkin_ws/src/px4/build/posix_sitl_default/build_gazebo:~/catkin_ws/src/inkonova_stl_aep/simulation/models/servo_ax12a/build:${GAZEBO_PLUGIN_PATH}
# Set path to sitl_gazebo repository
export SITL_GAZEBO_PATH=~/catkin_ws/src/px4/Tools/sitl_gazebo
export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:~/catkin_ws/src/px4/Tools/sitl_gazebo/Build/msgs/:~/catkin_ws/src/px4/build/posix_sitl_default/build_gazebo
# So ROS finds these packages
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/catkin_ws/src/px4:~/catkin_ws/src/px4/Tools/sitl_gazebo
```
## Build the UAV
```
cd ~/catkin_ws/src/px4
make posix_sitl_default gazebo
```
## Build workspace
```
cd ~/catkin_ws/
catkin config --cmake-args -DCMAKE_BUILD_TYPE=RelWithDebInfo
catkin build
```
