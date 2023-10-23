cmake_minimum_required(VERSION 3.1.2)

project(h264bsd VERSION 2.0.0)

option(DISABLE_PKGCONFIG "Disable PkgConfig module generation" OFF)
option(DISABLE_CMAKE_CONFIG "Disable CMake package config module generation" OFF)
option(ENABLE_WIN32_IO "Build the Windows I/O helper class" OFF)
option(BUILD_SHARED_LIBS "Build h264bsd as a shared library" OFF)

include(GNUInstallDirs)


set(libh264bsd_SOURCES
    src/h264bsd_byte_stream.c
    src/h264bsd_deblocking.c
    src/h264bsd_image.c
    src/h264bsd_macroblock_layer.c
    src/h264bsd_pic_order_cnt.c
    src/h264bsd_sei.c
    src/h264bsd_slice_group_map.c
    src/h264bsd_stream.c
    src/h264bsd_vlc.c
    src/h264bsd_cavlc.c
    src/h264bsd_decoder.c
    src/h264bsd_inter_prediction.c
    src/h264bsd_nal_unit.c
    src/h264bsd_pic_param_set.c
    src/h264bsd_seq_param_set.c
    src/h264bsd_slice_header.c
    src/h264bsd_transform.c
    src/h264bsd_vui.c
    src/h264bsd_conceal.c
    src/h264bsd_dpb.c
    src/h264bsd_intra_prediction.c
    src/h264bsd_neighbour.c
    src/h264bsd_reconstruct.c
    src/h264bsd_slice_data.c
    src/h264bsd_storage.c
    src/h264bsd_util.c
)

set(libh264bsd_PUBLIC_HEADERS
    src/basetype.h
    src/h264bsd_cfg.h
    src/h264bsd_deblocking.h
    src/h264bsd_image.h
    src/h264bsd_macroblock_layer.h
    src/h264bsd_pic_order_cnt.h
    src/h264bsd_sei.h
    src/h264bsd_slice_group_map.h
    src/h264bsd_stream.h
    src/h264bsd_vlc.h
    src/h264bsd_byte_stream.h
    src/h264bsd_conceal.h
    src/h264bsd_decoder.h
    src/h264bsd_inter_prediction.h
    src/h264bsd_nal_unit.h
    src/h264bsd_pic_param_set.h
    src/h264bsd_seq_param_set.h
    src/h264bsd_slice_header.h
    src/h264bsd_transform.h
    src/h264bsd_vui.h
    src/h264bsd_cavlc.h
    src/h264bsd_container.h
    src/h264bsd_dpb.h
    src/h264bsd_intra_prediction.h
    src/h264bsd_neighbour.h
    src/h264bsd_reconstruct.h
    src/h264bsd_slice_data.h
    src/h264bsd_storage.h
    src/h264bsd_util.h
)

add_library(h264bsd ${libh264bsd_SOURCES} ${libh264bsd_PUBLIC_HEADERS})
set_target_properties(h264bsd PROPERTIES
  VERSION 6.0.0
  SOVERSION 6
#  CXX_VISIBILITY_PRESET hidden
#  VISIBILITY_INLINES_HIDDEN ON
)
target_include_directories(h264bsd
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)
if(WIN32)
  target_compile_definitions(h264bsd PRIVATE _CRT_SECURE_NO_WARNINGS)
endif()

#include(TestBigEndian)
#test_big_endian(BUILD_BIG_ENDIAN)
#if(BUILD_BIG_ENDIAN)
#  target_compile_definitions(h264bsd PRIVATE BUILD_BIG_ENDIAN)
#else()
#  target_compile_definitions(h264bsd PRIVATE BUILD_LITTLE_ENDIAN)
#endif()

#include(GenerateExportHeader)
#generate_export_header(h264bsd EXPORT_MACRO_NAME EBML_DLL_API)
#target_sources(h264bsd
#  PRIVATE
#    ${CMAKE_CURRENT_BINARY_DIR}/h264bsd_export.h
#)

if(NOT BUILD_SHARED_LIBS)
  target_compile_definitions(h264bsd PUBLIC h264bsd_STATIC_DEFINE)
endif()

install(TARGETS h264bsd
  EXPORT h264bsdTargets
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})

install(FILES ${libh264bsd_PUBLIC_HEADERS} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/h264bsd)
#install(FILES ${CMAKE_CURRENT_BINARY_DIR}/h264bsd_export.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/h264bsd)

#if(NOT DISABLE_PKGCONFIG)
#  set(prefix ${CMAKE_INSTALL_PREFIX})
#  set(exec_prefix "\$\{prefix\}")
#  if (IS_ABSOLUTE ${CMAKE_INSTALL_LIBDIR})
#    set(libdir "${CMAKE_INSTALL_LIBDIR}")
#  else()
#    set(libdir "\$\{prefix\}/${CMAKE_INSTALL_LIBDIR}")
#  endif()
#  if (IS_ABSOLUTE ${CMAKE_INSTALL_INCLUDEDIR})
#    set(includedir "${CMAKE_INSTALL_INCLUDEDIR}")
#  else()
#    set(includedir "\$\{prefix\}/${CMAKE_INSTALL_INCLUDEDIR}")
#  endif()
#  set(PACKAGE_VERSION ${PROJECT_VERSION})
#
#  get_target_property(EBML_DEFINITIONS h264bsd INTERFACE_COMPILE_DEFINITIONS)
#  if(NOT "${EBML_DEFINITIONS}" STREQUAL "EBML_DEFINITIONS-NOTFOUND")
#    set(EBML_DEFINITIONS "-D${EBML_DEFINITIONS}")
#    list(JOIN EBML_DEFINITIONS " -D" EBML_DEFINITIONS)
#  endif()
#
#  configure_file(libh264bsd.pc.in libh264bsd.pc @ONLY)
#  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libh264bsd.pc DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)
#endif()

if(NOT DISABLE_CMAKE_CONFIG)
  set(CMAKE_INSTALL_PACKAGEDIR ${CMAKE_INSTALL_LIBDIR}/cmake/h264bsd)
  include(CMakePackageConfigHelpers)
  configure_package_config_file(h264bsdConfig.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/h264bsdConfig.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_PACKAGEDIR})
  write_basic_package_version_file(h264bsdConfigVersion.cmake COMPATIBILITY SameMajorVersion)
  install(EXPORT h264bsdTargets NAMESPACE h264bsd:: DESTINATION ${CMAKE_INSTALL_PACKAGEDIR})
  install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/h264bsdConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/h264bsdConfigVersion.cmake
      DESTINATION ${CMAKE_INSTALL_PACKAGEDIR})
endif()
