cmake_minimum_required(VERSION 3.9)

project(MCSimSim)

if (NOT (${CMAKE_SIZEOF_VOID_P} EQUAL "8"))
  message(FATAL_ERROR "64 bit only.")
endif ()

if (MSVC)
  set (BUILD_TOOLS_ID "VS")
  set (BUILD_TOOLS_VERSION "v15x64")
elseif (MINGW)
  set (BUILD_TOOLS_ID "mingw-w64")
  set (BUILD_TOOLS_VERSION "x86_64-7.2.0-posix-seh-rt_v5-rev1")
else ()
  message(FATAL_ERROR "Unsupported compiler.")
endif ()

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/.)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/..)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../deps/${BUILD_TOOLS_ID}/${BUILD_TOOLS_VERSION}/gsl/include)

link_directories(${CMAKE_CURRENT_SOURCE_DIR}/../deps/${BUILD_TOOLS_ID}/${BUILD_TOOLS_VERSION}/gsl/lib)

file(GLOB headers *.h)
file(GLOB source *.c)

add_executable(sim ${source} ${headers} ../config.h ../deps/model/src/model.c)

if (MSVC)
  
  add_definitions(
    -D_CRT_SECURE_NO_WARNINGS
    /wd4244 # 'conversion' conversion from 'type1' to 'type2', possible loss of data
    /wd4267 # 'var' : conversion from 'size_t' to 'type', possible loss of data
    )

  target_link_libraries(sim gslcblas.lib gsl.lib)

else ()

  target_link_libraries(sim gslcblas.a gsl.a)
  set_target_properties(sim PROPERTIES LINKER_LANGUAGE CXX)

endif ()

install(TARGETS sim DESTINATION "${BUILD_TOOLS_ID}/${BUILD_TOOLS_VERSION}")
set(CMAKE_INSTALL_PREFIX "${CMAKE_CURRENT_SOURCE_DIR}/../bin")
