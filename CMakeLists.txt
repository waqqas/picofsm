cmake_minimum_required(VERSION 3.10)

project(PicoFSM VERSION 0.0.1 DESCRIPTION "Minimilistic FSM.")

list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/modules")

set(CMAKE_CXX_STANDARD 17)


## Clang Tidy
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

list(APPEND RUN_CLANG_TIDY_BIN_ARGS
    -clang-tidy-binary ${CLANG_TIDY_BIN}
    -header-filter=.*
    -checks=clan*,cert*,misc*,perf*,cppc*,read*,mode*,-cert-err58-cpp,-misc-noexcept-move-constructor
)

add_custom_target(
    tidy
    COMMAND ${RUN_CLANG_TIDY_BIN} ${RUN_CLANG_TIDY_BIN_ARGS}
    COMMENT "running clang tidy"
)

##########

# pre-requisites
# find_package(Threads REQUIRED)
# find_package(RT REQUIRED)

file(GLOB HEADERS ${PROJECT_SOURCE_DIR}/include/*.h)
file(GLOB SOURCES ${PROJECT_SOURCE_DIR}/src/*.cpp ${PROJECT_SOURCE_DIR}/src/*.h)
add_library(${PROJECT_NAME} ${SOURCES} ${HEADERS})

target_include_directories(${PROJECT_NAME} PRIVATE ${PROJECT_SOURCE_DIR}/include)

set_target_properties(${PROJECT_NAME} PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(${PROJECT_NAME} PROPERTIES SOVERSION 1)
set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER "${HEADERS}")

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/${PROJECT_NAME}.pc.in ${PROJECT_NAME}.pc @ONLY)

option(BUILD_BENCHMARK "Build benchmark" ON)
if(BUILD_PERF)
    add_subdirectory(benchmark)
endif()

option(BUILD_EXAMPLES "Build examples" OFF)
if(BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()

option(BUILD_TEST "Build unit tests" ON)
if(BUILD_TEST)
    enable_testing()
    add_subdirectory(test)
endif()

include(GNUInstallDirs)

install(TARGETS ${PROJECT_NAME} ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install(FILES ${CMAKE_BINARY_DIR}/${PROJECT_NAME}.pc DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/pkgconfig)

