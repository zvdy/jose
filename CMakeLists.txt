cmake_minimum_required(VERSION 3.10)
project(jose VERSION 14 LANGUAGES C)

# Enable testing
enable_testing()

option(BUILD_DOCS "Whether to build asciidoc manpages" ON)

# Set C standard
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS ON)

# Set compiler warnings
if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    add_compile_options(
        -Wall -Wextra
        -Wstrict-aliasing
        -Wchar-subscripts
        -Wformat-security
        -Wmissing-declarations
        -Wmissing-prototypes
        -Wnested-externs
        -Wpointer-arith
        -Wshadow
        -Wsign-compare
        -Wstrict-prototypes
        -Wtype-limits
        -Wunused-function
        -Wno-missing-field-initializers
        -Wno-unused-parameter
        -Wno-unknown-pragmas
    )
    if(CMAKE_BUILD_TYPE MATCHES "Debug|Release")
        add_compile_options(-Werror)
    endif()
endif()

# Find dependencies
find_package(ZLIB REQUIRED)
find_package(Threads REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(JANSSON REQUIRED jansson>=2.10)
pkg_check_modules(LIBCRYPTO REQUIRED libcrypto>=1.0.2)

# Optional programs
find_program(A2X_PROGRAM a2x)
find_program(JQ_PROGRAM jq)

if(NOT JQ_PROGRAM)
    message(STATUS "jq not found (unrequired but recommended)")
endif()

# License handling
set(LICENSE_FILES ${CMAKE_CURRENT_SOURCE_DIR}/COPYING)
if(CMAKE_SYSTEM_NAME MATCHES "FreeBSD")
    set(LICENSE_INSTALL_DIR "share/licenses/${PROJECT_NAME}-${PROJECT_VERSION}")
else()
    set(LICENSE_INSTALL_DIR "share/licenses/${PROJECT_NAME}")
endif()
install(FILES ${LICENSE_FILES} DESTINATION ${LICENSE_INSTALL_DIR})

# Process subdirectories
add_subdirectory(include)
add_subdirectory(lib)
add_subdirectory(cmd)
add_subdirectory(tests)

# Handle docs
if(BUILD_DOCS)
    if(A2X_PROGRAM)
        add_subdirectory(doc)
    else()
        message(WARNING "Will not build man pages due to missing dependencies!")
    endif()
endif()

# Configure pkg-config
set(PKG_CONFIG_NAME "José Library")
set(PKG_CONFIG_DESCRIPTION "Library for managing JOSE objects")
set(PKG_CONFIG_REQUIRES "jansson >= 2.10")
set(PKG_CONFIG_LIBS_PRIVATE "-lz -lcrypto")

configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/jose.pc.in
    ${CMAKE_CURRENT_BINARY_DIR}/jose.pc
    @ONLY
)

install(
    FILES ${CMAKE_CURRENT_BINARY_DIR}/jose.pc
    DESTINATION lib/pkgconfig
)