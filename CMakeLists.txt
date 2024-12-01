cmake_minimum_required(VERSION 3.22)

project(chifc)

if(CMAKE_CXX_COMPILER_VERSION VERSION_GREATER_EQUAL "10.0")
    set(CMAKE_CXX_STANDARD 20)
else()
    set(CMAKE_CXX_STANDARD 17)
endif()

set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(Boost_USE_STATIC_LIBS ON)
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static -static-libgcc -static-libstdc++")

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(CMAKE_CXX_FLAGS_DEBUG "-g -O0 -Wall -Wextra -Wpedantic")
elseif(CMAKE_BUILD_TYPE STREQUAL "Release")
    set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -Wextra -Wpedantic")
endif()

find_package(Boost REQUIRED COMPONENTS system thread)

if(Boost_FOUND)
    message(STATUS "Boost found: ${Boost_INCLUDE_DIRS}")
    include_directories(${Boost_INCLUDE_DIRS})
else()
    message(FATAL_ERROR "Boost not found")
endif()

# if you want to add a subdirectory, do it in this section
# Pay attention to line 41!

include_directories(${CMAKE_SOURCE_DIR}/include)

file(GLOB_RECURSE SOURCES "src/*.cpp")

add_executable(chifc ${SOURCES})

# If you added a subdirectory, don't forget to add it here!
target_link_libraries(chifc
        PRIVATE
        ${Boost_LIBRARIES}
        pthread
)

set_target_properties(chifc PROPERTIES LINK_SEARCH_START_STATIC ON)

target_compile_options(chifc PRIVATE
        $<$<CONFIG:Debug>:-g -O0>
        $<$<CONFIG:Release>:-O3>
)


