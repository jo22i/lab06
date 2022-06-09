cmake_minimum_required(VERSION 3.10)

project(solver_app)

include_directories("${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib")
include_directories("${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib")
include_directories("${CMAKE_CURRENT_SOURCE_DIR}/solver_lib")
include_directories("${CMAKE_CURRENT_SOURCE_DIR}/solver_application")

add_library(formatter STATIC "${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib/formatter.cpp")
add_library(formatter_ex STATIC "${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/formatter_ex.cpp")
add_library(solver STATIC "${CMAKE_CURRENT_SOURCE_DIR}/solver_lib/solver.cpp")

add_executable(solver_app "${CMAKE_CURRENT_SOURCE_DIR}/solver_application/equation.cpp")

target_link_libraries(solver_app formatter_ex formatter solver)

include("${CMAKE_CURRENT_SOURCE_DIR}/CPackConfig.cmake")
