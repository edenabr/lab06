cmake_minimum_required(VERSION 3.10)

project(lab05)
option(BUILD_TESTS "Build tests" OFF)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex_lib)
add_subdirectory(solver_lib)
add_subdirectory(hello_world_application)
add_subdirectory(solver_application)

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB TEST_SOURCES tests/*.cpp)
  add_executable(check ${TEST_SOURCES})
  target_link_libraries(check solver gtest_main)
  add_test(NAME check COMMAND check)
endif()

# Disable warnings-as-errors for old GoogleTest on modern GCC
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
  foreach(_target gtest gtest_main gmock gmock_main)
    if(TARGET ${_target})
      target_compile_options(${_target} PRIVATE -Wno-error)
    endif()
  endforeach()
endif()
