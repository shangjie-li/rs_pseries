cmake_minimum_required(VERSION 3.5)
project(rs_sdk)
set(COMPILE_LOCALIZATION TRUE) #User can modify this flag to determinate whether to compile localization module or not
set(COMPILE_PERCEPTION TRUE) #User can modify this flag to determinate whether to compile perception module or not

#========================
# Subdirectory
#========================
add_subdirectory(module/rs_common)
add_subdirectory(module/rs_lidar)
add_subdirectory(module/rs_sensor)
add_subdirectory(module/rs_preprocessing)
if(${COMPILE_LOCALIZATION})
add_definitions(-DCOMPILE_LOCALIZATION)
add_subdirectory(module/rs_localization)
endif(${COMPILE_LOCALIZATION})
if(${COMPILE_PERCEPTION})
add_definitions(-DCOMPILE_PERCEPTION)
add_subdirectory(module/rs_perception)
endif(${COMPILE_PERCEPTION})
include(CheckSymbolExists)
#========================
# Project details / setup
#========================
set(PROJECT_NAME rs_sdk)
string(TOUPPER ${PROJECT_NAME} PROJECT_NAME_UPPER)
string(TOLOWER ${PROJECT_NAME} PROJECT_NAME_LOWER)
#========================
# debug
#========================
add_definitions(-DDEBUG_LEVEL=0)
add_definitions(-DPROJECT_PATH="${PROJECT_SOURCE_DIR}")
# The version number.
set (PROJECT_VERSION_MAJOR 2)
set (PROJECT_VERSION_MINOR 0)
set (PROJECT_VERSION_PATCH 0)
set (PROJECT_VERSION ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH})

cmake_policy(SET CMP0048 NEW)
project(${PROJECT_NAME} VERSION ${PROJECT_VERSION})

set(CMAKE_BUILD_TYPE Release)
add_definitions(-O3)
add_definitions(-std=c++11)
add_compile_options(-W)

# configure a header file to pass some of the CMake settings
# to the source code
configure_file (
  "${PROJECT_SOURCE_DIR}/version.h.in"
  "${PROJECT_SOURCE_DIR}/include/version.h"
)

#========================
# Dependencies Setup
#========================
set(EXTERNAL_LIBS "")
find_package(tf QUIET)
find_package(cv_bridge QUIET)
find_package(roscpp 1.12 QUIET COMPONENTS)
if(roscpp_FOUND AND tf_FOUND)
  message(=============================================================)
  message("-- ROS Found, Ros Support is turned On!")
  message(=============================================================)
  add_definitions(-DROS_FOUND)
  include_directories(${roscpp_INCLUDE_DIRS})
  include_directories(${tf_INCLUDE_DIRS})
  include_directories(${cv_bridge_INCLUDE_DIRS})
  set(EXTERNAL_LIBS ${EXTERNAL_LIBS} ${roscpp_LIBRARIES} ${tf_LIBRARIES} ${cv_bridge_LIBRARIES})
else(roscpp_FOUND AND tf_FOUND)
  message(=============================================================)
  message("-- ROS Not Found, Ros Support is turned Off!")
  message(=============================================================)
endif(roscpp_FOUND AND tf_FOUND)

find_package(Protobuf QUIET)
if(Protobuf_FOUND)
  message(=============================================================)
  message("-- Protobuf Found, Protobuf Support is turned On!")
  message(=============================================================)
  add_definitions(-DPROTO_FOUND)
  include_directories(${PROTOBUF_INCLUDE_DIRS})
  set(EXTERNAL_LIBS ${EXTERNAL_LIBS} ${PROTOBUF_LIBRARY})
else(Protobuf_FOUND)
  message(=============================================================)
  message("-- Protobuf Not Found, Protobuf Support is turned Off!")
  message(=============================================================)
endif(Protobuf_FOUND)

#========================
# Build Setup
#========================
add_executable(rs_sdk_demo
            src/rs_sdk_demo_node.cpp
            src/rs_sdk_demo.cpp
            )
add_dependencies(rs_sdk_demo rs_sensormanager rs_preprocessing)
if(${COMPILE_PERCEPTION})
add_dependencies(rs_sdk_demo ${RS_PERCEPTION_LIBRARY})
endif(${COMPILE_PERCEPTION})
if(${COMPILE_LOCALIZATION})
add_dependencies(rs_sdk_demo rs_localization)
endif(${COMPILE_LOCALIZATION})
target_link_libraries(rs_sdk_demo
                    rs_sensormanager
                    rs_preprocessing       
                    ${roscpp_LIBRARIES}
                    ${PROTOBUF_LIBRARY}
                    ${tf_LIBRARIES}   
                    ${cv_bridge_LIBRARIES}        
                       )
if(${COMPILE_LOCALIZATION})
target_link_libraries(rs_sdk_demo rs_localization)
endif(${COMPILE_LOCALIZATION})
if(${COMPILE_PERCEPTION})
target_link_libraries(rs_sdk_demo ${RS_PERCEPTION_LIBRARY})
endif(${COMPILE_PERCEPTION})

target_include_directories(rs_sdk_demo PUBLIC ${PROJECT_SOURCE_DIR}/include)    
set_target_properties(rs_sdk_demo PROPERTIES 
            VERSION ${PROJECT_VERSION}
            SOVERSION ${PROJECT_VERSION_MAJOR}
                      )
