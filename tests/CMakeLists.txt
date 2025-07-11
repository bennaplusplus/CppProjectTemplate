include(Catch)

add_executable("UnitTestFoo" "test_foo.cc")
target_link_libraries("UnitTestFoo" PUBLIC "LibFoo")
target_link_libraries("UnitTestFoo" PRIVATE Catch2::Catch2WithMain)

add_executable("UnitTestBar" "test_bar.cc")
target_link_libraries("UnitTestBar" PUBLIC "LibBar")
target_link_libraries("UnitTestBar" PRIVATE Catch2::Catch2WithMain)

if(${ENABLE_WARNINGS})
    target_set_warnings(
        TARGET
        "UnitTestFoo"
        ENABLE
        ${ENABLE_WARNINGS}
        AS_ERRORS
        ${ENABLE_WARNINGS_AS_ERRORS})
    target_set_warnings(
        TARGET
        "UnitTestBar"
        ENABLE
        ${ENABLE_WARNINGS}
        AS_ERRORS
        ${ENABLE_WARNINGS_AS_ERRORS})
endif()

catch_discover_tests("UnitTestFoo")
catch_discover_tests("UnitTestBar")

if(ENABLE_COVERAGE)
    set(COVERAGE_MAIN "coverage")
    set(COVERAGE_EXCLUDES
        "${CMAKE_SOURCE_DIR}/app/.*"
        "${CMAKE_SOURCE_DIR}/cmake/.*"
        "${CMAKE_SOURCE_DIR}/docs/.*"
        "${CMAKE_SOURCE_DIR}/external/.*"
        "${CMAKE_SOURCE_DIR}/tests/.*"
        "${CMAKE_BINARY_DIR}/.*"
        "/usr/include/*")
    set(COVERAGE_EXTRA_FLAGS)
    set(COVERAGE_DEPENDENCIES "UnitTestFoo" "UnitTestBar")

    setup_target_for_coverage_gcovr_html(
        NAME
        ${COVERAGE_MAIN}
        EXCLUDE
        ${COVERAGE_EXCLUDES}
        EXECUTABLE
        ctest
        DEPENDENCIES
        ${COVERAGE_DEPENDENCIES}
        BASE_DIRECTORY
        ${CMAKE_SOURCE_DIR})
endif()
