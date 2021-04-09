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

# headers
set (LIBVISO2_HDR_FILES
    "src/filter.h"
    "src/matcher.h"
    "src/matrix.h"
    "src/reconstruction.h"
    "src/timer.h"
    "src/triangle.h"
    "src/viso.h"
    "src/viso_mono.h"
    "src/viso_stereo.h"
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


################################
## INSTALLATION

include(GNUInstallDirs)

# install library
install(TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

# install headers
install(FILES ${LIBVISO2_HDR_FILES}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME})

# create a CMake configuration
install(EXPORT ${PROJECT_NAME}
    DESTINATION share/${PROJECT_NAME}/cmake
    FILE ${PROJECT_NAME}Config.cmake
)
