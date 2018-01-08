cmake_minimum_required(VERSION 3.9)

project(MCSimMod)

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

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/..)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/../deps/${BUILD_TOOLS_ID}/${BUILD_TOOLS_VERSION}/libSBML/include)

link_directories(${CMAKE_CURRENT_SOURCE_DIR}/../deps/${BUILD_TOOLS_ID}/${BUILD_TOOLS_VERSION}/libSBML/lib)

add_definitions(-DLIBSBML_STATIC) # NB: requires static lib with non-static MSVC runtime

file(GLOB headers *.h)
file(GLOB source *.c)

add_executable(mod ${source} ${headers} ../config.h)

if (MSVC)
  
  add_definitions(
    -D_CRT_SECURE_NO_WARNINGS
    /wd4018 # 'expression' : signed/unsigned mismatch 
    /wd4267 # 'var' : conversion from 'size_t' to 'type', possible loss of data
    /wd4311 # 'variable' : pointer truncation from 'type' to 'type'
    /wd4312 # 'operation' : conversion from 'type1' to 'type2' of greater size 
    /wd4700 # uninitialized local variable 'name' used  ('pvm' in modisbml.c:754,1174)
    )

  target_link_libraries(mod expat.lib libbz2.lib libcheck.lib libiconv.lib libsbml-static.lib libxml2.lib xerces-c_3.lib zdll.lib Ws2_32.lib)

else ()

  target_link_libraries(mod libsbml-static.a libexpat.a liblibbz2.a)
  set_target_properties(mod PROPERTIES LINKER_LANGUAGE CXX)

endif ()

install(TARGETS mod DESTINATION "${BUILD_TOOLS_ID}/${BUILD_TOOLS_VERSION}")
set(CMAKE_INSTALL_PREFIX "${CMAKE_CURRENT_SOURCE_DIR}/../bin")
