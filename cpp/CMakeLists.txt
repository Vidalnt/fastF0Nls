cmake_minimum_required(VERSION 3.15)
project(fastF0Nls)

# Configuración específica para Windows
if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
    set(CMAKE_SHARED_LIBRARY_PREFIX "")
    set(CMAKE_SHARED_LIBRARY_SUFFIX ".dll")
    # Definir FFTW_DLL para compilar con soporte de DLL
    add_compile_definitions(FFTW_DLL)
endif()

# Estándar C++
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Fuentes
file(GLOB SOURCES "src/*.cpp")

# Crear biblioteca compartida
add_library(single_pitch SHARED ${SOURCES})

# Directorios de inclusión
target_include_directories(single_pitch PRIVATE src)

# Definiciones de preprocesador necesarias
target_compile_definitions(single_pitch PRIVATE
    DOUBLE
    CHEBYSHEV
    LIB
)

# Configuración de compilación
if(MSVC)
    target_compile_options(single_pitch PRIVATE
        /O2
        /W3
        /EHsc
    )
else()
    target_compile_options(single_pitch PRIVATE
        -O3
        -march=native
        -fPIC
        -Wno-write-strings
    )
endif()

# Enlazar con FFTW3
find_package(FFTW3 REQUIRED)
target_link_libraries(single_pitch PRIVATE FFTW3::fftw3)
