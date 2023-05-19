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

set(BADGEPY_BOARD DEFAULT)
cmake_path(SET BADGEPY_BOARD_DIR NORMALIZE "${CMAKE_CURRENT_SOURCE_DIR}/boards/${BADGEPY_BOARD}")

cmake_path(SET MICROPY_DIR NORMALIZE "${CMAKE_CURRENT_SOURCE_DIR}/external/micropython")
cmake_path(RELATIVE_PATH MICROPY_DIR)
cmake_path(SET MICROPY_PORT_DIR NORMALIZE "${MICROPY_DIR}/ports/esp32")

set(EXTRA_COMPONENT_DIRS ./obsolete_components)

project(mp-test)
