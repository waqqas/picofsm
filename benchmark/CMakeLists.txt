project(Benchmark)

set(CMAKE_CXX_STANDARD 17)

include(ExternalProject)

set(EXTERNAL_INSTALL_LOCATION ${CMAKE_BINARY_DIR}/external)

ExternalProject_Add(benchmark 
    GIT_REPOSITORY https://github.com/google/benchmark
    CMAKE_ARGS 
              -DCMAKE_INSTALL_PREFIX=${EXTERNAL_INSTALL_LOCATION} 
              -DBENCHMARK_ENABLE_GTEST_TESTS=OFF
              -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
)

include_directories(${CMAKE_SOURCE_DIR}/src)
include_directories(${EXTERNAL_INSTALL_LOCATION}/include)
link_directories(${EXTERNAL_INSTALL_LOCATION}/lib)

file(GLOB APP_SOURCES ${CMAKE_SOURCE_DIR}/src/*.cpp ${CMAKE_SOURCE_DIR}/src/*.h)
file(GLOB TEST_SOURCES ${CMAKE_SOURCE_DIR}/benchmark/*.cpp ${CMAKE_SOURCE_DIR}/benchmark/*.h)

add_executable(${PROJECT_NAME} ${APP_SOURCES} ${TEST_SOURCES})
add_dependencies(${PROJECT_NAME} benchmark)
target_link_libraries(${PROJECT_NAME} libbenchmark.a )
target_link_libraries(${PROJECT_NAME} pthread)
