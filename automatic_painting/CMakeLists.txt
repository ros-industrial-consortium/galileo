cmake_minimum_required(VERSION 2.8.3)
project(automatic_painting)

find_package(catkin REQUIRED COMPONENTS
  image_transport
  roscpp
  sensor_msgs	
  std_msgs
  geometry_msgs 
  tf
  pcl_ros
  pcl_conversions
  image_geometry
)

# find Orocos kdl
find_package(orocos_kdl REQUIRED)

find_package(PCL REQUIRED)

#find_package(OpenCV REQUIRED)

# find Openni
find_package(PkgConfig)
include(FindPkgConfig)
pkg_check_modules(OpenNi REQUIRED libopenni)

include_directories(
  ${catkin_INCLUDE_DIR}
  ${OpenNi_INCLUDE_DIR}
  ${Nite_INCLUDE_DIR}
  ${orocos_kdl_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
)

link_directories(
  ${catkin_LIBRARY_DIRS}
  ${orocos_kdl_LIBRARY_DIRS}
  ${OpenNi_LIBRARIES}
  ${PCL_LIBRARY_DIRS}
)

add_definitions(${PCL_DEFINITIONS})
#
set(ROS_BUILD_TYPE Release)
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

#rosbuild_genmsg()

add_message_files(
  FILES
  Skeleton.msg
  SkeletonJoint.msg
  Skeletons.msg
  Features.msg
)

generate_messages(DEPENDENCIES 
  geometry_msgs std_msgs 
)

catkin_package(
# INCLUDE_DIRS include
# LIBRARIES automatic_painting
  DEPENDS system_lib
  CATKIN_DEPENDS 
    image_transport 
    roscpp 
    sensor_msgs 
    std_msgs
    geometry_msgs
    tf
    pcl_ros
    pcl_conversions
    image_geometry
)

## Declare a cpp executable
include_directories(${OPENNI_INCLUDEDIR} /usr/include/nite)
add_executable(extract_features_node src/extract_features_node.cpp)
target_link_libraries(extract_features_node 
  ${catkin_LIBRARIES} 
  ${OpenNI_LIBRARIES}
  ${orocos_kdl_LIBRARIES}
  ${PCL_LIBRARIES}
)

add_executable(point_cloud_node src/point_cloud_node.cpp)
target_link_libraries(point_cloud_node  
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES}
)

add_executable(point_cloud_node2 src/point_cloud_node2.cpp)
target_link_libraries(point_cloud_node2  
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES}
)

add_executable(train_data_node src/train_data_node.cpp)
target_link_libraries(train_data_node  
  ${catkin_LIBRARIES}
  ${OpenCV_LIBS}
)

add_executable(classifier_features_node src/classifier_features_node.cpp)
target_link_libraries(classifier_features_node  
  ${catkin_LIBRARIES}
  ${OpenCV_LIBS}
)

install(TARGETS 
    extract_features_node 
    point_cloud_node point_cloud_node2 
    classifier_features_node
    train_data_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)  

#install(TARGETS train_data_node 
#  LIBRARY DESTINATION ${CMAKE_BINARY_DIR}
#  RUNTIME DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}
#)

## Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE
)
