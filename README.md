# turtlebot_vibot
Metapackage to handle our customized TurtleBot2 fleet

## RP-LiDAR A1 Installation

Install rp-lidar package by following the instructions provided by the manufacturer :
Slamtec repository for ROS packages : https://github.com/Slamtec/rplidar_ros
(see references on the repository for more informations on the LiDAR)

### Remap the USB serial port name
Plug the LiDAR to a USB port

To use a fixed rplidar port, you can use the script file to remap the USB port name:

    $ roscd rplidar_ros
    $ ./scripts/create_udev_rules.sh
  
  /dev/ttyUSBx is remapped to /dev/rplidar
 
## TurtleBot with RP-LiDAR A1

To start the TurtleBot with RP-LiDAR - on the TB laptop :

    $ roslaunch turtlebot_vibot_bringup minimal_rplidar.launch
    
To view the robot with LiDAR in rviz :

    $ roslaunch turtlebot_vibot_bringup view_robot_rplidar.launch 
