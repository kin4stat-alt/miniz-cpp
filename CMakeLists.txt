cmake_minimum_required(VERSION 3.2)
project(miniz-cpp)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(TEST_SOURCE ${CMAKE_CURRENT_SOURCE_DIR}/tests/test.cpp)

add_library(miniz-cpp INTERFACE)

target_include_directories(miniz-cpp INTERFACE
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

add_executable(miniz-cpp.test ${TEST_SOURCE})

target_link_libraries(miniz-cpp.test PRIVATE miniz-cpp)

set(EXAMPLE_SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/examples/pipe.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/examples/read.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/examples/write.cpp)

foreach(EXAMPLE_SOURCE IN ITEMS ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    set(EXAMPLE_EXECUTABLE example-${EXAMPLE_NAME})

    add_executable(${EXAMPLE_EXECUTABLE} ${EXAMPLE_SOURCE})

    target_include_directories(${EXAMPLE_EXECUTABLE}
        PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
		
	target_link_libraries(${EXAMPLE_EXECUTABLE} PRIVATE miniz-cpp)
endforeach()

# install

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    "${PROJECT_BINARY_DIR}/miniz-cppConfigVersion.cmake"
    VERSION 1.0
    COMPATIBILITY AnyNewerVersion
)

install(TARGETS miniz-cpp
    EXPORT miniz-cppTargets
    LIBRARY DESTINATION lib COMPONENT Runtime
    ARCHIVE DESTINATION lib COMPONENT Development
    RUNTIME DESTINATION bin COMPONENT Runtime
    PUBLIC_HEADER DESTINATION include COMPONENT Development
    BUNDLE DESTINATION bin COMPONENT Runtime
)

include(CMakePackageConfigHelpers)

configure_package_config_file(
    "${PROJECT_SOURCE_DIR}/cmake/miniz-cppConfig.cmake.in"
    "${PROJECT_BINARY_DIR}/miniz-cppConfig.cmake"
    INSTALL_DESTINATION lib/cmake/miniz-cpp
)

install(EXPORT miniz-cppTargets DESTINATION share/miniz-cpp)
install(FILES "${PROJECT_BINARY_DIR}/miniz-cppConfigVersion.cmake"
              "${PROJECT_BINARY_DIR}/miniz-cppConfig.cmake"
        DESTINATION lib/cmake/miniz-cpp)
install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/ DESTINATION include)