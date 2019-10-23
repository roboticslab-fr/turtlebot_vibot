# TurtleBot Navigation - with RP LiDAR

## SLAM Map Building with TurtleBot

1. On turtlebot laptop

For basic use with standard parameters - use official :

    $ roslaunch turtlebot_navigation gmapping_demo.launch

For **customized gmapping files** (advanced use by tweeking gmapping files):

    $ roslaunch turtlebot_vibot_nav gmapping_demo_rplidar.launch

  -> include file = turtlebot_vibot_nav/launch/includes/gmapping/rplidar_gmapping.launch.xml
  -> include file = turtlebot_vibot_nav/launch/includes/move_base.launch.xml

2. On the Workstation

Start rviz already configured to visualize the robot and its sensor's output:
    
    $ roslaunch turtlebot_rviz_launchers view_navigation.launch

3. Save the map to file

After SLAM, the map which is built can be saved as a file with the following command.

    $ roscd turtlebot_vibot_nav/maps/

    $ rosrun map_server map_saver -f my_map

By default, map_saver retrieves map data and writes it out to map.pgm and map.yaml.
Use the -f option to provide a different base name for the output files.
(-f my_map => my_map.pgm + my_map.yaml)

Note: Do not close the gmapping launch until saving the map. 

## Autonomous Navigation of a Known Map with TurtleBot

1. On the TurtleBot laptop, run the navigation demo passing in your generated map file.

To do that you have **FIRST** to **define TURTLEBOT_MAP_FILE variable**.

Can be done manually in a command line :

        $ export TURTLEBOT_MAP_FILE=/...path.../map.yaml

    example : export TURTLEBOT_MAP_FILE=`rospack find turtlebot_vibot_nav`/maps/my_map.yaml

**OR** in the amcl_demo_rplidar.launch file :

    Change default value of map_file

      <!-- Map server -->
      <!--arg name="map_file" default="$(env TURTLEBOT_MAP_FILE)"/-->
      <arg name="map_file" default="(find turtlebot_vibot_nav)/maps/my_map.yaml"/>

**OR** Could be done manually when you launch the amcl_demo :

        $ roslaunch turtlebot_vibot_nav amcl_demo_rplidar.launch map_file:=...ABSOLUTE_PATH/catkin_ws/src/turtlebot_vibot/turtlebot_vibot_nav/maps/my_map.yaml

OPTIONAL -> Setting environment variable is also possible to set TURTLEBOT_MAP_FILE value :

    install first rospack-tools => $ sudo apt install rospack-tools

    TURTLEBOT_MAP_FILE has be set in *~/.bashrc* file by adding the following line AFTER you source your workspace
        
        #catkin
        source ~/ros/kinetic/catkin_ws/devel/setup.bash

        ## Set default map for TurtleBot navigation
        export TURTLEBOT_MAP_FILE=`rospack find turtlebot_vibot_nav`/maps/my_map.yaml

Once TURTLEBOT_MAP_FILE variable is set you can start the AMCL launch file to perform autonomous navigation :

        $ roslaunch turtlebot_vibot_nav amcl_demo_rplidar.launch

The *amcl_demo_rplidar.launch* launch file can be modified :

    You can manually set the initial pose of the robot (requires to start always your robot with a defined pose -> cross on the floor)
        <!-- AMCL -->
        ...
        <arg name="initial_pose_x
        ...

The *amcl_demo_rplidar.launch* launch file includes the following files that can be modified to perform fine tuning of parameters :

        -> include file = turtlebot_vibot_nav/launch/includes/amcl/rplidar_amcl.launch.xml => Copied from amcl.launch.xml

        -> include file = turtlebot_vibot_nav/launch/includes/move_base.launch.xml => Modified to include "turtlebot_vibot_nav/param" files

            --> include file = turtlebot_vibot_nav/param/costmap_common_params.yaml => global_costmap
            --> include file = turtlebot_vibot_nav/param/costmap_common_params.yaml => local_costmap
            --> include file = turtlebot_vibot_nav/param/local_costmap_params.yaml
            --> include file = turtlebot_vibot_nav/param/global_costmap_params.yaml
            ...
        
        -> include file = turtlebot_vibot_nav/param/rplidar_costmap_params.yaml => ready for customized parameters

To perform LiDAR navigation => **turtlebot_vibot_nav/param/costmap_common_params.yaml file settings**

- map_type: voxel -> changed to costmap for the RPLiDAR

- max_obstacle_height is set to 1.00 instead of 0.35 inside the obstacle layer because the Lidar height from the ground is greater than the Kinect’s height. Otherwise the obstacle layer will not record any obstacle.

- z_voxels: is set to 10 instead of 2 to enable LiDAR sensor to be within range of local map 

2. On the Workstation, launch rviz with the following command.

    $ roslaunch turtlebot_rviz_launchers view_navigation.launch --screen

3. RVIZ

**Localize the TurtleBot**

When starting up, the TurtleBot does not know where it is. To provide him its approximate location on the map:

    Click the "2D Pose Estimate" button

    Click on the map where the TurtleBot approximately is and drag in the direction the TurtleBot is pointing. 

You will see a collection of arrows which are hypotheses of the position of the TurtleBot. The laser scan should line up approximately with the walls in the map. If things don't line up well you can repeat the procedure.

Note: set the fixed frame to "map" in RVIZ's global options to use the "2D Pose Estimate button".

**Teleoperation**

The teleoperation can be run simultaneously with the navigation stack. It will override the autonomous behavior if commands are being sent. It is often a good idea to teleoperate the robot after seeding the localization to make sure it converges to a good estimate of the position.

**Send a navigation goal**

With the TurtleBot localized, it can then autonomously plan through the environment.

To send a goal:

    Click the "2D Nav Goal" button

    Click on the map where you want the TurtleBot to drive and drag in the direction the TurtleBot should be pointing at the end. 

This can fail if the path or goal is blocked.

If you want to stop the robot before it reaches it's goal, send it a goal at it's current location. 

## References

- BSCV5 students who did an impressive work : Sylwia Bakalarz, Arsanios Mickael & Lasse Mackeprang
https://github.com/roboticslab-fr/BSCV5_RobEng2

- For advanced tuning of TurtleBot navigation settings
http://kaiyuzheng.me/documents/navguide.pdf

- Excellent Course slides on navigation package
https://u.cs.biu.ac.il/~yehoshr1/89-685/Fall2015/ROS_Lesson7.pdf

