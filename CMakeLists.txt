cmake_minimum_required(VERSION 3.28)

project(IC-addin-zlib
    DESCRIPTION "1C NativeApi addin for zlib's inflate-deflate."
    VERSION 0.1.0
    LANGUAGES CXX)

include(cmake/set_if_undefined.cmake)
include(cmake/enable_addr_sanitizer.cmake)
include(cmake/enable_thread_sanitizer.cmake)

option(IC_ADDIN_ZLIB_BUILD_TESTS "Build tests" OFF)
option(IC_ADDIN_ZLIB_TIDY "run clang-tidy" OFF)
option(IC_ADDIN_ZLIB_SANITIZE_ADDRESS "Enable address sanitizer in tests" OFF)
option(IC_ADDIN_ZLIB_SANITIZE_THREAD "Enable thread sanitizer in tests" OFF)

if(NOT DEFINED CMAKE_BUILD_TYPE AND NOT DEFINED CMAKE_CONFIGURATION_TYPES)
    set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

set_if_undefined(CMAKE_CXX_VISIBILITY_PRESET hidden)
set_if_undefined(CMAKE_VISIBILITY_INLINES_HIDDEN ON)

if (IC_ADDIN_ZLIB_TIDY)
    set(CMAKE_CXX_CLANG_TIDY "clang-tidy")
    set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
    message(STATUS "Enabled clang-tidy")
endif ()

if (IC_ADDIN_ZLIB_SANITIZE_ADDRESS AND IC_ADDIN_ZLIB_SANITIZE_THREAD)
    message(FATAL_ERROR "IC_ADDIN_ZLIB_SANITIZE_ADDRESS and IC_ADDIN_ZLIB_SANITIZE_THREAD are mutually exclusive")
endif ()

find_package(ZLIB REQUIRED)

add_library(IC-addin-zlib)
add_library(IC-addin-zlib::IC-addin-zlib ALIAS IC-addin-zlib)



if(IC_ADDIN_ZLIB_BUILD_TESTS)
    if (IC_ADDIN_ZLIB_SANITIZE_ADDRESS)
        enable_addr_sanitizer(tplog)
    elseif (IC_ADDIN_ZLIB_SANITIZE_THREAD)
        enable_thread_sanitizer(tplog)
    endif ()


    enable_testing()
    add_subdirectory(tests)
endif()