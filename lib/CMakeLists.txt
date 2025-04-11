set(LIBJOSE_SOURCES
  misc.c
  cfg.c
  io.c
  b64.c
  hsh.c
  hooks.c
  jwk.c
  jws.c
  jwe.c
  zlib/deflate.c
  openssl/aescbch.c
  openssl/aesgcm.c
  openssl/aesgcmkw.c
  openssl/aeskw.c
  openssl/compat.c
  openssl/dir.c
  openssl/ec.c
  openssl/ecdh.c
  openssl/ecdhes.c
  openssl/ecmr.c
  openssl/ecdsa.c
  openssl/hash.c
  openssl/hmac.c
  openssl/jwk.c
  openssl/lock.c
  openssl/misc.c
  openssl/oct.c
  openssl/pbes2.c
  openssl/rsa.c
  openssl/rsaes.c
  openssl/rsassa.c
)

set(LIBJOSE_HEADERS
  misc.h
  hsh.h
  hooks.h
  openssl/compat.h
  openssl/misc.h
)

add_library(jose SHARED ${LIBJOSE_SOURCES} ${LIBJOSE_HEADERS})

target_include_directories(jose
  PUBLIC
    ${CMAKE_SOURCE_DIR}/include
  PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
)

target_link_libraries(jose
  PUBLIC
    ${JANSSON_LIBRARIES}
  PRIVATE
    ZLIB::ZLIB
    ${LIBCRYPTO_LIBRARIES}
    Threads::Threads
)

target_include_directories(jose
  PUBLIC
    ${JANSSON_INCLUDE_DIRS}
  PRIVATE
    ${LIBCRYPTO_INCLUDE_DIRS}
    ${ZLIB_INCLUDE_DIRS}
)

# Version script for symbol visibility
if(CMAKE_SYSTEM_NAME MATCHES "Linux|FreeBSD")
  set(JOSE_VERSION_SCRIPT ${CMAKE_CURRENT_SOURCE_DIR}/libjose.map)
  
  # Check if the compiler supports version scripts
  include(CheckCCompilerFlag)
  
  if(CMAKE_SYSTEM_NAME MATCHES "FreeBSD")
    set(VERSION_FLAG_TEST "-Wl,--version-script=${JOSE_VERSION_SCRIPT},--undefined-version")
  else()
    set(VERSION_FLAG_TEST "-Wl,--version-script=${JOSE_VERSION_SCRIPT}")
  endif()
  
  check_c_compiler_flag("${VERSION_FLAG_TEST}" HAS_VERSION_SCRIPT)
  
  if(HAS_VERSION_SCRIPT)
    set_target_properties(jose PROPERTIES LINK_FLAGS "${VERSION_FLAG_TEST}")
  else()
    # Fall back to regex-based symbol exporting
    if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
      set_target_properties(jose PROPERTIES LINK_FLAGS "-Wl,-export-symbols-regex=^jose_.*")
    endif()
  endif()
endif()

set_target_properties(jose PROPERTIES
  VERSION 0.0.0
  SOVERSION 0
)

# Make the jose library available to other subdirectories
add_library(jose::jose ALIAS jose)

install(TARGETS jose
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)