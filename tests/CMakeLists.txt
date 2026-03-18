# Copyright (c) 2020, Michael P. Howard
# Copyright (c) 2021, Auburn University
# This file is released under the Modified BSD License.

# enable the language used for testing
if(HIPPER_TEST_PLATFORM STREQUAL "CUDA")
    enable_language(CUDA)
elseif(HIPPER_TEST_PLATFORM STREQUAL "HIP")
    enable_language(HIP)
else()
    message(FATAL_ERROR "HIPPER_TEST_PLATFORM value ${HIPPER_TEST_PLATFORM} not recognized, must be CUDA or HIP")
endif()

# the list of all source files
set(HIPPER_TEST_FILES
    test_hipper.cc
    test_kernel.cu
    test_cub.cu
)
# the list of test labels to turn into executables
set(HIPPER_TEST_LABELS
    kernel
    CUB
)

# look for catch quietly, and pull down v2.13.10 if not found
find_package(Catch2 2 QUIET)
if(NOT Catch2_FOUND)
    message(STATUS "Downloading Catch2...")
    set(url https://github.com/catchorg/Catch2/releases/download/v2.13.10/catch.hpp)
    file(DOWNLOAD ${url} "${CMAKE_CURRENT_BINARY_DIR}/catch2/catch.hpp" STATUS status)
    list(GET status 0 error)
    if(error)
        message(FATAL_ERROR "... could not download from ${url}.")
    endif()
    set(Catch2_ROOT ${CMAKE_CURRENT_BINARY_DIR})
    find_package(Catch2 REQUIRED)
endif()

add_executable(test_hipper ${HIPPER_TEST_FILES})
target_link_libraries(test_hipper PRIVATE hipper::hipper Catch2::Catch2)
target_compile_definitions(test_hipper PRIVATE HIPPER_${HIPPER_TEST_PLATFORM})

foreach(label ${HIPPER_TEST_LABELS})
    add_test(NAME hipper-${label} COMMAND test_hipper "[${label}]")
endforeach()
