cmake_minimum_required(VERSION 3.15)

project(vui
    VERSION 1.0.0
    LANGUAGES CXX
)

# Platform-specific configuration
if(WIN32)
    include(cmake/ConfigWindows.cmake)
elseif(APPLE)
    include(cmake/ConfigMacOS.cmake)
elseif(UNIX)
    include(cmake/ConfigLinux.cmake)
endif()


# Set the C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Build type" FORCE)

# Set the files directories
set(HEADERS_DIR "${CMAKE_CURRENT_SOURCE_DIR}/headers")
set(SOURCES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/sources")
set(TESTS_DIR "${CMAKE_CURRENT_SOURCE_DIR}/tests")

# Add the include directories
include_directories(
    ${HEADERS_DIR}
)

# Add the source files
file(GLOB_RECURSE SOURCES
    "${SOURCES_DIR}/*.cpp"
)

# Add the executable target
add_library(${PROJECT_NAME} ${SOURCES})

# Set the target properties
set_target_properties(${PROJECT_NAME} PROPERTIES
    CXX_STANDARD 20
    CXX_STANDARD_REQUIRED ON
    CXX_EXTENSIONS OFF
)

# Add the include directories to the target
target_include_directories(${PROJECT_NAME} PRIVATE
    ${HEADERS_DIR}
)

# For each library in the libraries directory, add it as a subdirectory
file(GLOB LIBRARIES_DIR "${CMAKE_CURRENT_SOURCE_DIR}/libraries/*")
foreach(LIBRARY_DIR ${LIBRARIES_DIR})
    if (IS_DIRECTORY ${LIBRARY_DIR})
        add_subdirectory(${LIBRARY_DIR})
    else()
        message(STATUS "Skipping non-directory: ${LIBRARY_DIR}")
    endif()
endforeach()

target_link_libraries(${PROJECT_NAME} PRIVATE
    yaml-cpp
)

# Add the tests
enable_testing()
add_subdirectory(${TESTS_DIR})