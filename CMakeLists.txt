cmake_minimum_required(VERSION 3.15)

project(Tetgen)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreadedDLL")
set(CMAKE_INSTALL_RPATH "$ORIGIN")
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH ON)
set(CMAKE_BUILD_RPATH_USE_ORIGIN ON)

add_library(tetgen STATIC include/tetgen.h src/tetgen.cxx src/predicates.cxx)
set_target_properties(tetgen 
    PROPERTIES
        POSITION_INDEPENDENT_CODE ON
)
target_compile_definitions(tetgen 
    PUBLIC
        TETLIBRARY
)
include(GNUInstallDirs)
target_include_directories(tetgen
    PUBLIC
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

include(GenerateExportHeader)
include(CMakePackageConfigHelpers)

if(WIN32)
    set(CMAKE_CONFIG_DESTINATION cmake)
else()
    set(CMAKE_CONFIG_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})
endif()

export(TARGETS tetgen
    NAMESPACE ${PROJECT_NAME}::
    FILE ${PROJECT_BINARY_DIR}/${CMAKE_CONFIG_DESTINATION}/tetgen_target.cmake
)

install(
    DIRECTORY ${PROJECT_SOURCE_DIR}/include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(TARGETS tetgen
    EXPORT tetgen
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

install(EXPORT tetgen
    FILE tetgen_target.cmake
    NAMESPACE ${PROJECT_NAME}::
    DESTINATION ${CMAKE_CONFIG_DESTINATION}
)

set(OUTPUT_CONFIG_FILE ${PROJECT_BINARY_DIR}/${CMAKE_CONFIG_DESTINATION}/${PROJECT_NAME}Config.cmake)
configure_package_config_file(
    ${PROJECT_NAME}Config.cmake.in 
    ${OUTPUT_CONFIG_FILE}
    INSTALL_DESTINATION ${CMAKE_CONFIG_DESTINATION}
)
install(
    FILES ${OUTPUT_CONFIG_FILE}
    DESTINATION ${CMAKE_CONFIG_DESTINATION}
)
