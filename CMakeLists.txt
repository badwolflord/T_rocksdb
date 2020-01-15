cmake_minimum_required(VERSION 3.15)
project(T_rocksdb)

set(CMAKE_CXX_STANDARD 17)

include(CheckCCompilerFlag)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "RelWithDebInfo" CACHE STRING "Default BUILD_TYPE is RelWithDebInfo, other options are: Debug, Release, and MinSizeRel." FORCE)
endif()

include_directories(SYSTEM /usr/local/include)
link_directories(/usr/local/lib)
list(APPEND CMAKE_REQUIRED_INCLUDES /usr/local/include)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/modules/")

find_package(snappy REQUIRED)
find_package(ZLIB REQUIRED)
option(WITH_LZ4 "LZ4 compression support" ON)
if(WITH_LZ4)
    find_package(LZ4 1.7 REQUIRED)
    set(HAVE_LZ4 ${LZ4_FOUND})
endif(WITH_LZ4)

message("-DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}\n-DWITH_SNAPPY=${SNAPPY_FOUND}\n-DWITH_LZ4=${LZ4_FOUND}\n-DWITH_ZLIB=${ZLIB_FOUND}")

set(HAVE_LIBROCKSDB 1)
include(BuildRocksDB)
build_rocksdb()

add_executable(T_rocksdb main.cpp)
target_include_directories(T_rocksdb SYSTEM PUBLIC $<TARGET_PROPERTY:RocksDB::RocksDB,INTERFACE_INCLUDE_DIRECTORIES>)
target_link_libraries(T_rocksdb RocksDB::RocksDB)