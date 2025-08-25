cmake_minimum_required(VERSION 3.13)
project(dectalk CXX)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY build)
set(SRCDIR .)
set(ORIG_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(TARGET_BUILD_DIR "dist/opt/dectalk")
set(TARGET_DOC_DIR "${TARGET_BUILD_DIR}/doc/DECtalk")
set(TARGET_LIB_DIR "${TARGET_BUILD_DIR}/lib")

find_package(PkgConfig)
pkg_check_modules(ALSA REQUIRED alsa)
pkg_check_modules(GTK2 REQUIRED gtk+-2.0)
pkg_check_modules(LIBPULSE REQUIRED libpulse)

include(ExternalProject)
ExternalProject_Add(
  dectalk-orig
  PREFIX ${CMAKE_BINARY_DIR}
  SOURCE_DIR ${ORIG_DIR}
  CONFIGURE_COMMAND ./autogen.sh
  COMMAND ./configure

  BUILD_IN_SOURCE 1
  BUILD_COMMAND cd ${ORIG_DIR}
  COMMAND ${MAKE}

  INSTALL_COMMAND make install DESTDIR=${CMAKE_CURRENT_BINARY_DIR}/dist
)

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/dist/opt/dectalk/say
  TYPE BIN
)

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_DOC_DIR}/man/man1/say.1
  TYPE MAN
)

install(FILES
  "${CMAKE_CURRENT_BINARY_DIR}/${TARGET_LIB_DIR}/libtts.so"
  "${CMAKE_CURRENT_BINARY_DIR}/${TARGET_LIB_DIR}/libtts_us.so"
  TYPE LIB
)
