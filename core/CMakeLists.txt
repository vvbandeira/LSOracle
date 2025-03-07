cmake_minimum_required (VERSION 3.9)                  # CMake minimum version
project(lsoracle)

include_directories(${PROJECT_SOURCE_DIR}/lib/kahypar/include)
#include_directories(${PROJECT_SOURCE_DIR}/lib/Galois/lonestar)

include_directories(${PROJECT_SOURCE_DIR}/algorithms/classification/fplus/include)
include_directories(${PROJECT_SOURCE_DIR}/algorithms/classification/eigen)
include_directories(${PROJECT_SOURCE_DIR}/algorithms/classification/json/include)
include_directories(${PROJECT_SOURCE_DIR}/algorithms/classification/fdeep_keras/include)

set(CMAKE_CXX_STANDARD 17)                            # enable c++17
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_custom_command(
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/kahypar_config.cpp
  COMMAND python3 ${PROJECT_SOURCE_DIR}/generate_kahypar.py ${PROJECT_SOURCE_DIR}/test.ini ${CMAKE_CURRENT_BINARY_DIR}/kahypar_config.cpp
  MAIN_DEPENDENCY ${PROJECT_SOURCE_DIR}/test.ini
)

add_executable(lsoracle lsoracle.cpp ${CMAKE_CURRENT_BINARY_DIR}/kahypar_config.cpp kahypar_temp_config.cpp)

target_include_directories(lsoracle PRIVATE ../lib/kahypar/include)
target_include_directories(lsoracle PRIVATE .)

if (${ENABLE_GALOIS})
  add_definitions(-DENABLE_GALOIS)
  target_include_directories(lsoracle PRIVATE ../lib/Galois/lonestar)
  target_link_libraries(lsoracle galois_utah)
endif()

if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang" AND CMAKE_CXX_COMPILER_VERSION VERSION_LESS 9.0 OR CMAKE_CXX_COMPILER_ID STREQUAL "GNU" AND CMAKE_CXX_COMPILER_VERSION VERSION_LESS 9.1)
  target_link_libraries(lsoracle alice mockturtle stdc++fs kahypar)
else()
  target_link_libraries(lsoracle alice mockturtle kahypar)
endif()


configure_file(test.ini test.ini COPYONLY)
