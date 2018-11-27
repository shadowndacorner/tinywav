cmake_minimum_required(VERSION 3.2 FATAL_ERROR)
project(tinywav VERSION 0.1 LANGUAGES CXX)
option(TW_WITH_SAMPLE "Build sample application" on)

set(TW_ROOT_DIR ${CMAKE_CURRENT_SOURCE_DIR})
set(TW_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include)

# Expose target for subdirectory inclusion
add_library(tinywav INTERFACE)
target_include_directories(tinywav INTERFACE ${TW_INCLUDE_DIR})

if (${TW_WITH_SAMPLE})
    add_executable(tinywav_sample ${TW_ROOT_DIR}/sample/twsample.cpp ${TW_INCLUDE_DIR}/tinywav.h)
    target_link_libraries(tinywav_sample tinywav)
endif()