
# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html.
# For more examples on how to use CMake, see https://github.com/android/ndk-samples.

# Sets the minimum CMake version required for this project.
cmake_minimum_required(VERSION 3.22.1)

# Declares the project name. The project name can be accessed via ${ PROJECT_NAME},
# Since this is the top level CMakeLists.txt, the project name is also accessible
# with ${CMAKE_PROJECT_NAME} (both CMake variables are in-sync within the top level
# build script scope).
project("orbslam3")

# Creates and names a library, sets it as either STATIC
# or SHARED, and provides the relative paths to its source code.
# You can define multiple libraries, and CMake builds them for you.
# Gradle automatically packages shared libraries with your APK.
#
# In this top level CMakeLists.txt, ${CMAKE_PROJECT_NAME} is used to define
# the target library name; in the sub-module's CMakeLists.txt, ${PROJECT_NAME}
# is preferred for the same purpose.
#
# In order to load a library into your app from Java/Kotlin, you must call
# System.loadLibrary() and pass the name of the library defined here;
# for GameActivity/NativeActivity derived applications, the same library name must be
# used in the AndroidManifest.xml file.

#set(BOOST_INCLUDE_DIR  ${PROJECT_SOURCE_DIR}/Boost/include/boost-1_49)
#include_directories(${BOOST_INCLUDE_DIR})
#
#add_library(boost_serialization STATIC IMPORTED)
## 设置静态库的实际路径
#set_target_properties(boost_serialization PROPERTIES
#        IMPORTED_LOCATION  ${PROJECT_SOURCE_DIR}/Boost/lib/libboost_serialization-gcc-mt-1_49.a
#)
#
## 设置接口库的包含目录
#target_include_directories(boost_serialization INTERFACE ${BOOST_INCLUDE_DIR})

set(OpenCV_DIR opencv-4.10.0/sdk/native/jni)
find_package(OpenCV REQUIRED)
# 如果找到了 OpenCV 库，打印其版本信息
if (OpenCV_FOUND)
    message(STATUS "OpenCV library found: ${OpenCV_VERSION}")
    message(STATUS "OpenCV include path: ${OpenCV_INCLUDE_DIRS}")
    message(STATUS "OpenCV libraries: ${OpenCV_LIBS}")
else ()
    message(FATAL_ERROR "OpenCV library not found.")
endif ()

include_directories(${OpenCV_INCLUDE_DIRS})
add_subdirectory(eigen-3.4.0)
set(Eigen3_INCLUDE_DIRS eigen-3.4.0)
# 添加 Eigen3 头文件目录
include_directories(${Eigen3_INCLUDE_DIRS})
set(g2o_INCLUDE_DIRS g2o)
include_directories(${g2o_INCLUDE_DIRS})
include_directories(./)
add_subdirectory(g2o)
add_subdirectory(DBoW2)
add_subdirectory(Sophus)
#add_subdirectory(Pangolin-0.9.2)
add_subdirectory(orb_slam3)
add_library(${CMAKE_PROJECT_NAME} SHARED
        # List C/C++ source files with relative paths to this CMakeLists.txt.
        orbslam3.cpp)

# Specifies libraries CMake should link to your target library. You
# can link libraries from various origins, such as libraries defined in this
# build script, prebuilt third-party libraries, or Android system libraries.
target_link_libraries(${CMAKE_PROJECT_NAME}
        # List libraries link to the target library
        android
        log
        ORB_SLAM3
        ${OpenCV_LIBS}
)

add_executable(mono_inertial_euroc mono_inertial_euroc.cc)
target_link_libraries(mono_inertial_euroc
        ORB_SLAM3
        ${OpenCV_LIBS}
        ${EIGEN3_LIBS}
        DBoW2
        sophus
        g2o
#        boost_serialization
)