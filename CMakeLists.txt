cmake_minimum_required(VERSION 3.9 FATAL_ERROR)
if(POLICY CMP0074)
    cmake_policy(SET CMP0074 NEW) # Use <PackageName>_ROOT variables
endif()
project(FastFormat
  DESCRIPTION "The fastest, most robust C++ formatting library"
  LANGUAGES C CXX)

# Directory for CMake specific extensions and source files.
set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

# Set build type to Debug if this is a Git repository.
# Otherwise set to Release.
# Unless user overrides on the command line.
include(BuildType)

# Configure a project for testing with CTest/CDash.
# Activate testing (var BUILD_TESTING).
# unless user overrides on the command line.
include(CTest)

# Handle version number
set(RX_WS "[ \t\r\n]")
file(READ "${CMAKE_CURRENT_SOURCE_DIR}/include/fastformat/fastformat.h" _header_file)
string(REGEX MATCH "#define${RX_WS}+FASTFORMAT_VER_MAJOR${RX_WS}+([0-9]+)" MAJOR_DUMMY ${_header_file})
set(_VERSION_MAJOR ${CMAKE_MATCH_1})
string(REGEX MATCH "#define${RX_WS}+FASTFORMAT_VER_MINOR${RX_WS}+([0-9]+)" MINOR_DUMMY ${_header_file})
set(_VERSION_MINOR ${CMAKE_MATCH_1})
string(REGEX MATCH "#define${RX_WS}+FASTFORMAT_VER_REVISION${RX_WS}+([0-9]+)" PATCH_DUMMY ${_header_file})
set(_VERSION_PATCH ${CMAKE_MATCH_1})

# Set project version number here
set(PROJECT_VERSION_MAJOR ${_VERSION_MAJOR})
set(PROJECT_VERSION_MINOR ${_VERSION_MINOR})
set(PROJECT_VERSION_PATCH ${_VERSION_PATCH})
set(PROJECT_VERSION "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH}")

# Adhere strictly to C and C++ standards plus extensions.
set(CMAKE_C_STANDARD 90)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS ON) # GNU extensions and POSIX standard
set(CMAKE_CXX_STANDARD 98)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS ON)

# #########################################################
# Preparations for installing

# Provides install directory variables as defined by the GNU Coding Standards.
include(GNUInstallDirs)

# Build
option(FASTFORMAT_USE_WIDE_STRINGS "Build with wide string support." Off)
if(FASTFORMAT_USE_WIDE_STRINGS)
    set_property(GLOBAL PROPERTY COMPILE_DEFINITIONS FASTFORMAT_USE_WIDE_STRINGS)
endif(FASTFORMAT_USE_WIDE_STRINGS)

find_package(STLSoft 1.9.121 REQUIRED)
find_package(shwild QUIET)

# The library
add_subdirectory(src)

# Tests
if(BUILD_TESTING)
    find_package(xTests 0.18.4 QUIET)
    if(xTests_FOUND)
        add_subdirectory(test)
    else()
        message(AUTHOR_WARNING "The xTests framework not found. Cannot build tests.")
    endif()
endif()

# Examples
option(BUILD_EXAMPLES "Build the example programs" ON)
if(BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()

# #########################################################
# Export and install the project

string(TOLOWER ${PROJECT_NAME} EXPORT_NAME)

# Prepare a config and config-version files
include(CMakePackageConfigHelpers)
configure_package_config_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/cmake/${EXPORT_NAME}-config.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/cmake/${EXPORT_NAME}-config.cmake
  INSTALL_DESTINATION ${LIB_INSTALL_DIR}/${EXPORT_NAME}/cmake
)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/cmake/${EXPORT_NAME}-config-version.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion
)

export(EXPORT project-targets
    FILE "${CMAKE_CURRENT_BINARY_DIR}/cmake/${EXPORT_NAME}-targets.cmake"
    NAMESPACE "${PROJECT_NAME}::"
)

# Install to GNU type subdirs under CMAKE_INSTALL_PREFIX
install(EXPORT project-targets
    NAMESPACE "${PROJECT_NAME}::"
    FILE "${EXPORT_NAME}-targets.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${EXPORT_NAME}
)
install( FILES
    "${CMAKE_CURRENT_BINARY_DIR}/cmake/${EXPORT_NAME}-config.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/cmake/${EXPORT_NAME}-config-version.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${EXPORT_NAME}
)

# Store the current build directory in the CMake
# user package registry for package.
# This helps dependent projects use a package
# from the current project’s build tree,
# i.e. without installing it.
export(PACKAGE "${PROJECT_NAME}")

























# add_library( FastFormat
#   src/api.cpp
#   src/fmt_cache.cpp
#   src/fmt_spec_defect_handlers.cpp
#   src/init_code_strings.cpp
#   src/memory_pool.cpp
#   src/replacements.cpp
#   src/snprintf.cpp
# )
# generate_export_header( FastFormat )
# set_property(TARGET FastFormat PROPERTY VERSION ${FastFormat_VERSION})
# set_property(TARGET FastFormat PROPERTY SOVERSION 3)
# set_property(TARGET FastFormat PROPERTY
#   INTERFACE_FastFormat_MAJOR_VERSION 3)
# set_property(TARGET FastFormat APPEND PROPERTY
#   COMPATIBLE_INTERFACE_STRING FastFormat_MAJOR_VERSION
# )
#
# install(TARGETS FastFormat EXPORT FastFormatTargets
#   LIBRARY DESTINATION lib
#   ARCHIVE DESTINATION lib
#   RUNTIME DESTINATION bin
# )
# install(
#   DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/include/fastformat"
#   DESTINATION include
#   COMPONENT Devel
# )
#
# write_basic_package_version_file(
#   "${CMAKE_CURRENT_BINARY_DIR}/cmake/FastFormatConfigVersion.cmake"
#   VERSION ${FastFormat_VERSION}
#   COMPATIBILITY AnyNewerVersion
# )
#
# export(TARGETS FastFormat
#   FILE "${CMAKE_CURRENT_BINARY_DIR}/cmake/FastFormatTargets.cmake"
#   NAMESPACE FastFormat::
# )
# set(PACKAGE_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include")
# configure_file(cmake/FastFormatConfig.cmake.in
#   "${CMAKE_CURRENT_BINARY_DIR}/cmake/FastFormatConfig.cmake"
#   @ONLY
# )
# export(PACKAGE FastFormat)
#
# set(ConfigPackageLocation cmake/FastFormat)
# install(EXPORT FastFormatTargets
#   FILE FastFormatTargets.cmake
#   NAMESPACE FastFormat::
#   DESTINATION ${ConfigPackageLocation}
# )
# install( FILES
#     "${CMAKE_CURRENT_BINARY_DIR}/cmake/FastFormatConfig.cmake"
#     "${CMAKE_CURRENT_BINARY_DIR}/cmake/FastFormatConfigVersion.cmake"
#   DESTINATION ${ConfigPackageLocation}
#   COMPONENT Devel
# )
#
