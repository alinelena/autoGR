cmake_minimum_required(VERSION 3.0.0)

project(autoGR
  VERSION 0.1.1
  HOMEPAGE_URL https://github.com/msg-byu/autoGR
  LANGUAGES Fortran)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")
if (NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release")
endif()
include(helper)
include(flags)
include(GNUInstallDirs)

set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/${CMAKE_INSTALL_LIBDIR})
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/${CMAKE_INSTALL_BINDIR})

option(INTERNAL_KGRIDGEN "Build with Internal kgridgen rather than external" OFF)

if(INTERNAL_KGRIDGEN)
  include(ExternalProject)
  ExternalProject_Add(int-kgrid
    GIT_REPOSITORY https://github.com/alinelena/kgridGen
    GIT_TAG master
    GIT_PROGRESS true
    PREFIX int-kgrid
    BINARY_DIR int-kgrid/build
    SOURCE_DIR int-kgrid/symlib
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX} -DCMAKE_Fortran_COMPILER=${CMAKE_Fortran_COMPILER} -DBUILD_SHARED_LIBS=off -DCMAKE_BUILD_TYPE=Release -DINTERNAL_SYMLIB=On
    )
  set(KGRID_INC ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR})
  set(KGRID_LIB ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR})
  set(LIBS "kptgen;sym;rational;utils")

else()
  find_package(PkgConfig REQUIRED)
  pkg_check_modules(KGRID REQUIRED kgridGen>=0.1.1)
  set(KGRID_INC ${KGRID_INCLUDE_DIRS})
  set(KGRID_LIB ${KGRID_LIBRARY_DIRS})
  set(LIBS ${KGRID_LIBRARIES})
  message(STATUS "symlib include dirs ${KGRID_INC}")
  message(STATUS "symlib include dirs ${LIBS}")
  pkg_check_modules(SYMLIB REQUIRED symlib>=2.0.1)
  set(LIBS ${LIBS} ${SYMLIB_LIBRARIES})

endif()
link_directories(${KGRID_LIB})



add_subdirectory(src)


