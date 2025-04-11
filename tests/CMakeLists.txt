include(CTest)

set(TEST_SCRIPTS
  jose-alg
  jose-fmt
  jose-b64-enc
  jose-b64-dec
  jose-jwk-eql
  jose-jwk-exc
  jose-jwk-gen
  jose-jwk-pub
  jose-jwk-use
  jose-jwk-thp
  jose-jws-fmt
  jose-jws-ver
  jose-jws-sig
  jose-jwe-fmt
  jose-jwe-dec
  jose-jwe-enc
)

set(TEST_PROGS
  alg_hash
  alg_sign
  alg_encr
  alg_wrap
  alg_comp
  api_b64
  api_jws
  api_jwe
)

# Build and add C test programs
foreach(prog ${TEST_PROGS})
  add_executable(${prog} ${prog}.c)
  target_link_libraries(${prog} PRIVATE jose::jose)
  
  # Set different timeout for api_b64 test
  if(${prog} STREQUAL "api_b64")
    add_test(NAME ${prog} COMMAND ${prog})
    set_tests_properties(${prog} PROPERTIES TIMEOUT 1800)
  else()
    add_test(NAME ${prog} COMMAND ${prog})
    set_tests_properties(${prog} PROPERTIES TIMEOUT 180)
  endif()
endforeach()

# Add script tests
foreach(script ${TEST_SCRIPTS})
  add_test(NAME ${script} COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/${script})
  set_tests_properties(${script} PROPERTIES
    # Updated path to use jose_cmd's output location (which is named 'jose')
    ENVIRONMENT "PATH=$<TARGET_FILE_DIR:jose_cmd>:$ENV{PATH};VECTORS=${CMAKE_CURRENT_SOURCE_DIR}/vectors;CVE_2023_50967=${CMAKE_CURRENT_SOURCE_DIR}/cve-2023-50967"
    TIMEOUT 900
  )
endforeach()

# Add subdirectory for issue-75
add_subdirectory(issue-75)