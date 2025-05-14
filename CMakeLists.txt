cmake_minimum_required(VERSION 3.20..3.31.5)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(MC33
    DESCRIPTION "C++ version based on the Marching Cubes library of the paper: http://jcgt.org/published/0008/03/01"
    LANGUAGES CXX
)

add_library(MC33 STATIC
    source/grid3d.cpp
    source/MC33_LookUpTable.h
    source/MC33.cpp
    source/surface.cpp
)

add_library(MC33::MC33 ALIAS MC33)

target_compile_options(MC33 PRIVATE -Ofast -m64 -Wall -Wextra -funroll-loops)
target_compile_features(MC33 PUBLIC cxx_std_11)
target_include_directories(MC33 PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
