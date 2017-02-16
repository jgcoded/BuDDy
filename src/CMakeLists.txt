cmake_minimum_required(VERSION 2.8)

set(HEADERS
  config.h
  bdd.h
  fdd.h
  bvec.h
  bddtree.h
  imatrix.h
  cache.h
  kernel.h
  prime.h
)

set(SOURCES
 bddio.c
 bddop.c
 bvec.c
 cache.c
 cppext.cxx
 fdd.c
 imatrix.c
 kernel.c
 pairs.c
 prime.c
 reorder.c
 tree.c
)

configure_file(${BUDDY_ROOT_DIR}/config.h.in config.h COPYONLY)
file(APPEND ${CMAKE_CURRENT_BINARY_DIR}/config.h
  "\n#define PACKAGE_VERSION \"${BUDDY_VERSION_MAJOR}.${BUDDY_VERSION_MINOR}\"")
file(APPEND ${CMAKE_CURRENT_BINARY_DIR}/config.h
  "\n#define MAJOR_VERSION ${BUDDY_VERSION_MAJOR}")
file(APPEND ${CMAKE_CURRENT_BINARY_DIR}/config.h
  "\n#define MINOR_VERSION ${BUDDY_VERSION_MINOR}")

add_library(${BUDDY_TARGET} ${HEADERS} ${SOURCES})
set_target_properties(${BUDDY_TARGET} PROPERTIES FOLDER BuDDy)
target_include_directories(${BUDDY_TARGET} PRIVATE ${CMAKE_CURRENT_BINARY_DIR})


if(WIN32)
  target_compile_definitions(${BUDDY_TARGET} PRIVATE
    _CRT_SECURE_NO_WARNINGS)
endif()

OPTION(BUDDY_DISABLE_WARNINGS "Disable Warnings from BuDDy" ON)
if(BUDDY_DISABLE_WARNINGS)
  if(MSVC)
    # Force to always compile with W4
    if(CMAKE_CXX_FLAGS MATCHES "/W[0-4]")
      string(REGEX REPLACE "/W[0-4]" "/W0" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
    else()
      set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W0")
    endif()
  elseif(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_GNUCXX)
    # Update if necessary
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -w")
  endif()
endif()
