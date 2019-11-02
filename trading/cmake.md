# cmake：构建项目工程

`cmake` 是一款跨平台的代码**项目工程构建套件**，通过 `CMakeLists.txt` 来设置相关的参数。

## `myCTP` 的配置信息

```cmake
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)
project(myctp)

## -----------------------------------------------------------------------------
#set compiler for c++ language
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -g -pthread")
set(CMAKE_CXX_COMPILER "g++")

# set bin
message(STATUS "|--> PROJECT_BINARY_DIR: ${PROJECT_BINARY_DIR}")
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)


# set path
message(STATUS "|--> PROJECT_SOURCE_DIR: ${PROJECT_SOURCE_DIR}")
set(INCLUDE_PATH ${PROJECT_SOURCE_DIR}/include)
set(LIB_PATH ${PROJECT_SOURCE_DIR}/lib)
## -----------------------------------------------------------------------------


## -----------------------------------------------------------------------------
#add the dir that including head file
include_directories(${INCLUDE_PATH})
include_directories(${INCLUDE_PATH}/ctp)
#library path
link_directories(${LIB_PATH}/ctp)
link_directories(${LIB_PATH}/yaml-cpp)

link_libraries(thostmduserapi_se)
link_libraries(thosttraderapi_se)
link_libraries(yaml-cpp)

link_libraries(pthread)
## -----------------------------------------------------------------------------


#add the source file to exe
aux_source_directory(./src DIR_SRCS)

## MD ==========================================================================
set(MD_SRCS ./src/mainMD.cpp ./src/md.cpp ./src/td.cpp ./src/util.cpp)
add_executable(myctpMD ${MD_SRCS})
## =============================================================================

## TD ==========================================================================
set(TD_SRCS ./src/mainTD.cpp ./src/md.cpp ./src/td.cpp ./src/util.cpp)
add_executable(myctpTD ${TD_SRCS})
## =============================================================================
```
