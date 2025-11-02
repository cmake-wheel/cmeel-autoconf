cmake_minimum_required(VERSION 3.30)

set(NAME autoconf)
set(VERSION 2.72)
set(EXT tar.xz)
set(SHA256 ba885c1319578d6c94d46e9b0dceb4014caafe2490e437a0dbca3f270a223f5a)

project("cmeel-${NAME}" VERSION "${VERSION}")

include(ExternalProject)
ExternalProject_Add(
  ${NAME}
  URL "https://ftpmirror.gnu.org/gnu/${NAME}/${NAME}-${VERSION}.${EXT}"
  URL_HASH "SHA256=${SHA256}"
  DOWNLOAD_EXTRACT_TIMESTAMP ON
  BUILD_IN_SOURCE ON
  CONFIGURE_COMMAND "./configure" "--prefix=${CMAKE_INSTALL_PREFIX}"
  BUILD_COMMAND "make"
  # TEST_COMMAND "make" "check"
  INSTALL_COMMAND "make" "install")

# dummy file for install target
install(FILES "README.md" DESTINATION "share/cmeel-${NAME}/")

# Fix shebangs
install(
  CODE [[
  file(GLOB _scripts "${CMAKE_INSTALL_PREFIX}/bin/*")
  foreach(_f IN LISTS _scripts)
    file(READ "${_f}" _head LIMIT 128)
    if(_head MATCHES "^#![ \t]*([^ \t\r\n]+)")
      set(_shebang "${CMAKE_MATCH_1}")
      get_filename_component(_prog "${_shebang}" NAME)
      if(NOT _prog STREQUAL "")
        message(STATUS "Normalizing ${_shebang} in ${_f} to /usr/bin/env ${_prog}")
        file(READ "${_f}" _full)
        # Replace *only* the first shebang line
        string(REGEX REPLACE "^#![^\n\r]+"
                             "#!/usr/bin/env ${_prog}"
                             _fixed "${_full}")
        file(WRITE "${_f}" "${_fixed}")
        file(CHMOD "${_f}" PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_EXECUTE WORLD_READ WORLD_EXECUTE)
      endif()
    endif()
  endforeach()
]])
