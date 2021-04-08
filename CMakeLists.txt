# project
cmake_minimum_required (VERSION 2.6)
project (libviso2)


################################
## BUILDING

# for PNG reading support, find the C++ headers and the C libraries
find_package(PNG)
find_path(PNGPP_INCLUDE_DIR "png++/png.hpp" REQUIRED)

# use sse3 instruction set
set(CMAKE_CXX_FLAGS "-msse3")

# sources
set (LIBVISO2_SRC_FILES
    "src/filter.cpp"
    "src/matcher.cpp"
    "src/matrix.cpp"
    "src/reconstruction.cpp"
    "src/triangle.cpp"
    "src/viso.cpp"
    "src/viso_mono.cpp"
    "src/viso_stereo.cpp"
)

add_library(${PROJECT_NAME} ${LIBVISO2_SRC_FILES})

# include directory
target_include_directories(${PROJECT_NAME} PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>"
    "$<INSTALL_INTERFACE:$<INSTALL_PREFIX>/${CMAKE_INSTALL_INCLUDEDIR}>/${PROJECT_NAME}")

# demo program
if (PNG_FOUND AND PNGPP_INCLUDE_DIR)
    add_executable(viso2 "src/demo.cpp")
    target_include_directories(viso2 PRIVATE ${PNGPP_INCLUDE_DIR})
    target_link_libraries (viso2 ${PROJECT_NAME} PNG::PNG)
else()
    message(NOTICE "PNG support not available (need 'png++' and 'libpng').")
endif()

