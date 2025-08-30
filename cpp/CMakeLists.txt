cmake_minimum_required(VERSION 3.15)
project(fastF0Nls)

if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
    set(CMAKE_SHARED_LIBRARY_PREFIX "")
    set(CMAKE_SHARED_LIBRARY_SUFFIX ".dll")
    add_compile_definitions(FFTW_DLL)
endif()

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

file(GLOB SOURCES "src/*.cpp")

add_library(single_pitch SHARED ${SOURCES})

target_include_directories(single_pitch PRIVATE src)

target_compile_definitions(single_pitch PRIVATE
    DOUBLE
    CHEBYSHEV
    LIB
)

if(WIN32)
    target_compile_definitions(single_pitch PRIVATE
        _USE_MATH_DEFINES
        NOMINMAX
        _CRT_SECURE_NO_WARNINGS
        WIN32_LEAN_AND_MEAN
    )
endif()

if(MSVC)
    target_compile_options(single_pitch PRIVATE
        /O2
        /W3
        /EHsc
        /bigobj
        /permissive-
    )
else()
    target_compile_options(single_pitch PRIVATE
        -O3
        -march=native
        -fPIC
        -Wno-write-strings
    )
endif()

find_package(FFTW3 REQUIRED)
target_link_libraries(single_pitch PRIVATE FFTW3::fftw3)

if(NOT WIN32)
    find_package(Threads REQUIRED)
    target_link_libraries(single_pitch PRIVATE Threads::Threads)
endif()
