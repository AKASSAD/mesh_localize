cmake_minimum_required(VERSION 2.8.3)
project(mesh_localize)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules)
SET(CMAKE_CXX_FLAGS "-march=native -std=c++11")
SET(CMAKE_BUILD_TYPE "Release")

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  cmake_modules
  cv_bridge
  gazebo_msgs
  image_transport
#  opencv2
#  pcl_ros
  roscpp
  rospy
  sensor_msgs
  std_msgs
  tf
)

## System dependencies are found with CMake's conventions
# find_package(Boost REQUIRED COMPONENTS system)
find_package(TinyXML REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Eigen REQUIRED)
find_package(PCL REQUIRED)
find_package(OpenMP REQUIRED)
find_package(ObjectRenderer REQUIRED)
find_package(OGRE REQUIRED)
find_package(OIS REQUIRED)
#find_package(GCOP REQUIRED)

message(STATUS "OBJECT_RENDERER_LIBS=${OBJECT_RENDERER_LIBS}")
message(STATUS "OBJECT_RENDERER_INCLUDE_DIR=${OBJECT_RENDERER_INCLUDE_DIR}")

#set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")

## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependencies might have been
##     pulled in transitively but can be declared for certainty nonetheless:
##     * add a build_depend tag for "message_generation"
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

## Generate messages in the 'msg' folder
# add_message_files(
#   FILES
#   Message1.msg
#   Message2.msg
# )

## Generate services in the 'srv' folder
# add_service_files(
#   FILES
#   Service1.srv
#   Service2.srv
# )

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here
# generate_messages(
#   DEPENDENCIES
#   gazebo_msgs#   sensor_msgs#   std_msgs
# )

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  INCLUDE_DIRS include ${Eigen_INCLUDE_DIRS} ${TinyXML_INCLUDE_DIRS} ${PCL_INCLUDE_DIRS} 
               ${OBJECT_RENDERER_INCLUDE_DIRS} #${GCOP_INCLUDE_DIRS}
  LIBRARIES mesh_localize
  CATKIN_DEPENDS cv_bridge gazebo_msgs image_transport roscpp rospy sensor_msgs std_msgs tf
  DEPENDS TinyXML Eigen OpenCV 
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
# include_directories(include)
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${Eigen_INCLUDE_DIRS} 
  ${TinyXML_INCLUDE_DIRS} 
  ${PCL_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${OBJECT_RENDERER_INCLUDE_DIRS}
  ${OGRE_INCLUDE_DIRS}
  ${OIS_INCLUDE_DIRS}
  #${GCOP_INCLUDE_DIRS}
#  /usr/local/kvld
) 

## Declare a cpp library
add_library(mesh_localize
                                  src/KeyframeContainer.cpp
                                  src/CameraContainer.cpp
                                  src/MeshLocalizer.cpp
                                  src/Common.cpp
                                  src/FindCameraMatrices.cpp
                                  src/Triangulation.cpp
                                  src/KeyframeMatch.cpp
                                  src/MapFeatures.cpp
                                  src/ImageDbUtil.cpp
                                  src/PnPUtil.cpp
                                  src/EdgeTrackingUtil.cpp
                                  src/OgreImageGenerator.cpp
                                  src/PointCloudImageGenerator.cpp
                                  src/KLTTracker.cpp
                                  src/FeatureMatchLocalizer.cpp
                                  src/DepthFeatureMatchLocalizer.cpp
                                  src/FABMAPLocalizer.cpp
                                  #src/IMUMotionModel.cpp
                                  src/ASiftDetector.cpp)

target_link_libraries(mesh_localize
  ${OpenCV_LIBS} 
  ${TinyXML_LIBRARY}
  ${PCL_LIBRARIES}
  ${OpenCV_LIBRARIES}
  ${OBJECT_RENDERER_LIBS}
  #${GCOP_LIBRARY}
#  /usr/local/lib/libkvld.a
#  /usr/local/lib/libOrsa.a
)


## Declare a cpp executable
add_executable(mesh_localize_node src/mesh_localize_node.cpp)
add_executable(render_node src/render_node.cpp)

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
# add_dependencies(mesh_localize_node mesh_localize_generate_messages_cpp)

## Specify libraries to link a library or executable target against


target_link_libraries(mesh_localize_node
   mesh_localize
   ${catkin_LIBRARIES}
)

target_link_libraries(render_node
   mesh_localize
   ${catkin_LIBRARIES}
)
#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS mesh_localize mesh_localize_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_mesh_localize.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
