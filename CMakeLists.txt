cmake_minimum_required (VERSION 3.0.0 FATAL_ERROR)

foreach(p
    CMP0020 # CMake 2.8.11
    CMP0022 # CMake 2.8.12
    CMP0025 # CMake 3.0
    CMP0053 # CMake 3.1
    CMP0054 # CMake 3.1
    )
  if(POLICY ${p})
    cmake_policy(SET ${p} NEW)
  endif()
endforeach()

project (singers)

# Project-cmake files
set (singers_CMAKE_DIR "${singers_SOURCE_DIR}/cmake/Modules")
set (CMAKE_MODULE_PATH ${singers_CMAKE_DIR} ${CMAKE_MODULE_PATH})

# Set up our directory structure for output libraries and binaries
if(NOT CMAKE_RUNTIME_OUTPUT_DIRECTORY)
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${singers_BINARY_DIR}/bin")
endif()
if(NOT CMAKE_LIBRARY_OUTPUT_DIRECTORY)
  if(UNIX)
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${singers_BINARY_DIR}/lib")
  else()
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${singers_BINARY_DIR}/bin")
  endif()
endif()
if(NOT CMAKE_ARCHIVE_OUTPUT_DIRECTORY)
  set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${singers_BINARY_DIR}/lib")
endif()
mark_as_advanced(CMAKE_RUNTIME_OUTPUT_DIRECTORY
  CMAKE_LIBRARY_OUTPUT_DIRECTORY
  CMAKE_ARCHIVE_OUTPUT_DIRECTORY
  )

include (DefineVersion)

# ------------------------------------------------------------------------------
# Compilers
# ------------------------------------------------------------------------------
include (ConfigureCompiler)

# ------------------------------------------------------------------------------
# Testing - must be before any targets are added
# ------------------------------------------------------------------------------
option (ENABLE_TESTING "Enable testing modules" ON)
if (ENABLE_TESTING)
  enable_testing ()
  include (EnableCxxTestingMacros)
endif ()

# ------------------------------------------------------------------------------
# Modules & executables
# ------------------------------------------------------------------------------
# Build shared libraries by default
set (BUILD_SHARED_LIBS ON)
include (SingersModuleMacros)

# ------------------------------------------------------------------------------
# Framework
# ------------------------------------------------------------------------------
add_subdirectory (singers/datamodel)

# ------------------------------------------------------------------------------
# Qt
# ------------------------------------------------------------------------------
add_subdirectory (qt)

# ------------------------------------------------------------------------------
# Static checks
# ------------------------------------------------------------------------------
add_subdirectory (devtools/static-analysis/cppcheck)

# ------------------------------------------------------------------------------
# Documentation
# ------------------------------------------------------------------------------
add_subdirectory (docs/developer/doxygen)
