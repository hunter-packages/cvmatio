# MatlabIO CMakeLists.txt
cmake_minimum_required(VERSION 3.0)

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.10.6.tar.gz"
    SHA1 "59cae8be6506e65a6c756dd42fc0eab128b05eda"
)

project(cvmatio VERSION 1.0.18)

# -----------------------------------------------
# USER-DEFINED VARIABLES
option(BUILD_EXECUTABLE "Build as executable to test functionality" OFF)
option(BUILD_DOC        "Build documentation with Doxygen"          ON)

# --------------------
# CATKIN
set(BUILD_CATKIN OFF)
if (catkin_FOUND)
    set(BUILD_CATKIN ON)
    if (${catkin_VERSION} VERSION_GREATER "0.5.28")
        catkin_package(INCLUDE_DIRS include)
    else()
    catkin_stack()
    catkin_project(cvmatio
                   DEPENDS Boost OpenCV
                   INCLUDE_DIRS include
                   LIBRARIES cvmatio
    )
    endif()
endif()

# --------------------
# COMMON

# include all warning types
if(NOT MSVC)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wno-unused-parameter -Werror -pedantic")
endif()

# Disable Clang specific error
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wshorten-64-to-32")
endif()

set(CMAKE_BUILD_TYPE Release CACHE STRING
        "Choose the type of build. Options are: None, Debug, Release, RelWithDebInfo, MinSizeRel")

if(ANDROID) ### Temporary hack for OpenCV Android builds
  list(APPEND CMAKE_MODULE_PATH "${HUNTER_TOOLCHAIN_ID_PATH}/Install/sdk/native/jni")
  set(OpenCV_DIR "${HUNTER_TOOLCHAIN_ID_PATH}/Install/sdk/native/jni") 
  message("OpenCV_DIR ${OpenCV_DIR}")
  message("CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH}")
endif()

hunter_add_package(OpenCV)
hunter_add_package(Boost)

find_package(Boost CONFIG REQUIRED)
find_package(OpenCV REQUIRED HINTS ${OpenCV_DIR})

# include the dependencies
message( "CVMATIO OPENCV: ${OpenCV_INCLUDE_DIRS}" )
message( "CVMATIO Boost: ${Boost_INCLUDE_DIRS}" )
message( "CVMATIO ZLIB: ${ZLIB_INCLUDE_DIRS}" )

include_directories("include")
include_directories("${OpenCV_INCLUDE_DIRS}")

# build the project
add_subdirectory(src)

# build the doxygen pdf
if(BUILD_DOC)
    add_subdirectory(doc)
endif()

# configuration summary
message("")
message("---------------------------------------------")
message(${PROJECT_NAME} " Configuration Summary")
message("")
message("Building with ECTO/Catkin:     ${BUILD_CATKIN}")
message("Build as executable:           ${BUILD_EXECUTABLE}")
message("Build with documentation:      ${BUILD_DOC}")
message("")
message("---------------------------------------------")
