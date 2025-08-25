cmake_minimum_required(VERSION 3.13)
project(dectalk CXX)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY build)
set(SRCDIR .)
set(ORIG_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(TARGET_ROOT_DIR "dist")
set(TARGET_BUILD_DIR "${TARGET_ROOT_DIR}/opt/dectalk")
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

  INSTALL_COMMAND make install DESTDIR=${CMAKE_CURRENT_BINARY_DIR}/${TARGET_ROOT_DIR}
)

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_BUILD_DIR}/DECtalk.conf
  TYPE SYSCONF
)

install(DIRECTORY
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_BUILD_DIR}/include/dtk
  TYPE INCLUDE
)

#
# doc
#

install(DIRECTORY
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_DOC_DIR}/man/man1
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_DOC_DIR}/man/man3
  TYPE MAN
)

#
# data
#

install(DIRECTORY
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_BUILD_DIR}/bitmaps
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_BUILD_DIR}/dic
  TYPE DATA
)

#
# lib
#

set(LIB_FILES
  tts
  tts_fr
  tts_gr
  tts_la
  tts_sp
  tts_uk
  tts_us
)

list(TRANSFORM
  LIB_FILES
  PREPEND ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_LIB_DIR}/lib
)

list(TRANSFORM
  LIB_FILES
  APPEND .so
)

install(FILES
  ${LIB_FILES}
  TYPE LIB
)

#
# bin
#

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_BUILD_DIR}/aclock
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_BUILD_DIR}/dtmemory
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_BUILD_DIR}/gspeak
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_BUILD_DIR}/say
  ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_BUILD_DIR}/windic
  TYPE BIN
)
