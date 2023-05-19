# For more information about build system see
# https://docs.espressif.com/projects/esp-idf/en/latest/api-guides/build-system.html
# The following five lines of boilerplate have to be in your project's
# CMakeLists in this exact order for cmake to work correctly
cmake_minimum_required(VERSION 3.20)

message(STATUS "Clearing cache")
unset(EXTMODS CACHE)
unset(EXTMODS_NAMES CACHE)
unset(EXTMODS_INIT CACHE)

cmake_path(SET MICROPY_DIR "${CMAKE_CURRENT_SOURCE_DIR}/external/micropython")

set(EXTRA_COMPONENT_DIRS ./external/micropython/ports/esp32/main ./obsolete_components)

include($ENV{IDF_PATH}/tools/cmake/project.cmake)
project(mp-test)
