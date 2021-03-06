cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

option(lua_MSVC_STATIC_RUNTIME "Link static runtime libraries" ON)

if (MSVC AND lua_MSVC_STATIC_RUNTIME)
  foreach(flag_var
    CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
    CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
    if(${flag_var} MATCHES "/MD")
      string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
    endif(${flag_var} MATCHES "/MD")
  endforeach(flag_var)
endif (MSVC AND lua_MSVC_STATIC_RUNTIME)

project("lua")

if (${CMAKE_CXX_COMPILER_ID} STREQUAL MSVC)

  if($(CMAKE_BUILD_TYPE) STREQUAL DEBUG)
    add_definitions(-D_LIB)
    add_definitions(-D_DEBUG)
    add_definitions(-D_WIN32)

    set(CMAKE_CXX_FLAGS " /Od /EHsc /MTd /Zi /GR /nologo /RTC1")
    set(MSVC_LINK_FLAGS " /DEBUG /INCREMENTAL")
  else()
    add_definitions(-D_LIB)
    add_definitions(-D_WIN32)
    add_definitions(-DNDEBUG)

    set(CMAKE_CXX_FLAGS " /Ox /EHsc /MT /GL /GR /nologo")
    set(MSVC_LINK_FLAGS " /RELEASE /LTCG /INCREMENTAL:NO /NOLOGO")
  endif()

endif()

set( SOURCES_BASE
  src/lapi.c
  src/lauxlib.c
  src/lbaselib.c
  src/lbitlib.c
  src/lcode.c
  src/lcorolib.c
  src/lctype.c
  src/ldblib.c
  src/ldebug.c
  src/ldo.c
  src/ldump.c
  src/lfunc.c
  src/lgc.c
  src/linit.c
  src/liolib.c
  src/llex.c
  src/lmathlib.c
  src/lmem.c
  src/loadlib.c
  src/lobject.c
  src/lopcodes.c
  src/loslib.c
  src/lparser.c
  src/lstate.c
  src/lstring.c
  src/lstrlib.c
  src/ltable.c
  src/ltablib.c
  src/ltm.c
  src/lundump.c
  src/lutf8lib.c
  src/lvm.c
  src/lzio.c
)

set( SOURCE_HEADERS
  src/lapi.h
  src/lauxlib.h
  src/lcode.h
  src/lctype.h
  src/ldebug.h
  src/ldo.h
  src/lfunc.h
  src/lgc.h
  src/llex.h
  src/llimits.h
  src/lmem.h
  src/lobject.h
  src/lopcodes.h
  src/lparser.h
  src/lstate.h
  src/lstring.h
  src/ltable.h
  src/ltm.h
  src/lua.h
  src/lua.hpp
  src/luaconf.h
  src/lualib.h
  src/lundump.h
  src/lvm.h
  src/lzio.h
)

add_library(lua STATIC ${SOURCES_BASE} ${SOURCE_HEADERS})

include(GNUInstallDirs)

install(TARGETS lua
  EXPORT lua-export
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(FILES ${SOURCE_HEADERS} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(EXPORT lua-export
  FILE
  LuaTargets.cmake
  NAMESPACE
  lua::
  DESTINATION
  ${CMAKE_INSTALL_LIBDIR}/cmake/Lua
)
