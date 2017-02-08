To properly build this example with the generated PolySync messages in ROS, you will first need to set up your catkin workspace with the ros_bridge.

If you do not have a catkin workspace already, follow these steps to set one up.

http://wiki.ros.org/catkin/Tutorials/create_a_workspace

If you do have a workspace feel free to continue with the steps below. First source your ROS install and then create a package in the src directory of your catkin workspace as shown below.

    $ cd ~/catkin_ws/src
    $ source /opt/ros/indigo/setup.bash
    $ catkin_create_pkg polysync_lidar_translator std_msgs roscpp tf

Copy "polysync_lidar_translator_node.cpp" to the src directory of the polysync_lidar_translator package you created.

    $ cp PolySync-Core-CPP-Examples/PolysyncLidarTranslator/polysync_lidar_translator_node.cpp ~/catkin_ws/src/polysync_lidar_translator/src/

Make sure that in the "Build" section of the CMakeLists file for your PolysyncLidarTranslator project are uncommented. All other lines in the "Build" section should be commented.

    ## Declare a C++ executable
    add_executable(polysync_lidar_translator_node src/polysync_lidar_translator_node.cpp)

    ## Add cmake target dependencies of the executable
    ## same as for the library above
    add_dependencies(polysync_lidar_translator_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

    ## Specify libraries to link a library or executable target against
    target_link_libraries(polysync_lidar_translator_node
       ${catkin_LIBRARIES}
     )

Generate the ros_bridge files in the src folder of your catkin workspace and copy the ros_bridge project into the root directory of your catkin workspace; as shown below.

    $ cd ~/catkin_ws/src
    $ pdm-gen -r /usr/local/polysync/modules/sensor/sensor.idl /usr/local/polysync/modules/navigation/navigation.idl /usr/local/polysync/modules/control/control.idl /usr/local/polysync/modules/dtc/dtc.idl
    $ mv pdm/ros_bridge ../
    $ rm -rf pdm

Source the ros_bridge, and then run catkin_make in the root directory of your catkin workspace.

    $ cd ~/catkin_ws
    $ source ros_bridge/setup.bash
    $ catkin_make

Run ROS bridge node as shown below. (remember to source ros_bridge/setup.bash if using a different terminal)

    $ cd ~/catkin_ws/ros_bridge/share/polysync_ros_bridge
    $ ./BridgeNode -t ps_lidar_points_msg

Start up PolySync data generator in another terminal.

    $ polysync-data-generator-c

Run the translator project you just built. The executable should be in the devel folder of your catkin workspace.

    $ cd ~/catkin_ws/devel/lib/polysync_lidar_translator
    $ ./polysync_lidar_translator_node

Open up rviz.

    $ rosrun rviz rviz

In "Global Options" for the fixed frame select "world". Then hit "add" and add a new "PointCloud2" visualization. For the topic of this new visualization select "/polysync/lidar_points". You can change the style to "Points" to have a little more realistic looking lidar point could.