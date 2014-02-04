cmake_minimum_required(VERSION 2.6)

PROJECT(snappy)

set(SNAPPY_MAJOR 1)
set(SNAPPY_MINOR 1)
set(SNAPPY_PATCHLEVEL 1)

INCLUDE_DIRECTORIES(${snappy_SOURCE_DIR})

set(ac_cv_have_stdint_h 1)
set(ac_cv_have_stddef_h 1)

if(MSVC)
    ADD_DEFINITIONS(-DWIN32 -DHAVE_WINDOWS_H)
    set(ac_cv_have_sys_uio_h 0)
else()
    ADD_DEFINITIONS(-DHAVE_SYS_TIME_H -DHAVE_SYS_RESOURCE_H -DHAVE_SYS_MMAN_H)
    set(ac_cv_have_sys_uio_h 1)
endif()

configure_file(snappy-stubs-public.h.in ${snappy_SOURCE_DIR}/snappy-stubs-public.h @ONLY)

ADD_LIBRARY(snappy_static STATIC
    snappy-c.cc  snappy-sinksource.cc  snappy-stubs-internal.cc  snappy.cc)

ADD_LIBRARY(snappy SHARED
    snappy-c.cc  snappy-sinksource.cc  snappy-stubs-internal.cc  snappy.cc)

if(MSVC)
    set_target_properties (snappy PROPERTIES COMPILE_DEFINITIONS "SNAPPY_DLL")
    set_target_properties (snappy_static PROPERTIES COMPILE_DEFINITIONS "SNAPPY_STATIC_LIB")
endif()

ADD_EXECUTABLE(snappy_unittest snappy_unittest.cc snappy-test.cc)
target_link_libraries(snappy_unittest snappy)

enable_testing()
add_test(NAME snappy_test WORKING_DIRECTORY ${snappy_SOURCE_DIR} COMMAND snappy_unittest)

INSTALL(FILES snappy.h snappy-stubs-public.h
        DESTINATION include)
INSTALL(TARGETS snappy snappy_static
        RUNTIME DESTINATION bin 
        LIBRARY DESTINATION lib 
        ARCHIVE DESTINATION lib)
