cmake_minimum_required(VERSION 3.8)

option(BUILD_SHARED_LIBS "Enable compilation of shared libraries" OFF)
option(CPP_STARTER_USE_QT "Enable compilation of QT sample" OFF)
option(CPP_STARTER_USE_FLTK "Enable compilation of FLTK sample" OFF)
option(CPP_STARTER_USE_GTKMM "Enable compilation of GTKMM sample" OFF)
option(CPP_STARTER_USE_IMGUI "Enable compilation of ImGui sample" OFF)
option(CPP_STARTER_USE_NANA "Enable compilation of Nana GUI sample" OFF)

include(support/project_warnings.cmake)

add_executable(intro main.cpp)
target_compile_features(intro PRIVATE cxx_std_17)
target_link_libraries(intro PRIVATE project_warnings --coverage)

enable_testing()

add_executable(tester tester.cpp)
target_link_libraries(tester PRIVATE project_warnings --coverage)
add_test(Tester tester)

# qt
if(CPP_STARTER_USE_QT)
  message("Using Qt")
  add_subdirectory(qt)
endif()

# fltk test
if(CPP_STARTER_USE_FLTK)
  find_package(FLTK REQUIRED)
  add_executable(test_fltk fltk/test_fltk.cpp)
  target_link_libraries(test_fltk PRIVATE project_warnings ${FLTK_LIBRARIES})
  target_include_directories(test_fltk PRIVATE ${FLTK_INCLUDE_DIR})
endif()

# gtkmm test
if(CPP_STARTER_USE_GTKMM)
  find_package(PkgConfig REQUIRED)
  pkg_check_modules(GTKMM REQUIRED IMPORTED_TARGET gtkmm-3.0)
  add_executable(test_gtkmm gtkmm/main.cpp gtkmm/hello_world.cpp)
  target_link_libraries(test_gtkmm PRIVATE project_warnings PkgConfig::GTKMM)
endif()

# imgui example
if(CPP_STARTER_USE_IMGUI)
  find_package(SFML COMPONENTS graphics window system)
  find_package(OpenGL)

  # imgui + sfml built as a lib, intentionally not using full warning flags
  add_library(imgui imgui/lib/imgui.cpp imgui/lib/imgui_draw.cpp imgui/lib/imgui-SFML.cpp)
  target_link_libraries(imgui INTERFACE ${SFML_LIBRARIES} ${OPENGL_gl_LIBRARY})

  # imgui test executable, with full warnings enabled
  add_executable(test_imgui imgui/test.cpp)
  target_link_libraries(test_imgui PRIVATE project_warnings imgui)
  target_include_directories(test_imgui PRIVATE ${SFML_INCLUDE_DIR})
endif()

# Nana
if(CPP_STARTER_USE_NANA)
  include(ExternalProject)
  externalproject_add(Nana
                      GIT_REPOSITORY
                      https://github.com/cnjinhao/nana.git
                      GIT_TAG
                      v1.5.6
                      CMAKE_CACHE_ARGS
                      "-DNANA_CMAKE_SHARED_LIB"
                      INSTALL_COMMAND
                      "")

  # ExternalProject_Get_Property(Nana NANA_INCLUDE_DIR)
  externalproject_get_property(Nana SOURCE_DIR BINARY_DIR)
  add_executable(test_nana nana/main.cpp)
  target_include_directories(test_nana PRIVATE ${SOURCE_DIR}/include)
  target_link_libraries(test_nana PRIVATE ${BINARY_DIR}/libnana.so ${NANA_LINKS})
endif()
