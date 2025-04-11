configure_file(
  jose.h.in
  jose.h
  @ONLY
)

set(JOSE_HEADERS
  ${CMAKE_CURRENT_BINARY_DIR}/jose.h
  cfg.h
  io.h
  b64.h
  jwk.h
  jws.h
  jwe.h
  openssl.h
)

install(FILES ${JOSE_HEADERS} DESTINATION include/jose)