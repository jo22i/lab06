cmake_minimum_required(VERSION 3.10)

project(formatter)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

add_library(formatter STATIC formatter.cpp formatter.h)
