find_package(OpenSSL 1.1.0 QUIET)

if(OPENSSL_FOUND)
  add_executable(issue75 issue-75.c)
  target_link_libraries(issue75 PRIVATE jose::jose OpenSSL::SSL OpenSSL::Crypto)
  
  add_test(NAME issue75 COMMAND issue75)
  set_tests_properties(issue75 PROPERTIES 
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    TIMEOUT 30
  )
endif()