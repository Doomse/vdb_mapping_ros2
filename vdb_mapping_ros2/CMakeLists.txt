cmake_minimum_required(VERSION 3.16.3)
project(vdb_mapping_ros2)

if(NOT CMAKE_CONFIGURATION_TYPES AND NOT CMAKE_BUILD_TYPE)
  message("${PROJECT_NAME}: You did not request a specific build type: selecting 'Release'.")
  set(CMAKE_BUILD_TYPE Release)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(backward_ros REQUIRED)
find_package(rclcpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(std_srvs REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_eigen REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(vdb_mapping REQUIRED)
find_package(vdb_mapping_interfaces REQUIRED)
find_package(visualization_msgs REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(rclcpp_components REQUIRED)

# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)

set(THIS_PACKAGE_INCLUDE_DEPENDS
  rclcpp
  rclcpp_components
  sensor_msgs
  std_msgs
  std_srvs
  tf2
  tf2_eigen
  tf2_ros
  vdb_mapping_interfaces
  visualization_msgs
  pcl_conversions
  backward_ros
)

add_library(${PROJECT_NAME} SHARED
  src/vdb_mapping_ros2_component.cpp
)

target_include_directories(${PROJECT_NAME}
  INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_17)

target_link_libraries(${PROJECT_NAME} PUBLIC
  ${sensor_msgs_TARGETS}
  ${std_msgs_TARGETS}
  ${std_srvs_TARGETS}
  ${vdb_mapping_interfaces_TARGETS}
  ${visualization_msgs_TARGETS}
  pcl_conversions::pcl_conversions
  rclcpp::rclcpp
  rclcpp_components::component
  sensor_msgs::sensor_msgs_library
  tf2::tf2
  tf2_eigen::tf2_eigen
  tf2_ros::tf2_ros
  vdb_mapping::vdb_mapping
)
rclcpp_components_register_nodes(${PROJECT_NAME}
  PLUGIN "vdb_mapping_ros2::vdb_mapping_ros2"
  EXECUTABLE vdb_mapping_ros2_node
)

install(TARGETS
  ${PROJECT_NAME}
  DESTINATION lib
)

install(TARGETS ${PROJECT_NAME}
  EXPORT "export_${PROJECT_NAME}"
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib
  INCLUDES DESTINATION include
)

install(DIRECTORY include/
  DESTINATION include
)

install(DIRECTORY
  launch
  DESTINATION share/${PROJECT_NAME})

install(DIRECTORY
  config
  DESTINATION share/${PROJECT_NAME})


if(BUILD_TESTING)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
endif()

ament_export_dependencies(
  rclcpp
  rclcpp_components
  sensor_msgs
  std_msgs
  std_srvs
  tf2
  tf2_eigen
  tf2_ros
  vdb_mapping_interfaces
  visualization_msgs
  pcl_conversions
  backward_ros
)
ament_export_targets("export_${PROJECT_NAME}")
ament_package()
