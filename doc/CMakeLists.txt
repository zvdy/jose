add_subdirectory(man)

# Install man pages
install(FILES
  doxygen/man/man3/jose_b64.3
  doxygen/man/man3/jose_jwk.3
  doxygen/man/man3/jose_jws.3
  doxygen/man/man3/jose_cfg.3
  doxygen/man/man3/jose_jwe.3
  doxygen/man/man3/jose_io_t.3
  doxygen/man/man3/jose_io.3
  DESTINATION share/man/man3
)