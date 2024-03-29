cmake_minimum_required(VERSION 3.18)

#
# Define the library
#

project(simulacrum
        VERSION 1.0.0
        LANGUAGES CXX)

# specify file interface
set(simulacrum_SRCEXT hpp)

# define the library and its aliases
add_library(simulacrum INTERFACE)
add_library(sim::simulacrum ALIAS simulacrum)
set_target_properties(simulacrum PROPERTIES
    CXX_STANDARD 20
    CXX_STANDARD_REQUIRED true)

# link required external libraries
# find_package(spatula REQUIRED)
# target_link_libraries(simulacrum INTERFACE sp::spatula)

# add test sub-directory if specified
option(run_tests "Run tests" off)
if (run_tests)
    add_subdirectory(test)
endif()

#
# Export the library as a cmake package
#

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

# define what directories to include
target_include_directories(simulacrum INTERFACE
    $<BUILD_INTERFACE:${simulacrum_SOURCE_DIR}/src>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

# create the export targets
install(TARGETS simulacrum
        EXPORT simulacrum-targets
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})

# create the package-version
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/simulacrum-config-version.cmake
    VERSION ${simulacrum_VERSION}
    COMPATIBILITY AnyNewerVersion)

# generate the package configuration file
configure_package_config_file(
    simulacrum-config.cmake.in simulacrum-config.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/simulacrum)

# allow exporting from build tree
export(EXPORT simulacrum-targets
       FILE ${CMAKE_CURRENT_BINARY_DIR}/simulacrum-targets.cmake
       NAMESPACE sim::)

# install the exported targets
install(EXPORT simulacrum-targets
        FILE simulacrum-targets.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/simulacrum
        NAMESPACE sim::)

# install the config files
install(FILES
        ${CMAKE_CURRENT_BINARY_DIR}/simulacrum-config.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/simulacrum-config-version.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/simulacrum)

# install the header files
install(DIRECTORY src/
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
        FILES_MATCHING PATTERN "*.${simulacrum_SRCEXT}")

export(PACKAGE simulacrum)
