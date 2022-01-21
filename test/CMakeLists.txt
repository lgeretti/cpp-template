include(CTest)

set(UNIT_TESTS
    test_lib
)

foreach(TEST ${UNIT_TESTS})
    add_executable(${TEST} ${TEST}.cpp)
    target_link_libraries(${TEST} lib)
    add_test(${TEST} ${TEST})
endforeach()

add_custom_target(tests)
add_dependencies(tests ${UNIT_TESTS})
