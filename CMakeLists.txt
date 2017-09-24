cmake_minimum_required (VERSION 3.6)
project (yaml C)

set (YAML_VERSION_MAJOR 0)
set (YAML_VERSION_MINOR 1)
set (YAML_VERSION_PATCH 7)
set (YAML_VERSION_STRING "${YAML_VERSION_MAJOR}.${YAML_VERSION_MINOR}.${YAML_VERSION_PATCH}")

file (GLOB SRC src/*.c)

option (BUILD_SHARED_LIBS "Build shared libraries" ON)
option (BUILD_TESTS "Build tests" ON)


include_directories (include)
add_library (libyaml ${SRC})
target_compile_definitions (libyaml
    PUBLIC YAML_VERSION_MAJOR=${YAML_VERSION_MAJOR}
    PUBLIC YAML_VERSION_MINOR=${YAML_VERSION_MINOR}
    PUBLIC YAML_VERSION_PATCH=${YAML_VERSION_PATCH}
    PUBLIC YAML_VERSION_STRING="${YAML_VERSION_STRING}"
)
if (BUILD_SHARED_LIBS)
    target_compile_definitions(libyaml
        PRIVATE YAML_DECLARE_EXPORT
    )
else (BUILD_SHARED_LIBS)
    target_compile_definitions(libyaml
        PUBLIC YAML_DECLARE_STATIC
    )
endif (BUILD_SHARED_LIBS)


if (BUILD_TESTS)
    enable_testing()
    
    add_executable (test-version tests/test-version.c)
    target_link_libraries(test-version libyaml)
    add_test (NAME version COMMAND test-version)

    add_executable (test-reader tests/test-reader.c)
    target_link_libraries(test-reader libyaml)
    add_test (NAME reader COMMAND test-reader)
endif (BUILD_TESTS)


install(TARGETS libyaml
    EXPORT libyaml
    RUNTIME DESTINATION bin/$<CONFIG>
    ARCHIVE DESTINATION lib/$<CONFIG>
    INCLUDES DESTONATION include
)
install(FILES include/yaml.h DESTINATION include/)
install(EXPORT libyaml DESTINATION cmake FILE  libyaml-config.cmake)

