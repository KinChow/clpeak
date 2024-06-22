cmake_minimum_required(VERSION 3.5)
project(clpeak)

include(${CMAKE_CURRENT_SOURCE_DIR}/cmake/common.cmake)
include(GNUInstallDirs)

if (NOT CMAKE_BUILD_TYPE)
  message(STATUS "Setting build type to Release")
  set(CMAKE_BUILD_TYPE "Release")
endif()

set(CMAKE_CXX_STANDARD "11")
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/output)

set(CLPEAK_SOURCE_FILES
    src/common.cpp
    src/clpeak.cpp
    src/options.cpp
    src/logger.cpp
    src/global_bandwidth.cpp
    src/compute_sp.cpp
    src/compute_hp.cpp
    src/compute_dp.cpp
    src/compute_integer.cpp
    src/compute_integer_fast.cpp
    src/compute_char.cpp
    src/compute_short.cpp
    src/transfer_bandwidth.cpp
    src/kernel_latency.cpp
    src/entry.cpp
)

include_directories(
  ${PROJECT_SOURCE_DIR}/include
  ${PROJECT_SOURCE_DIR}/src/kernels
)

add_subdirectory(${PROJECT_SOURCE_DIR}/android/app/src/main/cpp/libopencl-stub)

add_executable(clpeak ${CLPEAK_SOURCE_FILES})

target_link_libraries(clpeak PRIVATE OpenCL)
target_compile_definitions(clpeak PRIVATE CL_TARGET_OPENCL_VERSION=120)

if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang" OR
   CMAKE_CXX_COMPILER_ID STREQUAL "GNU"   OR
   CMAKE_CXX_COMPILER_ID STREQUAL "AppleClang")
  target_compile_options(clpeak PRIVATE -fPIC -Wall -Wextra -Wno-unused-parameter)
endif()

install(TARGETS clpeak RUNTIME DESTINATION bin)
install(FILES LICENSE DESTINATION "${CMAKE_INSTALL_DATAROOTDIR}/clpeak")

enable_testing()
add_test(clpeak_test_run clpeak)

set(CPACK_PACKAGE_FILE_NAME "${CMAKE_PROJECT_NAME}-${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_PROCESSOR}-${VERSION_STR}")
set(CPACK_GENERATOR "ZIP")
include (CPack)
