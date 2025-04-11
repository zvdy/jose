if(A2X_PROGRAM)
  set(MAN_PAGES
    jose.1.adoc
    jose-alg.1.adoc
    jose-b64-dec.1.adoc
    jose-b64-enc.1.adoc
    jose-fmt.1.adoc
    jose-jwe-dec.1.adoc
    jose-jwe-enc.1.adoc
    jose-jwe-fmt.1.adoc
    jose-jwk-eql.1.adoc
    jose-jwk-exc.1.adoc
    jose-jwk-gen.1.adoc
    jose-jwk-pub.1.adoc
    jose-jwk-thp.1.adoc
    jose-jwk-use.1.adoc
    jose-jws-fmt.1.adoc
    jose-jws-sig.1.adoc
    jose-jws-ver.1.adoc
  )

  # Generate man pages from asciidoc files
  foreach(man ${MAN_PAGES})
    # Extract the output filename (remove .adoc extension)
    string(REGEX REPLACE "\\.adoc$" "" man_out ${man})
    
    add_custom_command(
      OUTPUT ${man_out}
      COMMAND ${A2X_PROGRAM} -f manpage -D ${CMAKE_CURRENT_BINARY_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/${man}
      DEPENDS ${man}
      COMMENT "Generating man page ${man_out}"
    )
    
    list(APPEND MAN_GENERATED ${CMAKE_CURRENT_BINARY_DIR}/${man_out})
    
    # Extract section number (last component after dot)
    string(REGEX REPLACE ".*\\.([0-9])$" "\\1" section ${man_out})
    
    # Add installation rule
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${man_out} DESTINATION share/man/man${section})
  endforeach()
  
  # Add a custom target to ensure man pages are built
  add_custom_target(man_pages ALL DEPENDS ${MAN_GENERATED})
endif()