cmake_minimum_required(VERSION 3.14)
project(DronePort_case)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(cpp_task cpp_task.cpp)

find_package(Threads REQUIRED)
target_link_libraries(cpp_task PRIVATE Threads::Threads)