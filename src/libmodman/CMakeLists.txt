### Required
project(libmodman CXX)
set_project_version(2 0 1)

### Main library
# Flags / Definitions / Environment
if (WIN32)
  add_definitions(-D_CRT_SECURE_NO_WARNINGS=1)
else(WIN32)
  set(CMAKE_CXX_FLAGS "-fvisibility=hidden ${CMAKE_CXX_FLAGS}")
endif(WIN32)
include_directories(${CMAKE_SOURCE_DIR})

# PkgConfig file
if(NOT WIN32 AND NOT APPLE)
  set (PKGCONFIG_PREFIX ${CMAKE_INSTALL_PREFIX})
  
  if(IS_ABSOLUTE ${LIB_INSTALL_DIR})
    set (PKGCONFIG_LIBDIR ${LIB_INSTALL_DIR})
  else(IS_ABSOLUTE ${LIB_INSTALL_DIR})
    set (PKGCONFIG_LIBDIR "\${prefix}/${LIB_INSTALL_DIR}")
  endif (IS_ABSOLUTE ${LIB_INSTALL_DIR})

  if(IS_ABSOLUTE ${INCLUDE_INSTALL_DIR})
    set (PKGCONFIG_INCLUDEDIR ${INCLUDE_INSTALL_DIR})
  else(IS_ABSOLUTE ${INCLUDE_INSTALL_DIR})
    set (PKGCONFIG_INCLUDEDIR "\${prefix}/${INCLUDE_INSTALL_DIR}")
  endif(IS_ABSOLUTE ${INCLUDE_INSTALL_DIR})

  configure_file(libmodman-2.0.pc.in
                 ${CMAKE_CURRENT_BINARY_DIR}/libmodman-2.0.pc @ONLY)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libmodman-2.0.pc
          DESTINATION ${LIB_INSTALL_DIR}/pkgconfig)
endif()

# CMake Find helper
if (NOT WIN32 AND NOT APPLE)
  configure_file(Findlibmodman.cmake.in
                 ${CMAKE_CURRENT_BINARY_DIR}/Findlibmodman.cmake @ONLY)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/Findlibmodman.cmake
          DESTINATION ${SHARE_INSTALL_DIR}/cmake/Modules)
endif()

# Define the library itself
add_library(modman SHARED
            module.hpp
            module_manager.hpp
            module_manager.cpp)
if(NOT WIN32)
  target_link_libraries(modman dl)
endif()
set_target_properties(modman PROPERTIES PREFIX "lib" VERSION 1.0.0 SOVERSION 1)
install(TARGETS modman DESTINATION ${LIB_INSTALL_DIR})
install(FILES   module_manager.hpp module.hpp DESTINATION ${INCLUDE_INSTALL_DIR}/${PROJECT_NAME})

### Tests
add_testdirectory(test)
