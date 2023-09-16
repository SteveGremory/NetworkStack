cmake_minimum_required(VERSION 3.22)
project(NetworkStack)

# Set C++ options
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_BUILD_TYPE Release)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# GTest & GBench
set(GTEST_GIT_TAG v1.13.0)
set(GBENCH_GIT_TAG v1.8.3)
include(FetchContent)
FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG ${GTEST_GIT_TAG}
)
FetchContent_Declare(
    googlebenchmark
    GIT_REPOSITORY https://github.com/google/benchmark.git
    GIT_TAG ${GBENCH_GIT_TAG}
)
FetchContent_MakeAvailable(googletest googlebenchmark)
message("-- Fetched GBench version: ${GBENCH_GIT_TAG}")
message("-- Fetched GTest version: ${GTEST_GIT_TAG}")

# Libnet
file(GLOB_RECURSE LIBNET_SOURCES
        libnet/*.cpp
)
add_library(libnet ${LIBNET_SOURCES})
target_include_directories(libnet PUBLIC 
        libnet/
)

target_compile_options(libnet PRIVATE
        -Wall -fsanitize=address -Wextra
)

# Tests
file(GLOB_RECURSE TESTS_SOURCES
        tests/*.cpp
)
add_executable(tests ${TESTS_SOURCES})
target_link_libraries(
    tests PUBLIC
        GTest::gtest_main
        libnet
)
include(GoogleTest)
gtest_discover_tests(tests)

# Benchmarks
file(GLOB_RECURSE BENCHMARKS_SOURCES
    benchmarks/*.cpp
)
add_executable(benchmarks ${BENCHMARKS_SOURCES})
target_link_libraries(
    benchmarks PUBLIC
        benchmark::benchmark
)
