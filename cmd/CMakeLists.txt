set(JOSE_CMD_SOURCES
  jose.c
  b64/dec.c
  b64/enc.c
  jwk/eql.c
  jwk/exc.c
  jwk/gen.c
  jwk/pub.c
  jwk/thp.c
  jwk/use.c
  jws/fmt.c
  jws/sig.c
  jws/ver.c
  jwe/fmt.c
  jwe/dec.c
  jwe/enc.c
  alg.c
  fmt.c
)

set(JOSE_CMD_HEADERS
  jose.h
  b64/b64.h
  jwk/jwk.h
  jws/jws.h
  jwe/jwe.h
  jwe/pwd.h
)

# Renamed the executable target from "jose" to "jose_cmd" to avoid conflict with the library
add_executable(jose_cmd ${JOSE_CMD_SOURCES} ${JOSE_CMD_HEADERS})

target_link_libraries(jose_cmd PRIVATE jose::jose)

# Set the output name to be "jose" (without changing the target name)
set_target_properties(jose_cmd PROPERTIES OUTPUT_NAME jose)

install(TARGETS jose_cmd DESTINATION bin)