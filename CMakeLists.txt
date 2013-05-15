cmake_minimum_required(VERSION 2.8.3)
project(youbot_driver)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
########### ROS ###########
find_package(catkin REQUIRED COMPONENTS rosconsole)
find_package(Boost REQUIRED COMPONENTS thread date_time filesystem system regex)

###################################################
## Declare things to be passed to other projects ##
###################################################

## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  LIBRARIES YouBotDriver soem
  CATKIN_DEPENDS rosconsole
)

###########
## Build ##
###########

## Specify additional locations of header files
include_directories(
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
  ${PROJECT_SOURCE_DIR}/include
  ${PROJECT_SOURCE_DIR}/include/soem
  ${PROJECT_SOURCE_DIR}/include/generic
  ${PROJECT_SOURCE_DIR}/include/base-kinematic
  ${PROJECT_SOURCE_DIR}/include/generic-joint
  ${PROJECT_SOURCE_DIR}/include/testing
  ${PROJECT_SOURCE_DIR}/include/one-dof-gripper
  ${PROJECT_SOURCE_DIR}/include/youbot
  ${PROJECT_SOURCE_DIR}/include/generic-gripper
)

link_directories(
  ${catkin_LIBRARY_DIRS}
)

add_definitions(-DUSE_ROS_LOGGING)
set(CMAKE_BUILD_TYPE Release)

## Simple Open EtherCAT Master Library
add_subdirectory(src/soem)

## Main youBot Driver
set(YOUBOT_DRIVER_SRC
  ${PROJECT_SOURCE_DIR}/src/youbot/EthercatMaster.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/EthercatMasterWithThread.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/EthercatMasterWithoutThread.cpp
  ${PROJECT_SOURCE_DIR}/src/generic/Logger.cpp
  ${PROJECT_SOURCE_DIR}/src/generic/ConfigFile.cpp
  ${PROJECT_SOURCE_DIR}/src/generic/PidController.cpp
  ${PROJECT_SOURCE_DIR}/src/generic-joint/JointData.cpp
  ${PROJECT_SOURCE_DIR}/src/generic-joint/JointTrajectory.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/YouBotJoint.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/JointLimitMonitor.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/YouBotBase.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/YouBotManipulator.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/YouBotGripper.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/YouBotGripperBar.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/YouBotGripperParameter.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/DataTrace.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/GripperDataTrace.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/YouBotJointParameter.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/YouBotJointParameterReadOnly.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/YouBotJointParameterPasswordProtected.cpp
  ${PROJECT_SOURCE_DIR}/src/youbot/JointTrajectoryController.cpp
  ${PROJECT_SOURCE_DIR}/src/base-kinematic/FourSwedishWheelOmniBaseKinematic.cpp
  ${PROJECT_SOURCE_DIR}/src/base-kinematic/FourSwedishWheelOmniBaseKinematicConfiguration.cpp
)

add_library(YouBotDriver ${YOUBOT_DRIVER_SRC})
## Specify libraries to link a library or executable target against
target_link_libraries(YouBotDriver soem ${catkin_LIBRARIES} ${Boost_LIBRARIES})

## Test Program
add_subdirectory(src/testing)

## youBot display example application
add_subdirectory(src/display)

#############
## Install ##
#############

## Mark executables and/or libraries for installation
install(TARGETS YouBotDriver
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

#copy header files
install(DIRECTORY include/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.hpp" PATTERN "*.h"
)

## Copy config files
install(DIRECTORY config
  DESTINATION ${CATKIN_PACKAGE_ETC_DESTINATION}
) 
