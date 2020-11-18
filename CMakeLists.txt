cmake_minimum_required(VERSION 3.1.0)

set (CMAKE_CXX_STANDARD 14)

include(CheckCCompilerFlag)
include(CheckCSourceCompiles)

# Set a default build type if none was specified
set(default_build_type "Release")

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message(STATUS "Setting build type to '${default_build_type}' as none was specified.")
    set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE
        STRING "Choose the type of build." FORCE)
    # Set the possible values of build type for cmake-gui
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
        "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
#else()
#    message(STATUS "Build type set to '${CMAKE_BUILD_TYPE}'")
endif()

#add_subdirectory(3rdparty/Ransac)

ENABLE_TESTING()

find_package (Threads REQUIRED)
list(APPEND LIBS ${CMAKE_THREAD_LIBS_INIT})

find_package(OpenCV 4 REQUIRED)
if (OPENCV_FOUND)
message(STATUS "Found OpenCV ${OpenCV_VERSION_MAJOR}: ${OpenCV_DIR}")
endif()
include_directories(SYSTEM ${OpenCV_INCLUDE_DIRS})
#list(APPEND LIBS ${OpenCV_LIBRARIES})

FIND_PACKAGE(OpenMP)
if (OPENMP_FOUND)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
  SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
endif (OPENMP_FOUND)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/OpenQR/Application)

set(OPENQR_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/OpenQR/Application/OpenQR.cpp)

add_library(openqr ${OPENQR_SOURCES})
TARGET_LINK_LIBRARIES(openqr ${OpenCV_LIBRARIES})

add_executable(test_openqr ${CMAKE_CURRENT_SOURCE_DIR}/OpenQR/Application/main.cpp)
TARGET_LINK_LIBRARIES(test_openqr ${OpenCV_LIBRARIES} openqr)
