cmake_minimum_required(VERSION 3.8)
project(hector_pose_estimation)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_components REQUIRED)
find_package(hector_pose_estimation_core REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(message_filters REQUIRED)

find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)




## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

#######################################
## Declare ROS messages and services ##
#######################################

## Generate messages in the 'msg' folder
# add_message_files(
#   FILES
# )

## Generate services in the 'srv' folder
# add_service_files(
#   FILES
# )

## Generate added messages and services with any dependencies listed here
# generate_messages(
#   DEPENDENCIES
# )



###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(include)


add_library(hector_pose_estimation_node
  src/pose_estimation_node.cpp
)

ament_target_dependencies(hector_pose_estimation_node
  rclcpp
  hector_pose_estimation_core
  sensor_msgs
  geometry_msgs
  nav_msgs
  message_filters
  tf2
  tf2_ros
  tf2_geometry_msgs
)

# Find hector_timing and use it, if available
find_package(hector_timing QUIET)
if(hector_timing_FOUND)
  ament_target_dependencies(hector_pose_estimation_node hector_timing)
endif()

add_library(hector_pose_estimation_nodelet src/pose_estimation_nodelet.cpp)
target_link_libraries(hector_pose_estimation_nodelet hector_pose_estimation_node)
ament_target_dependencies(hector_pose_estimation_nodelet rclcpp rclcpp_components)

add_executable(pose_estimation src/main.cpp)
target_link_libraries(pose_estimation hector_pose_estimation_node)
ament_target_dependencies(pose_estimation rclcpp)

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executables and/or libraries for installation
install(TARGETS hector_pose_estimation_nodelet hector_pose_estimation_node pose_estimation
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib
)

## Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE
)

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION share/${PROJECT_NAME}
# )

install(DIRECTORY
  launch
  rviz_cfg
  DESTINATION share/${PROJECT_NAME}
)

ament_export_include_directories(include)
ament_export_libraries(hector_pose_estimation_node hector_pose_estimation_nodelet)
ament_export_dependencies(
  rclcpp
  rclcpp_components
  hector_pose_estimation_core
  sensor_msgs
  geometry_msgs
  nav_msgs
  message_filters
  tf2
  tf2_ros
  tf2_geometry_msgs
)

ament_package()
