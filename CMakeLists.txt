cmake_minimum_required(VERSION 3.8)
project(fall_detection)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# set(TARGET yolov8n_lite_arm_cpu)

include_directories(${OpenCV_INCLUDE_DIRS})

# 1. path to Paddle-Lite lib
set(LITE_DIR "/home/firefly/code/download/inference_lite_lib.armlinux.armv8.gcc.with_extra.with_cv/cxx")
set(LITE_DIR_INC ${LITE_DIR}/include)
set(LITE_DIR_LIB /home/firefly/code/download/inference_lite_lib.armlinux.armv8.gcc.with_extra.with_cv/cxx/lib/libpaddle_light_api_shared.so)
link_directories(${LITE_DIR}/lib)
include_directories(${LITE_DIR}/include)
# include_directories(/home/firefly/code/download/inference_lite_lib.armlinux.armv8.gcc.with_extra.with_cv/cxx/include/include)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules")

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(image_transport REQUIRED)

#set opencv lib
find_package(OpenCV REQUIRED)

#find json lib
find_package(JsonCpp REQUIRED)
include_directories(${JSONCPP_INCLUDE_DIRS})



# target_include_directories(example_interfaces_robot_01 PUBLIC
#   $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
#   $<BUILD_INTERFACE:${LITE_DIR}/include>
#   $<INSTALL_INTERFACE:include>
  
#   )


add_executable(fall_detection src/detection.cpp)
ament_target_dependencies(fall_detection
  rclcpp
  sensor_msgs
  cv_bridge
  image_transport
)



#添加消息文件和依赖
# rosidl_generate_interfaces(${PROJECT_NAME}
#   "msg/Novel.msg"
#    DEPENDENCIES rclcpp sensor_msgs cv_bridge
#  )

install(TARGETS fall_detection
  DESTINATION lib/${PROJECT_NAME}
)

target_link_libraries(fall_detection ${LITE_DIR_LIB} ${JSONCPP_LIBRARIES} paho-mqttpp3 paho-mqtt3as)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
