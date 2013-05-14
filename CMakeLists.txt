cmake_minimum_required(VERSION 2.8.3)
project(youbot_driver)

OPTION(USE_ROS "Enable ROS as compile tool" ON)

########### ROS ###########
SET(ROS_ROOT_PATH $ENV{ROS_ROOT})
IF(DEFINED ROS_ROOT_PATH AND USE_ROS MATCHES ON)
	find_package(catkin REQUIRED COMPONENTS rosconsole)
	include_directories(${catkin_INCLUDE_DIRS})
	link_directories(${catkin_LIBRARY_DIRS})
	catkin_package(
		INCLUDE_DIRS ${PROJECT_SOURCE_DIR} ${PROJECT_SOURCE_DIR}/soem/src
		LIBRARIES YouBotDriver soem
		CATKIN_DEPENDS rosconsole
		CFG_EXTRAS config.cmake
	)
	ADD_DEFINITIONS(-DUSE_ROS_LOGGING)
ELSEIF(DEFINED ROS_ROOT_PATH AND USE_ROS MATCHES ON)
	SET(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake-modules")
	SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib) #static libraries
	SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin) #executables
	SET(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib) #dynamic libraries (plugins)
ENDIF(DEFINED ROS_ROOT_PATH AND USE_ROS MATCHES ON)

########### cmake configuration and compiler flags ###########
SET(CMAKE_BUILD_TYPE Release)
SET(CMAKE_C_FLAGS_DEBUG "-fPIC -Wall -g")
SET(CMAKE_CXX_FLAGS_DEBUG "-fPIC -Wall -g")
SET(CMAKE_C_FLAGS_RELEASE "-fPIC -Wall")
SET(CMAKE_CXX_FLAGS_RELEASE "-fPIC -Wall")

# find required and optional dependencies

########### Boost ###########
#SET(Boost_USE_STATIC_LIBS ON)
#SET(Boost_ADDITIONAL_VERSIONS "1.39" "1.39.0" "1.40" "1.40.0" "1.41" "1.41.0" "1.42" "1.42.0")
FIND_PACKAGE(Boost REQUIRED COMPONENTS thread date_time filesystem system regex)
IF (Boost_FOUND)
  LINK_LIBRARIES(boost_thread boost_date_time boost_filesystem boost_system boost_regex)
  MESSAGE(STATUS "SUCCESSFUL: Boost found")
ELSE (Boost_FOUND)
  MESSAGE(STATUS "ERROR: Boost not found")
ENDIF (Boost_FOUND)

########### Doxygen ###########
#SET(DOXYFILE_LATEX "NO")
#SET(DOXYFILE_OUTPUT_DIR ${PROJECT_SOURCE_DIR}/doc)
#include(UseDoxygen)

########### Simple Open EtherCAT Master Library ###########
add_subdirectory(soem)

INCLUDE_DIRECTORIES(
 ${PROJECT_SOURCE_DIR}
 ${PROJECT_SOURCE_DIR}/soem/src
)

########### CppUnit and tests ###########
FIND_PACKAGE(CppUnit)
IF(CppUnit_FOUND)
	MESSAGE(STATUS "SUCCESSFUL: CPP_UNIT found")
  add_subdirectory(testing)
#ELSE(CppUnit_FOUND)
#	MESSAGE(STATUS "WARNING: CPP_UNIT not found")
ENDIF (CppUnit_FOUND)

########### youBot display example application ###########
#add_subdirectory(display)


SET(YOUBOT_DRIVER_SRC
  ${PROJECT_SOURCE_DIR}/youbot/EthercatMaster.cpp
  ${PROJECT_SOURCE_DIR}/youbot/EthercatMasterWithThread.cpp
  ${PROJECT_SOURCE_DIR}/youbot/EthercatMasterWithoutThread.cpp
  ${PROJECT_SOURCE_DIR}/generic/Logger.cpp
  ${PROJECT_SOURCE_DIR}/generic/ConfigFile.cpp
  ${PROJECT_SOURCE_DIR}/generic/PidController.cpp
  ${PROJECT_SOURCE_DIR}/generic-joint/JointData.cpp
  ${PROJECT_SOURCE_DIR}/generic-joint/JointTrajectory.cpp
  ${PROJECT_SOURCE_DIR}/youbot/YouBotJoint.cpp
  ${PROJECT_SOURCE_DIR}/youbot/JointLimitMonitor.cpp
  ${PROJECT_SOURCE_DIR}/youbot/YouBotBase.cpp
  ${PROJECT_SOURCE_DIR}/youbot/YouBotManipulator.cpp
  ${PROJECT_SOURCE_DIR}/youbot/YouBotGripper.cpp
  ${PROJECT_SOURCE_DIR}/youbot/YouBotGripperBar.cpp
  ${PROJECT_SOURCE_DIR}/youbot/YouBotGripperParameter.cpp
  ${PROJECT_SOURCE_DIR}/youbot/DataTrace.cpp
  ${PROJECT_SOURCE_DIR}/youbot/GripperDataTrace.cpp
  ${PROJECT_SOURCE_DIR}/youbot/YouBotJointParameter.cpp
  ${PROJECT_SOURCE_DIR}/youbot/YouBotJointParameterReadOnly.cpp
  ${PROJECT_SOURCE_DIR}/youbot/YouBotJointParameterPasswordProtected.cpp
  ${PROJECT_SOURCE_DIR}/youbot/JointTrajectoryController.cpp
  ${PROJECT_SOURCE_DIR}/base-kinematic/FourSwedishWheelOmniBaseKinematic.cpp
  ${PROJECT_SOURCE_DIR}/base-kinematic/FourSwedishWheelOmniBaseKinematicConfiguration.cpp
)

add_library(YouBotDriver ${YOUBOT_DRIVER_SRC})
target_link_libraries(YouBotDriver soem)


#install binary and lib
install(TARGETS YouBotDriver
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

#copy header files
install(DIRECTORY base-kinematic youbot generic generic-gripper generic-joint one-dof-gripper soem
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.hpp" PATTERN "*.h"
)

#copy config files
install(DIRECTORY config
  DESTINATION ${CATKIN_PACKAGE_ETC_DESTINATION}
)


#ADD_LIBRARY(YouBotDriverShared SHARED "")
#SET_TARGET_PROPERTIES(YouBotDriverShared PROPERTIES OUTPUT_NAME "YouBotDriver")
#SET_TARGET_PROPERTIES(YouBotDriverShared PROPERTIES LINKER_LANGUAGE CXX)
#TARGET_LINK_LIBRARIES(YouBotDriverShared -Wl,-whole-archive YouBotDriver soem -Wl,-no-whole-archive)

#INSTALL(TARGETS YouBotDriver YouBotDriverShared RUNTIME DESTINATION bin LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)


#ADD_EXECUTABLE(YouBot_test
#  ${PROJECT_SOURCE_DIR}/Test_commutation.cpp
#)

#target_link_libraries(YouBot_test YouBotDriver)

