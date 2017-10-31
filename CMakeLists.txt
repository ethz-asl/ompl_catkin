cmake_minimum_required(VERSION 2.8.3)
project(ompl_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(ompl_src
  GIT_REPOSITORY https://github.com/ompl/ompl.git
  GIT_TAG 0d840115b4f1459034c026864598605b7d486dd7
  UPDATE_COMMAND ""
  PATCH_COMMAND patch -p1 < ${CMAKE_SOURCE_DIR}/patch/bugfix.patch
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
  -DCMAKE_INSTALL_LIBDIR:PATH=${CATKIN_DEVEL_PREFIX}/lib/
  -DBUILD_SHARED_LIBS:BOOL=true
  -DCMAKE_BUILD_TYPE:STRING=Release
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} ompl_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libompl${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS ompl-extras.cmake)
