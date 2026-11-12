cmake_minimum_required(VERSION 3.16)

set(PKGCONF_VERSION "0.1.0" CACHE STRING "Version baked into pkgconf")

project(pkgconf
    VERSION ${PKGCONF_VERSION}
    LANGUAGES CXX
)

add_executable(pkgconf
    src/main.cpp
)

# CMake 3.28 does not treat GCC 16 as C++26-capable. cmdargs needs reflection.
target_compile_options(pkgconf PRIVATE -std=c++26 -freflection)

if ( PKGCONF_CMDARGS_ROOT )
    target_include_directories(pkgconf
        PRIVATE
            "${PKGCONF_CMDARGS_ROOT}/include"
    )
endif()

option(PKGCONF_STATIC_EXE
    "Link libgcc and libstdc++ statically (static variant)"
    OFF
)

if ( PKGCONF_STATIC_EXE AND MINGW )
    target_link_options(pkgconf PRIVATE -static -static-libgcc -static-libstdc++)
endif()

if ( WIN32 )
    enable_language(RC)
    set(PKGCONF_ICON "${CMAKE_CURRENT_SOURCE_DIR}/resources/pkgconf.ico")
    set(PKGCONF_VERSION_CSV "${PROJECT_VERSION_MAJOR},${PROJECT_VERSION_MINOR},${PROJECT_VERSION_PATCH},0")
    configure_file(
        resources/pkgconf.rc.in
        "${CMAKE_CURRENT_BINARY_DIR}/pkgconf.rc"
        @ONLY
    )
    target_sources(pkgconf PRIVATE "${CMAKE_CURRENT_BINARY_DIR}/pkgconf.rc")
    set_source_files_properties(
        "${CMAKE_CURRENT_BINARY_DIR}/pkgconf.rc"
        PROPERTIES
            OBJECT_DEPENDS "${PKGCONF_ICON}"
    )
endif()

install(TARGETS pkgconf RUNTIME DESTINATION bin)
