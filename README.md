# Udacity Project: Where am I
To utilize ROS AMCL package to accurately localize a mobile robot inside a map in the Gazebo simulation environments.
## Prerequisite before Building  
1. Install ROS navigation package
   ```
   $ sudo apt-get install ros-<$ ros-distro>-navigation
   ```
   The following two packages will be included in the navigation package
   - [**amcl**](http://wiki.ros.org/amcl)  
   Adaptive Monte Carlo Localization (AMCL) dynamically adjusts the number of particles over a period of time, as the robot navigates around in a map.  
   - [**map_server**](http://wiki.ros.org/map_server)  
   The `map_server` node provides map data as a service to other nodes such as the amcl node. Here, `map_server` node will locate the map you created in the Map Setup step and send it out as the map data.  
2. Install dependencies for `pgm_map_creator` package
   ```
   $ sudo apt-get install libignition-math2-dev protobuf-compiler
   ```
## Build
After cloning the repo to the src folder of catkin workspace, open a new terminal and run 
```
$ cd <Your catkin workspace>
$ catkin_make
$ source devel/setup.bash
```
## Packages
- [**pgm_map_creator**](https://github.com/udacity/pgm_map_creator)  
To create a pgm file for environment map, as the AMCL package requires a pgm file to work with.  
- [**amcl**](http://wiki.ros.org/amcl)  
Adaptive Monte Carlo Localization (AMCL) dynamically adjusts the number of particles over a period of time, as the robot navigates around in a map.  
- [**map_server**](http://wiki.ros.org/map_server)  
The `map_server` node provides map data as a ROS service to other nodes such as the amcl node. Here, `map_server` node will locate the map you created in the Map Setup step and send it out as the map data.  
- [**ros-teleop**](https://github.com/ros-teleop/teleop_twist_keyboard)  
To manually control a robot using keyboard commands.  
- **my_robot**  
Self created robot configuration files, including maps, worlds, rviz configuration, launch files `amcl.launch`, `world.launch` and urdf files.  
## Configuration Files
- `map.pgm`, `zheshien_world.yaml`  
Both files are required to provide a map for amcl, located in the **my_robot** package **maps** folder.
- `zheshien.world`  
World file to setup the environment in Gazebo, located in the **my_robot** package **worlds** folder.
## AMCL Parameters
#### The following are the set of parameters used to help imrpoving the results. For more information of the parameters, please refer to [AMCL Parameters](http://wiki.ros.org/amcl#Parameters)
- transform_tolerance  
- min_particles
- initial_pose_x
- initial_pose_y
- initial_pose_a
- update_min_d
- update_min_a
- laser_min_range
- laser_max_range
- laser_max_beams
- laser_z_hit
- laser_z_rand
- odom_alpha1
- odom_alpha2
- odom_alpha3
- odom_alpha4  
## Running the Scripts
1. Open a new terminal and run
   ```
   roslaunch amcl.launch
   ```
2. Open another terminal and launch
   ```
   rosrun teleop_twist_keyboard teleop_twist_keyboard.py
   ```
## Create Your Own Map (Optional)
#### It is also possible to create your own map.pgm file using your own Gazebo world file. To do this:  
1. First, copy your world file into `/pgm_map_creator/world/`.  
2. Insert the map creator plugin to your map file. Open the map file using the editor of your choice. Add the following tag towards the end of the file, but just before `</world>` tag.  
   ```
   <plugin filename="libcollision_map_creator.so" name="collision_map_creator"/>
   ```
3. Open a terminal and run gzerver with the map file
   ```
   gzserver src/pgm_map_creator/world/<YOUR GAZEBO WORLD FILE>
   ```
4. Open another terminal, launch the request_publisher node
   ```
   roslaunch pgm_map_creator request_publisher.launch
   ```
5. Wait for the plugin to generate map. It will be located in the map folder of the **pgm_map_creator**. Now add the map to my_robot package's world folder.
   ```
   cd /workspace/catkin_ws/
   cp src/pgm_map_creator/maps/<YOUR MAP NAME>  src/my)robot/maps/<YOUR MAP NAME>
   ```
6. You would also need a yaml file providing the metadata about the map. Create a yaml file next to your map.
   ```
   cd src/<YOUR PACKAGE NAME>/src/maps
   touch <YOUR MAP NAME>.yaml
   ```
7. Open the yaml file and add the following lines to it:
   ```
   image: <YOUR MAP NAME>
   resolution: 0.01
   origin: [-15.0, -15.0, 0.0]
   occupied_thresh: 0.65
   free_thresh: 0.196
   negate: 0
   ```
   Note that the origin of the map should correspond to your map's size. For example, the default map size is 30 by 30, so the origin will be [-15, -15, 0], i.e. half the size of the map.