# For more information about build system see
# https://docs.espressif.com/projects/esp-idf/en/latest/api-guides/build-system.html
# The following five lines of boilerplate have to be in your project's
# CMakeLists in this exact order for cmake to work correctly
cmake_minimum_required(VERSION 3.20)

#find_program(CMAKE_CXX_CPPCHECK NAMES cppcheck)
#if(CMAKE_CXX_CPPCHECK)
#	list(APPEND CMAKE_CXX_CPPCHECK "--enable=all")
#endif()

message(STATUS "Clearing cache")
unset(EXTMODS CACHE)
unset(EXTMODS_NAMES CACHE)
unset(EXTMODS_INIT CACHE)
include($ENV{IDF_PATH}/tools/cmake/project.cmake)

# Set the board if it's not already set.
if(DEFINED ENV{MICROPY_BOARD})
    set(board "$ENV{MICROPY_BOARD}")
elseif(DEFINED ENV{BOARD})
    set(board "$ENV{BOARD}")
else()
    set(board DEFAULT)
endif()

if(NOT DEFINED MICROPY_BOARD_DIR)
    cmake_path(SET MICROPY_BOARD_DIR NORMALIZE "${CMAKE_CURRENT_SOURCE_DIR}/boards/${board}")
endif()

cmake_path(SET MICROPY_DIR NORMALIZE "${CMAKE_CURRENT_SOURCE_DIR}/external/micropython")
#cmake_path(RELATIVE_PATH MICROPY_DIR)
cmake_path(SET MICROPY_PORT_DIR NORMALIZE "${MICROPY_DIR}/ports/esp32")

set(EXTRA_COMPONENT_DIRS ./obsolete_components)

# Set the location of this port's directory.

# Set the board directory and check that it exists.
#if(NOT MICROPY_BOARD_DIR)
#    cmake_path(SET MICROPY_BOARD_DIR "${MICROPY_PORT_DIR}/boards/${MICROPY_BOARD}")
#endif()
if(NOT EXISTS ${MICROPY_BOARD_DIR}/mpconfigboard.cmake)
    message(WARNING "Unable to locate board file ${MICROPY_BOARD_DIR}/mpconfigboard.cmake")
    message(FATAL_ERROR "Invalid MICROPY_BOARD specified: ${MICROPY_BOARD}")
endif()

# Include main IDF cmake file.
#include($ENV{IDF_PATH}/tools/cmake/project.cmake)

# Define the output sdkconfig so it goes in the build directory.
set(SDKCONFIG ${CMAKE_BINARY_DIR}/sdkconfig)

# Save the manifest file set from the cmake command line.
set(MICROPY_USER_FROZEN_MANIFEST ${MICROPY_FROZEN_MANIFEST})

# Include board config; this is expected to set SDKCONFIG_DEFAULTS (among other options).
include(${MICROPY_BOARD_DIR}/mpconfigboard.cmake)

# Set the frozen manifest file. Note if MICROPY_FROZEN_MANIFEST is set from the cmake
# command line, then it will override the default and any manifest set by the board.
if (MICROPY_USER_FROZEN_MANIFEST)
    set(MICROPY_FROZEN_MANIFEST ${MICROPY_USER_FROZEN_MANIFEST})
elseif (NOT MICROPY_FROZEN_MANIFEST)
    set(MICROPY_FROZEN_MANIFEST ${MICROPY_PORT_DIR}/boards/manifest.py)
endif()

# Add sdkconfig fragments that depend on the IDF version.
if(IDF_VERSION_MAJOR EQUAL 4 AND IDF_VERSION_MINOR LESS 2)
    set(SDKCONFIG_DEFAULTS ${SDKCONFIG_DEFAULTS} boards/sdkconfig.nimble_core0)
else()
    set(SDKCONFIG_DEFAULTS ${SDKCONFIG_DEFAULTS} boards/sdkconfig.nimble_core1)
endif()

# Concatenate all sdkconfig files into a combined one for the IDF to use.
file(WRITE ${CMAKE_BINARY_DIR}/sdkconfig.combined.in "")
foreach(SDKCONFIG_DEFAULT ${SDKCONFIG_DEFAULTS})
    #This is a bit of a deep mind-warp.  Due to how CMake caches variables, we actually want to generate a dynamic variable name to avoid any potential cache conflicts.
    find_file(sdk_${SDKCONFIG_DEFAULT} ${SDKCONFIG_DEFAULT}
        PATHS
          "${CMAKE_CURRENT_SOURCE_DIR}"
          "${MICROPY_BOARD_DIR}"
          "${MICROPY_PORT_DIR}"
          "${MICROPY_PORT_DIR}/boards"
          NO_CACHE)

    if("${sdk_${SDKCONFIG_DEFAULT}}" STREQUAL "sdk_${SDKCONFIG_DEFAULT}-NOTFOUND")
        message(WARNING "Unable to to locate SDKCONFIG_DEFAULT file ${SDKCONFIG_DEFAULT}")
    else()

        file(REAL_PATH "${sdk_${SDKCONFIG_DEFAULT}}" sdk_real_path)
        file(RELATIVE_PATH sdk_rel_path "${CMAKE_CURRENT_SOURCE_DIR}" ${sdk_real_path})

        configure_file(${sdk_${SDKCONFIG_DEFAULT}} "${CMAKE_BINARY_DIR}/${sdk_rel_path}" COPYONLY)
        file(READ ${sdk_${SDKCONFIG_DEFAULT}} CONTENTS)
        file(APPEND ${CMAKE_BINARY_DIR}/sdkconfig.combined.in "${CONTENTS}")
    endif()

endforeach()
configure_file(${CMAKE_BINARY_DIR}/sdkconfig.combined.in ${CMAKE_BINARY_DIR}/sdkconfig.combined COPYONLY)
set(SDKCONFIG_DEFAULTS ${CMAKE_BINARY_DIR}/sdkconfig.combined)

project(mp-test)
