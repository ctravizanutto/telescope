cmake_minimum_required(VERSION 3.28)
project(telescope)
set(CMAKE_CXX_STANDARD 17)

set(RAYLIB_VERSION 5.0)
find_package(raylib ${RAYLIB_VERSION} QUIET) # QUIET or REQUIRED
if (NOT raylib_FOUND) # If there's none, fetch and build raylib
    include(FetchContent)
    FetchContent_Declare(
            raylib
            DOWNLOAD_EXTRACT_TIMESTAMP OFF
            URL https://github.com/raysan5/raylib/archive/refs/tags/${RAYLIB_VERSION}.tar.gz
    )
    FetchContent_GetProperties(raylib)
    if (NOT raylib_POPULATED) # Have we downloaded raylib yet?
        set(FETCHCONTENT_QUIET NO)
        FetchContent_Populate(raylib)
        set(BUILD_EXAMPLES OFF CACHE BOOL "" FORCE) # don't build the supplied examples
        add_subdirectory(${raylib_SOURCE_DIR} ${raylib_BINARY_DIR})
    endif()
endif()

add_subdirectory(external/lua)
add_subdirectory(external/sol)

include_directories(src/engine)

add_executable(telescope src/main.cpp
        src/engine/app.cpp
        src/particles.cpp
        src/particles.h
        src/engine/system.h)
target_link_libraries(telescope raylib lua::lib sol2)
