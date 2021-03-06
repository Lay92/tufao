find_package(Qt5Test REQUIRED)
enable_testing()

# Tests
set(tests
    ibytearray
    headers
    httpfileserver
    httppluginserver
    websocket
    sessionsettings
    httpserverrequestrouter
    httpupgraderouter
    asctime
    rfc1036
    rfc1123
    cryptography
    httpserverresponse
    dependencytree
)

macro(setup_test_target target)
    set_target_properties(
        "${target}"
        PROPERTIES
        AUTOMOC ON
    )

  target_include_directories("${target}"
    PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/../../3rd/boost.http/include"
    ${Boost_INCLUDE_DIR})

    # CXX_STANDARD was introduced in CMake 3.1
    if("${CMAKE_VERSION}" VERSION_GREATER "3.0")
        set_target_properties(
            "${target}"
            PROPERTIES
            CXX_STANDARD 11
            CXX_STANDARD_REQUIRED ON
        )
endif()


target_link_libraries("${target}" "${TUFAO_LIBRARY}" Qt5::Core Qt5::Network Qt5::Test)

    add_test(NAME "${target}"
        WORKING_DIRECTORY "${CMAKE_BINARY_DIR}/src"
        COMMAND $<TARGET_FILE:${target}>
    )
endmacro()

foreach(test ${tests})
    if(IS_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/${test}")
        add_subdirectory("${test}")
    else()
        add_executable("${test}" "${test}.cpp")
        setup_test_target("${test}")
    endif()
endforeach()

add_custom_target(tests ALL
    ctest --output-on-failure
    WORKING_DIRECTORY "${CMAKE_BINARY_DIR}"
    COMMENT "Running tests"
    DEPENDS ${tests}
)
