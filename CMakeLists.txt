cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_COVERAGE "Build coverage" OFF)

set(
  HUNTER_CACHE_SERVERS
  "https://github.com/bmstu-iu8-cpp-sem-3/hunter-cache"
  CACHE STRING "Default cache server"
)

include("tools/gate/cmake/HunterGate.cmake")

HunterGate(
  URL "https://github.com/cpp-pm/hunter/archive/v0.23.218.tar.gz"
  SHA1 "9a3594a61227967fb92159339ba326701b287b19"
)

# TODO: rename project and delete this comment
project(server)
string(APPEND CMAKE_CXX_FLAGS " -pedantic -Werror -Wall -Wextra")
string(APPEND CMAKE_CXX_FLAGS " -Wno-unused-command-line-argument")
string(APPEND CMAKE_CXX_FLAGS " -Wshadow -Wnon-virtual-dtor")
string(APPEND CMAKE_CXX_FLAGS " -g")

hunter_add_package(Boost COMPONENTS filesystem log system thread)
find_package(Boost CONFIG REQUIRED filesystem log log_setup system thread)

add_library(server STATIC
  ${CMAKE_CURRENT_SOURCE_DIR}/sources/sync_server.cpp
)

add_library(client STATIC
  ${CMAKE_CURRENT_SOURCE_DIR}/sources/client.cpp
)

target_link_libraries(
        server
        Boost::system
        Boost::thread
        Boost::log_setup
        Boost::log
        Boost::filesystem
)

target_link_libraries(
        client
        Boost::system
        Boost::thread
        Boost::log_setup
        Boost::log
        Boost::filesystem
)

add_executable(
        main
        ${CMAKE_CURRENT_SOURCE_DIR}/sources/main.cpp
)

add_executable(
        test
        ${CMAKE_CURRENT_SOURCE_DIR}/sources/test.cpp
)

target_link_libraries(
        main
        server
)

target_link_libraries(
        test
        client
)

target_include_directories(server 
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
)

target_include_directories(main 
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
)

target_include_directories(client 
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
)

target_include_directories(main 
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
)