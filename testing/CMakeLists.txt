cmake_minimum_required(VERSION 2.8)

ADD_EXECUTABLE(base_arm_gripper_test
  ${PROJECT_SOURCE_DIR}/testing/TestSuite.cpp
  ${PROJECT_SOURCE_DIR}/testing/YouBotBaseTest.cpp
  ${PROJECT_SOURCE_DIR}/testing/YouBotBaseTestWithoutThread.cpp
  ${PROJECT_SOURCE_DIR}/testing/YouBotBaseKinematicsTest.cpp
  ${PROJECT_SOURCE_DIR}/testing/YouBotArmTest.cpp
  ${PROJECT_SOURCE_DIR}/testing/YouBotArmTestWithoutThread.cpp
  ${PROJECT_SOURCE_DIR}/testing/YouBotGripperTest.cpp
)

target_link_libraries(base_arm_gripper_test YouBotDriver cppunit)

## This doesn't work yet.  So you have to manually call:
## $ sudo setcap cap_net_raw+ep base_arm_gripper_test

#add_custom_command(TARGET base_arm_gripper_test POST_BUILD COMMAND sudo setcap cap_net_raw+ep #base_arm_gripper_test WORKING_DIRECTORY ${CATKIN_PACKAGE_BIN_DESTINATION}
#)

INSTALL(TARGETS base_arm_gripper_test RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION} LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION} ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})

