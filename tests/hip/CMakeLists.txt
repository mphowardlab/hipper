# Copyright (c) 2020, Michael P. Howard
# Copyright (c) 2021, Auburn University
# This file is released under the Modified BSD License.

include(CheckLanguage)
check_language(HIP)
if(NOT CMAKE_HIP_COMPILER)
    message(STATUS "No HIP compiler, skipping HIP tests.")
    return()
else()
    enable_language(HIP)
endif()

add_executable(test_hipper_hip ${HIPPER_TEST_FILES} LANGUAGES HIP)
target_link_libraries(test_hipper_hip PRIVATE hipper::hipper Catch2::Catch2)
target_compile_definitions(test_hipper_hip PRIVATE HIPPER_HIP)

# set cuda architectures for build
set_property(TARGET test_hipper_hip PROPERTY HIP_PLATFORM nvidia HIP_ARCHITECTURES 75)

foreach(label ${HIPPER_TEST_LABELS})
    add_test(NAME hipper-hip-${label} COMMAND test_hipper_hip "[${label}]")
endforeach()
