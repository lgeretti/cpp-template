cmake_minimum_required(VERSION 3.9)

project(CppTemplate VERSION 1.0)

if(CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
    if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 19.20)
        message(FATAL_ERROR "MSVC version must be at least 19.20!")
    endif()
elseif(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 10.2)
        message(FATAL_ERROR "GCC version must be at least 10.2!")
    endif()
elseif (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 11.0)
        message(FATAL_ERROR "Clang version must be at least 11.0!")
    endif()
else()
    message(WARNING "You are using an unsupported compiler! MSVC, GCC and Clang are supported.")
endif()

if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
    set(LIBRARY_KIND STATIC)
else()
    set(LIBRARY_KIND SHARED)	
endif()

set(CMAKE_MACOSX_RPATH 1)
set(BUILD_SHARED_LIBS ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if(NOT WIN32)
    set(ANY_TARGET_WARN all extra pedantic sign-conversion cast-qual disabled-optimization
        init-self missing-include-dirs sign-promo switch-default undef redundant-decls
        strict-aliasing unused-parameter shadow error)
    foreach(WARN ${ANY_TARGET_WARN})
        add_compile_options(-W${WARN})
    endforeach()
else()
    add_compile_options(/WX)
endif()

find_package(Threads REQUIRED)

include_directories(SYSTEM ${PROJECT_SOURCE_DIR}/include)

add_subdirectory(src)

add_library(lib ${LIBRARY_KIND} $<TARGET_OBJECTS:CPPTEMPLATE_SRC>)
target_link_libraries(lib ${CMAKE_THREAD_LIBS_INIT})

add_executable(exec src/exec.cpp)
target_link_libraries(exec lib)

option(COVERAGE "Enable coverage reporting" OFF)
add_library(coverage_config INTERFACE)
if(COVERAGE)
    message(STATUS "Enabled coverage reporting")
    target_compile_options(coverage_config INTERFACE -O0 -g --coverage)
    if(CMAKE_VERSION VERSION_GREATER_EQUAL 3.13)
        target_link_options(coverage_config INTERFACE --coverage)
    else()
        target_link_libraries(coverage_config INTERFACE --coverage)
    endif()
    target_link_libraries(lib coverage_config)
endif()

enable_testing()
add_subdirectory(test)
