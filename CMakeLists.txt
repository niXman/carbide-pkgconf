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

install(TARGETS pkgconf RUNTIME DESTINATION bin)
