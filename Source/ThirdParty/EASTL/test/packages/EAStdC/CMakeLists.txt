#-------------------------------------------------------------------------------------------
# Copyright (C) Electronic Arts Inc.  All rights reserved.
#-------------------------------------------------------------------------------------------
cmake_minimum_required(VERSION 3.1)
project(EAStdC CXX)

#-------------------------------------------------------------------------------------------
# Library definition
#-------------------------------------------------------------------------------------------
file(GLOB EASTDC_SOURCES "source/*.cpp" "include/EAStdC/*.h")
add_library(EAStdC STATIC ${EASTDC_SOURCES})

#-------------------------------------------------------------------------------------------
# Defines
#-------------------------------------------------------------------------------------------
add_definitions(-D_CRT_SECURE_NO_WARNINGS)
add_definitions(-D_SCL_SECURE_NO_WARNINGS)
add_definitions(-D_CHAR16T)

#-------------------------------------------------------------------------------------------
# Compiler Flags
#-------------------------------------------------------------------------------------------
set (CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH};${CMAKE_CURRENT_SOURCE_DIR}/../../../scripts/CMake")
include(CommonCppFlags)

#-------------------------------------------------------------------------------------------
# Include directories
#-------------------------------------------------------------------------------------------
target_include_directories(EAStdC PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)

#-------------------------------------------------------------------------------------------
# Dependencies
#-------------------------------------------------------------------------------------------
target_link_libraries(EAStdC EABase)
target_link_libraries(EAStdC EAAssert)

if (BUILD_SHARED_LIBS)
    target_compile_definitions(EAStdC PUBLIC -DEASTDC_DLL PRIVATE -DEASTDC_EXPORTS)
endif ()
