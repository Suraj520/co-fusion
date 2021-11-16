cmake_minimum_required(VERSION 3.1.0)
project(cofusion)

#set(CMAKE_CXX_STANDARD 14)

include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
        message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()



# Warnings / Errors
add_compile_options(-Wall -Wno-unused-function -Wno-unused-variable
  -Wno-unused-but-set-variable -Wno-unused-but-set-variable -Wno-write-strings
  -Wno-deprecated -Wno-deprecated-declarations -Werror -Wno-unknown-pragmas)

# Don't follow symlinks when FILE GLOB_RECURSE (and don't warn)
cmake_policy(SET CMP0009 NEW)

set(BUILD_TESTS OFF CACHE BOOL "Build unit tests")

# OpenMP
find_package(OpenMP)
if (OPENMP_FOUND)
    set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()

# boost
#find_package(Boost REQUIRED filesystem)
#message(STATUS "Boost_INCLUDE_DIRS: ${Boost_INCLUDE_DIRS}")
#include_directories(${Boost_INCLUDE_DIRS})
#link_directories(${Boost_LIBRARY_DIRS})
SET (BOOST_ROOT "/opt/boost/boost_1_65_1")
SET (BOOST_INCLUDEDIR "/opt/boost/boost-1.65.1/include")
SET (BOOST_LIBRARYDIR "/opt/boost/boost-1.65.1/lib")

SET (BOOST_MIN_VERSION "1.55.0")
set (Boost_NO_BOOST_CMAKE ON)
FIND_PACKAGE(Boost ${BOOST_MIN_VERSION} REQUIRED)
if (NOT Boost_FOUND)
  message(FATAL_ERROR "Fatal error: Boost (version >= 1.55) required.")
else()
  message(STATUS "Setting up BOOST")
  message(STATUS " Includes - ${Boost_INCLUDE_DIRS}")
  message(STATUS " Library  - ${Boost_LIBRARY_DIRS}")
  include_directories(${Boost_INCLUDE_DIRS})
  link_directories(${Boost_LIBRARY_DIRS})
endif (NOT Boost_FOUND)




# gSLICr
#find_package(gSLICr REQUIRED)
#replacing with its directory location
set(gSLICr_DIR /opt/gSLICR/build/)

# Pangolin
find_package(Pangolin 0.5 REQUIRED)
include_directories(${Pangolin_INCLUDE_DIRS})

# Eigen3
find_package (Eigen3 REQUIRED NO_MODULE)

# project-wide paths
include_directories(${PROJECT_SOURCE_DIR})

# -- CORE
add_subdirectory(Core)

# -- GUI
add_subdirectory(GUI)

# -- show all files
file(GLOB_RECURSE showfiles Core/* GUI/*)
add_custom_target(showthings SOURCES ${showfiles})

# export library
install(EXPORT cofusion
    DESTINATION share/${PROJECT_NAME}/cmake
    FILE ${PROJECT_NAME}Config.cmake
)
