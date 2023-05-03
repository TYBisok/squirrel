cmake_minimum_required(VERSION 3.14) 
set(CMAKE_EXPORT_COMPILE_COMMANDS ON) # Generate compile_commands.json.
set(BUILD_SHARED_LIBS OFF) # We expect external libraries to be linked statically.
set(CMAKE_CXX_STANDARD 17) # Compile as C++17.
set(CMAKE_CXX_STANDARD_REQUIRED ON) # Require C++17 support.

# Specify the default compiler
# SET(CMAKE_C_COMPILER /usr/bin/clang-12)
# SET(CMAKE_CXX_COMPILER /usr/bin/clang++-12)

project(
    Dummy
    VERSION 0.1.0
    DESCRIPTION "Modern C++ project template"
    LANGUAGES C CXX
)

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message(STATUS "Setting build type to `Debug` as none was specified.")
    set(CMAKE_BUILD_TYPE "Debug")
endif()

file(TO_CMAKE_PATH "${PROJECT_BINARY_DIR}/CMakeLists.txt" PATH_TO_CMAKELISTS_TXT)

if(EXISTS "${PATH_TO_CMAKELISTS_TXT}")
    message(FATAL_ERROR "Run CMake from a build subdirectory! \"mkdir build ; cd build ; cmake ..\" \
    Some junk files were created in this folder (CMakeCache.txt, CMakeFiles); you should delete those.")
endif()

message(STATUS "You're using ${CMAKE_CXX_COMPILER_ID} ${CMAKE_CXX_COMPILER_VERSION}")

# #####################################################################################################################
# DEPENDENCIES
# #####################################################################################################################


# CTest
enable_testing()

# clang-format

# clang-tidy

# #####################################################################################################################
# COMPILER SETUP
# #####################################################################################################################
if(NOT DEFINED DUMMY_SANITIZER)
    set(DUMMY_SANITIZER address)
endif()

message("Build mode: ${CMAKE_BUILD_TYPE}")
message("${DUMMY_SANITIZER} sanitizer will be enabled in debug mode.")

# Compiler flags.
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall -Wextra -Werror")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wno-unused-parameter -Wno-attributes") # TODO: remove
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -O0 -ggdb -fsanitize=${BUSTUB_SANITIZER} -fno-omit-frame-pointer -fno-optimize-sibling-calls")
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

message(STATUS "CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")
message(STATUS "CMAKE_CXX_FLAGS_DEBUG: ${CMAKE_CXX_FLAGS_DEBUG}")
message(STATUS "CMAKE_EXE_LINKER_FLAGS: ${CMAKE_EXE_LINKER_FLAGS}")
message(STATUS "CMAKE_SHARED_LINKER_FLAGS: ${CMAKE_SHARED_LINKER_FLAGS}")

# Output directory.
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Includes.
set(BUSTUB_SRC_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/src/include)
set(BUSTUB_TEST_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/test/include)
set(BUSTUB_THIRD_PARTY_INCLUDE_DIR
        ${PROJECT_SOURCE_DIR}/third_party
)

include_directories(${BUSTUB_SRC_INCLUDE_DIR} ${BUSTUB_TEST_INCLUDE_DIR} ${BUSTUB_THIRD_PARTY_INCLUDE_DIR})
include_directories(BEFORE src) # This is needed for gtest.

# #####################################################################################################################
# Other CMake modules
# MUST BE ADDED AFTER CONFIGURING COMPILER PARAMETERS
# #####################################################################################################################
add_subdirectory(src)
add_subdirectory(test)
add_subdirectory(third_party)
# add_subdirectory(tools)

# #####################################################################################################################
# MAKE TARGETS
# #####################################################################################################################