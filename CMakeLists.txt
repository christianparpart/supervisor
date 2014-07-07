cmake_minimum_required(VERSION 2.8)

project(x0)
set(PACKAGE_NAME "supervisor")
set(PACKAGE_VERSION "0.1.0")
set(PACKAGE_HOMEPAGE_URL "http://supervisor.xzero.io/")
set(PACKAGE_BUGTRACKER_URL "https://github.com/xzero/supervisor/issues/")

include(FindPkgConfig)
include(CheckIncludeFiles)
include(CheckIncludeFileCXX)
include(CheckFunctionExists)
include(CheckVariableExists)
include(CheckTypeSize)
include(CheckLibraryExists)
include(CheckCSourceCompiles)
include(CMakeDetermineCCompiler)

option(ENABLE_TESTS "Build unit tests [default: off]" OFF)

set(CMAKE_CXX_FLAGS "-std=c++0x")
add_definitions(-Wall -Wno-variadic-macros)

add_definitions(-DXOPEN_SOURCE=600)
add_definitions(-D_GNU_SOURCE)
add_definitions(-D__STDC_FORMAT_MACROS)
add_definitions(-D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE -D_LARGE_FILES)

CHECK_INCLUDE_FILES(fcntl.h HAVE_FCNTL_H)
CHECK_INCLUDE_FILES(netdb.h HAVE_NETDB_H)
CHECK_INCLUDE_FILES(gtest/gtest.h HAVE_GTEST_GTEST_H)

CHECK_INCLUDE_FILES(sys/sendfile.h HAVE_SYS_SENDFILE_H)
CHECK_FUNCTION_EXISTS(sendfile HAVE_SENDFILE)
CHECK_FUNCTION_EXISTS(posix_fadvise HAVE_POSIX_FADVISE)
CHECK_FUNCTION_EXISTS(readahead HAVE_READAHEAD)

CHECK_INCLUDE_FILES(sys/resource.h HAVE_SYS_RESOURCE_H)
CHECK_INCLUDE_FILES(sys/mman.h HAVE_SYS_MMAN_H)
CHECK_INCLUDE_FILES(sys/limits.h HAVE_SYS_LIMITS_H)
CHECK_INCLUDE_FILES(pwd.h HAVE_PWD_H)
CHECK_INCLUDE_FILES(syslog.h HAVE_SYSLOG_H)
CHECK_INCLUDE_FILES(uuid/uuid.h HAVE_UUID_UUID_H)
CHECK_INCLUDE_FILES(sys/utsname.h HAVE_SYS_UTSNAME_H)
CHECK_INCLUDE_FILES(dlfcn.h HAVE_DLFCN_H)

CHECK_FUNCTION_EXISTS(fork HAVE_FORK)
CHECK_FUNCTION_EXISTS(chroot HAVE_CHROOT)
CHECK_FUNCTION_EXISTS(pathconf HAVE_PATHCONF)
CHECK_FUNCTION_EXISTS(accept4 HAVE_ACCEPT4)
CHECK_LIBRARY_EXISTS(pthread pthread_setname_np "" HAVE_PTHREAD_SETNAME_NP)
CHECK_LIBRARY_EXISTS(pthread pthread_setaffinity_np "" HAVE_PTHREAD_SETAFFINITY_NP)

if(ENABLE_INOTIFY)
  CHECK_INCLUDE_FILES(sys/inotify.h HAVE_SYS_INOTIFY_H)
  if(HAVE_SYS_INOTIFY_H)
    CHECK_FUNCTION_EXISTS(inotify_init1 HAVE_INOTIFY_INIT1)
  endif(HAVE_SYS_INOTIFY_H)
endif()

if(ENABLE_TCMALLOC)
  CHECK_INCLUDE_FILES(google/tcmalloc.h HAVE_GOOGLE_TCMALLOC_H)
#  pkg_check_modules(TCMALLOC REQUIRED libtcmalloc>=1.8.3)
  set(TCMALLOC_LIBRARIES tcmalloc)
endif()

CHECK_INCLUDE_FILES(zlib.h HAVE_ZLIB_H)
if(HAVE_ZLIB_H)
  CHECK_LIBRARY_EXISTS(z deflate "" HAVE_LIBZ)
  set(ZLIB_LIBRARIES z)
endif(HAVE_ZLIB_H)

CHECK_INCLUDE_FILES(bzlib.h HAVE_BZLIB_H)
if(HAVE_BZLIB_H)
  CHECK_LIBRARY_EXISTS(bz2 BZ2_bzCompressInit "" HAVE_LIBBZ2)
  set(BZIP2_LIBRARIES bz2)
endif(HAVE_BZLIB_H)

# TODO dynamic check for tbb
# tbb is required for director plugin and for base's TimeoutScheduler (needed?)
# However, onn Ubuntu 14.04, the standard clang++ causes this test to fail.
if(NOT ("${CMAKE_CXX_COMPILER}" MATCHES "clang"))
  if(ENABLE_PLUGIN_DIRECTOR)
    CHECK_INCLUDE_FILE_CXX(tbb/tbb.h HAVE_TBB_TBB_H)
    if(NOT HAVE_TBB_TBB_H)
      message(FATAL_ERROR "Can't find <tbb/tbb.h> include file.\nPlease install Threading Building Blocks (e.g. libtbb-dev).")
    endif()
  endif()
endif()
set(TBB_LIBRARIES tbb)

# ---------------------------------------------------------------------------
include_directories(${CMAKE_CURRENT_BINARY_DIR}/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

if(ENABLE_TESTS)
  CHECK_INCLUDE_FILES(gtest/gtest.h HAVE_GTEST_GTEST_H)
endif()

configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/src/sysconfig.h.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/src/sysconfig.h)

add_definitions(-DHAVE_SYSCONFIG_H)

add_subdirectory(src)

add_custom_target(ctags
  COMMAND ctags -R --c++-kinds=+p --fields=+iaS --extra=+q ${CMAKE_SOURCE_DIR}/lib ${CMAKE_SOURCE_DIR}/include
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})

add_custom_target(cscope
  COMMAND echo -n > ${CMAKE_SOURCE_DIR}/cscope.files
          && find ${CMAKE_SOURCE_DIR}/src -name '*.h' -o -name '*.cc' -o -name '*.cpp' >> ${CMAKE_SOURCE_DIR}/cscope.files
          && find ${CMAKE_SOURCE_DIR}/lib -name '*.h' -o -name '*.cc' -o -name '*.cpp' >> ${CMAKE_SOURCE_DIR}/cscope.files
          && find ${CMAKE_SOURCE_DIR}/include -name '*.h' -o -name '*.cc' -o -name '*.hpp' >> ${CMAKE_SOURCE_DIR}/cscope.files
          && cscope -q -R -b -i cscope.files
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
)

add_custom_target(release
  COMMAND ${CMAKE_SOURCE_DIR}/contrib/ppa-release.sh
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
)
