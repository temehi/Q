# ===========================================================================
#                  SeqAn - The Library for Sequence Analysis
# ===========================================================================
# File: /preprocessing/nexcat/CMakeLists.txt
#
# CMakeLists.txt file for nexcat.
# ===========================================================================

message (STATUS "${ColourBold}Configuring nexcat...${ColourReset}")

# ----------------------------------------------------------------------------
# Dependencies
# ----------------------------------------------------------------------------
# Search SeqAn and select dependencies.

find_package(ZLIB)
find_package(BZip2)
find_package(SeqAn REQUIRED)
find_package (Boost)

if (NOT CXX11_FOUND)
    message (FATAL_ERROR "  C++11 not found: Not building nexcat.")
    return ()
endif (NOT CXX11_FOUND)

if (NOT CXX14_FOUND)
    message (FATAL_ERROR "  C++14 not found: Not building nexcat.")
    return ()
endif (NOT CXX14_FOUND)

# exit if boost was not found.
if (NOT Boost_FOUND)
  message (STATUS "  Bost not found: Not building nexcat.")
  return ()
endif (NOT Boost_FOUND)


# Warn if Zlib was not found..
if (NOT ZLIB_FOUND)
  message (WARNING "WARNING: Zlib not found! nexcat will not work compressed files including BAM files")
endif (NOT ZLIB_FOUND)

# Warn if BZip2 was not found.
if (NOT BZIP2_FOUND)
  message (WARNING "WARNING: BZip2 not found. Building nexcat without support for bzipped input and output.")
endif (NOT BZIP2_FOUND)

if(CMAKE_COMPILER_IS_GNUCXX)
  if (491 GREATER _GCC_VERSION)
    message (FATAL_ERROR "Your GCC version is too old. Minimum version is GCC-4.9.1!")
    return ()
  endif(491 GREATER _GCC_VERSION)
endif(CMAKE_COMPILER_IS_GNUCXX)

# ----------------------------------------------------------------------------
# App-Level Configuration
# ----------------------------------------------------------------------------

set (SEQAN_APP_VERSION "1.0.0")

# ----------------------------------------------------------------------------
# Change to approprate compiler flags
# ----------------------------------------------------------------------------
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
    message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()


# ----------------------------------------------------------------------------
# Build Setup
# ----------------------------------------------------------------------------

# Add include directories.
include_directories (${SEQAN_INCLUDE_DIRS})
include_directories (SYSTEM ${Boost_INCLUDE_DIRS})
INCLUDE_DIRECTORIES(${SEQAN_INCLUDE_DIRS} ${CMAKE_CURRENT_SOURCE_DIR}/../shared_headers)


# Add definitions set by find_package (SeqAn).
add_definitions (${SEQAN_DEFINITIONS})

# Add definitions set by the build system.
add_definitions (-DSEQAN_REVISION="${SEQAN_REVISION}")
add_definitions (-DSEQAN_DATE="${SEQAN_DATE}")
add_definitions (-DSEQAN_APP_VERSION="${SEQAN_APP_VERSION}")

# Update the list of file names below if you add source files to your application.
add_executable(nexcat nexcat.cpp)


# Add dependencies found by find_package (SeqAn).
target_link_libraries (nexcat ${SEQAN_LIBRARIES})

# Add CXX flags found by find_package (SeqAn).
set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${SEQAN_CXX_FLAGS} ${CXX11_CXX_FLAGS} ${CXX14_CXX_FLAGS}")

# ----------------------------------------------------------------------------
# Installation
# ----------------------------------------------------------------------------

# Set variables for installing, depending on the selected build type.
if (NOT SEQAN_PREFIX_SHARE_DOC)
  seqan_setup_install_vars (nexcat)
endif (NOT SEQAN_PREFIX_SHARE_DOC)

# Install nexcat in ${PREFIX}/bin directory
install (TARGETS nexcat
         DESTINATION bin)

# Install non-binary files for the package to "." for app builds and
# ${PREFIX}/share/doc/nexcat for SeqAn release builds.
install (FILES LICENSE README
         DESTINATION ${SEQAN_PREFIX_SHARE_DOC})

# ----------------------------------------------------------------------------
# App Test
# ----------------------------------------------------------------------------

seqan_add_app_test (nexcat)

# ----------------------------------------------------------------------------
# Setup Common Tool Description for Generic Workflow Nodes
# ----------------------------------------------------------------------------

# Include executable nexcat in CTD structure.
set (SEQAN_CTD_EXECUTABLES ${SEQAN_CTD_EXECUTABLES} nexcat CACHE INTERNAL "")

# ----------------------------------------------------------------------------
# CPack Install
# ----------------------------------------------------------------------------

if (SEQAN_BUILD_SYSTEM STREQUAL "APP:nexcat")
  set (CPACK_PACKAGE_NAME "nexcat")
  set (CPACK_PACKAGE_DESCRIPTION_SUMMARY "nexcat - a fast saturation-based ChIP-seq peak caller.")
  set (CPACK_DEBIAN_PACKAGE_MAINTAINER "Benjamin Menkuec")
  set (CPACK_PACKAGE_VENDOR "Charite Universitätsmedizin Berlin")

  seqan_configure_cpack_app (nexcat "nexcat")
endif (SEQAN_BUILD_SYSTEM STREQUAL "APP:nexcat")