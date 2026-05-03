cmake_minimum_required(VERSION 3.15)

# 1. Buscar el paquete de OpenMP
find_package(OpenMP REQUIRED)

include_directories(${PNG_INCLUDE_DIR})
include_directories(${JPEG_INCLUDE_DIR})

add_library(utils utils/image.h utils/image.cc utils/dct.h utils/dct.cc)

# 2. Enlazar OpenMP::OpenMP_CXX a librería
target_link_libraries(utils PNG::PNG JPEG::JPEG OpenMP::OpenMP_CXX)
add_executable(detect main.cc)
target_link_libraries(detect utils OpenMP::OpenMP_CXX)
