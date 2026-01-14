# CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(MyApp C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra")

# 指定源文件
file(GLOB SOURCES "srcs/*.c")

# 添加可执行文件
add_executable(myapp ${SOURCES})

# 包含头文件目录
target_include_directories(myapp PRIVATE headers)