cmake_minimum_required(VERSION 3.10)

project(solver_app)

include_directories("../formatter_lib")
include_directories("../formatter_ex_lib")
include_directories("../solver_lib")

add_library(formatter STATIC "../formatter_lib/formatter.cpp" "../formatter_lib/formatter.h")
add_library(formatter_ex STATIC "../formatter_ex_lib/formatter_ex.cpp" "../formatter_ex_lib/formatter_ex.h")
add_library(solver STATIC "../solver_lib/solver.cpp" "../solver_lib/solver.h")

add_executable(solver_app equation.cpp)

target_link_libraries(solver_app formatter_ex formatter solver)
