cmake_minimum_required(VERSION 3.20)
if(POLICY CMP0167)
  cmake_policy(SET CMP0167 NEW)
endif()
project(crane_x7_examples)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++20
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 20)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(image_geometry REQUIRED)
find_package(image_transport REQUIRED)
find_package(message_filters REQUIRED)
find_package(moveit_ros_planning_interface REQUIRED)
find_package(OpenCV REQUIRED COMPONENTS core)
find_package(jsoncpp REQUIRED)
find_package(VTK REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(PCL REQUIRED)
find_package(rclcpp REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)

# Build and install node executables
set(executable_list
  gripper_control
  cartesian_path
  pose_groupstate
  joint_values
  pick_and_place
  pick_and_place_tf
  aruco_detection
  color_detection
  point_cloud_detection
)
foreach(loop_var IN LISTS executable_list)
  add_executable(${loop_var} src/${loop_var}.cpp)
  target_include_directories(${loop_var} PRIVATE
    ${OpenCV_INCLUDE_DIRS}
    ${pcl_ros_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
  )
  target_link_libraries(${loop_var} PRIVATE
    ${OpenCV_LIBRARIES}
    ${PCL_LIBRARIES}
    ${pcl_ros_LIBRARIES}
    cv_bridge::cv_bridge
    geometry_msgs::geometry_msgs
    image_geometry::image_geometry
    image_transport::image_transport
    message_filters::message_filters
    moveit_ros_planning_interface::moveit_ros_planning_interface
    pcl_conversions::pcl_conversions
    rclcpp::rclcpp
    tf2_geometry_msgs::tf2_geometry_msgs
  )

  install(TARGETS
    ${loop_var}
    DESTINATION lib/${PROJECT_NAME}
  )
endforeach()

install(DIRECTORY
  launch
  DESTINATION share/${PROJECT_NAME}/
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
