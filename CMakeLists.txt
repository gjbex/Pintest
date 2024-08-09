cmake_minimum_required(VERSION 3.20)
project(PinTest CXX)

# Set the version
set(PinTest_VERSION_MAJOR 1)
set(PinTest_VERSION_MINOR 0)
set(PinTest_VERSION_PATCH 1)

add_definitions(
    -DPINTEST_VERSION="${PinTest_VERSION_MAJOR}.${PinTest_VERSION_MINOR}.${PinTest_VERSION_PATCH}"
    )

# Set the C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Compiler flags
set(MPI_FLAGS -DWITH_MPI)

# Find Boost
find_package(Boost REQUIRED COMPONENTS program_options)

# Find OpenMP
find_package(OpenMP)
if (NOT OpenMP_CXX_FOUND)
    message(WARNING "OpenMP not found, OpenMP versions will not be built")
endif()

# Find MPI
find_package(MPI)
if (NOT MPI_CXX_FOUND)
    message(WARNING "MPI not found, MPI versions will not be built")
endif()

include(CTest)
enable_testing()

add_subdirectory(src)
