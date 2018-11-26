#        Copyright The Authors 2018.
#    Distributed under the 3-Clause BSD License.
#    (See accompanying file LICENSE or copy at
#   https://opensource.org/licenses/BSD-3-Clause)

# ------------------------------------------------------------------------------
# Configuration
# ------------------------------------------------------------------------------

set(IDE_FOLDER "imgui")

# ------------------------------------------------------------------------------
# External dependencies
# ------------------------------------------------------------------------------

# find_package(THIRDPARTY REQUIRED)


# ==============================================================================
# Build instructions
# ==============================================================================

# Target name
set(target asap_imgui)

# Exit here if required dependencies are not met
message(STATUS "Lib ${target}")


# ------------------------------------------------------------------------------
# Sources
# ------------------------------------------------------------------------------

set(imgui_public_headers
  imgui/imgui.h
  )

set(imgui_private_headers
  imgui/imgui_internal.h
  imgui/imconfig.h
  imgui/imstb_rectpack.h
  imgui/imstb_textedit.h
  imgui/imstb_truetype.h
  )

set(imgui_sources
  imgui/imgui.cpp
  imgui/imgui_widgets.cpp
  imgui/imgui_draw.cpp
  imgui/imgui_demo.cpp
  ${imgui_public_headers}
  ${imgui_private_headers}
  )

set(public_headers
  ${imgui_public_headers}
  include/glad/khrplatform.h
  include/glad/glad.h
  )

set(private_headers
  ${imgui_private_headers}
  )

set(sources
  src/glad/glad.c
  src/glfw/imgui_impl_glfw.cpp
  src/glfw/imgui_impl_opengl3.cpp
  ${imgui_public_headers}
  ${imgui_private_headers}
  ${imgui_sources}
  )

# Group source files
set(header_group "Header Files")
set(private_header_group "Header Files\\Internal")
set(source_group "Source Files")
source_group(${header_group}
  FILES
    ${public_headers}
  REGULAR_EXPRESSION
    "\\\\.h$|\\\\.hpp$"
  )
source_group(${private_header_group}
  FILES
    ${private_headers}
  REGULAR_EXPRESSION
    "\\\\.h$|\\\\.hpp$"
  )
source_group(${source_group}
  FILES
    ${sources}
  REGULAR_EXPRESSION
    "\\\\.cpp$|\\\\.c$|\\\\.h$|\\\\.hpp$"
  )
source_group(${source_group}
  FILES
    ${sources}
  REGULAR_EXPRESSION
    "\\\\.cpp$|\\\\.c$|\\\\.h$|\\\\.hpp$"
  )


# ------------------------------------------------------------------------------
# Include dirs
# ------------------------------------------------------------------------------

set(include_dirs
  ${CMAKE_CURRENT_LIST_DIR}
  ${CMAKE_CURRENT_LIST_DIR}/include
  ${CMAKE_SOURCE_DIR}/third_party/glfw/include
  )

# ------------------------------------------------------------------------------
# Compile definitions / options
# ------------------------------------------------------------------------------

set(compile_definitions
  IMGUI_USER_CONFIG="../imgui_user_config.h"
  IMGUI_IMPL_OPENGL_LOADER_GLAD
  )
set(compile_options)

# ------------------------------------------------------------------------------
# Libraries
# ------------------------------------------------------------------------------

set(libraries asap::common glfw)


# ------------------------------------------------------------------------------
# Create targets
# ------------------------------------------------------------------------------

asap_library(
  TARGET
  ${target}
  EXPORT_NAME
  imgui
  SOURCES
  ${sources}
  PRIVATE_INCLUDE_DIRS
  ${include_dirs}
  PRIVATE_COMPILE_DEFINITIONS
  ${compile_definitions}
  PRIVATE_COMPILE_OPTIONS
  ${compile_options}
  PUBLIC_LIBRARIES
  ${libraries}
)

# Add dll specific defines for glad
target_compile_definitions(${target}
  PRIVATE
    $<$<BOOL:${BUILD_SHARED_LIBS}>:GLAD_GLAPI_EXPORT_BUILD>

  PUBLIC
    $<$<BOOL:${BUILD_SHARED_LIBS}>:GLAD_GLAPI_EXPORT>
  )


# Add imgui include dir manually as it is special
target_include_directories(${target} INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>)

# Handle custom installation of specific include headers
install(
  FILES
  ${CMAKE_CURRENT_SOURCE_DIR}/imgui/imconfig.h
  ${CMAKE_CURRENT_SOURCE_DIR}/imgui/imgui.h
  ${CMAKE_CURRENT_SOURCE_DIR}/imgui/imgui_internal.h
  DESTINATION
  ${INSTALL_INCLUDE}/imgui
  COMPONENT
  dev
  )


# ------------------------------------------------------------------------------
# Tests
# ------------------------------------------------------------------------------

#if (OPTION_BUILD_TESTS)
#  add_subdirectory(test)
#endif()


# ------------------------------------------------------------------------------
# Code analysis
# ------------------------------------------------------------------------------

#enable_cppcheck()
#tidy_target(${target})


# ------------------------------------------------------------------------------
# API Documentation
# ------------------------------------------------------------------------------

configure_doxyfile(${target}
  "\"Main\""
  "\"The application main entry point.\""
  "${source_path}")

add_doxygen_target(${target})

install(
  DIRECTORY ${DOXYGEN_BUILD_DIR}/${target}
  DESTINATION ${INSTALL_DOC}
  COMPONENT docs
)
