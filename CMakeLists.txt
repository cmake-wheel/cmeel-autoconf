cmake_minimum_required(VERSION 3.20)

set(AUTOCONF_VERSION 2.71)

project(cmeel-autoconf VERSION ${AUTOCONF_VERSION})

include(ExternalProject)
ExternalProject_Add(
  autoconf
  URL "https://ftpmirror.gnu.org/gnu/autoconf/autoconf-${AUTOCONF_VERSION}.tar.xz"
      DOWNLOAD_EXTRACT_TIMESTAMP
      ON
  BUILD_IN_SOURCE ON
  CONFIGURE_COMMAND "./configure" "--prefix=${CMAKE_INSTALL_PREFIX}"
  BUILD_COMMAND "make"
  # TEST_COMMAND "make" "check"
  INSTALL_COMMAND "make" "install")

# dummy file for install target
install(FILES README.md DESTINATION share/cmeel-autoconf/)
