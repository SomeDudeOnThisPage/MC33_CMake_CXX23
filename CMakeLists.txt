cmake_minimum_required(VERSION 3.20..3.31.5)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(MC33_ORTHOGONAL_GRID "Use orthogonal grids." OFF)
option(MC33_NEGATE_NORMALS  "Exchange front and back surfaces." OFF)

# Option: MC33_DATA_FORMAT
set(EXPECTED_DATA_FORMATS i8 i16 i32 f32 f64)
set(MC33_DATA_FORMAT f32 CACHE STRING "Set data format [i8|i16|i32|f32|f64].")
if(NOT MC33_DATA_FORMAT IN_LIST EXPECTED_DATA_FORMATS)
    message(FATAL_ERROR "invalid MC33_DATA_FORMAT: expected '${EXPECTED_DATA_FORMATS}', got '${MC33_DATA_FORMAT}'")
endif()

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

if(MC33_ORTHOGONAL_GRID)
    target_compile_definitions(MC33 PRIVATE GRD_orthogonal)
endif()

if(MC33_NEGATE_NORMALS)
    target_compile_definitions(MC33 PRIVATE MC_Normal_neg)
endif()

if(MC33_DATA_FORMAT STREQUAL "i8")
    message(STATUS "Compiling MC33 with i8 data type")
    target_compile_definitions(MC33 PRIVATE integer_GRD)
    target_compile_definitions(MC33 PRIVATE GRD_type_size=1)
elseif(MC33_DATA_FORMAT STREQUAL "i16")
    message(STATUS "Compiling MC33 with i16 data type")
    target_compile_definitions(MC33 PRIVATE integer_GRD)
    target_compile_definitions(MC33 PRIVATE GRD_type_size=2)
elseif(MC33_DATA_FORMAT STREQUAL "i32")
    message(STATUS "Compiling MC33 with i32 data type")
    target_compile_definitions(MC33 PRIVATE integer_GRD)
    target_compile_definitions(MC33 PRIVATE GRD_type_size=4)
elseif(MC33_DATA_FORMAT STREQUAL "f32")
    message(STATUS "Compiling MC33 with f32 data type")
    target_compile_definitions(MC33 PRIVATE GRD_type_size=4)
elseif(MC33_DATA_FORMAT STREQUAL "f64")
    message(STATUS "Compiling MC33 with f64 data type")
    target_compile_definitions(MC33 PRIVATE GRD_type_size=8)
else()
    message(FATAL_ERROR "invalid data type: ${MC33_DATA_FORMAT}")
endif()

target_compile_options(MC33 PRIVATE -Ofast -m64 -Wall -Wextra -funroll-loops)
target_compile_features(MC33 PUBLIC cxx_std_23)
target_include_directories(MC33 PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
