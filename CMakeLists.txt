cmake_minimum_required(VERSION 3.8)

option(BUILD_SHARED_LIBS "Enable compilation of shared libraries" OFF)

include(support/project_warnings.cmake)

add_executable(intro main.cpp)
target_compile_features(intro PRIVATE cxx_std_17)
target_link_libraries(intro PRIVATE  --coverage)
#enable_warnings(intro)

# make visual C++ set the proper value for the __cplusplus macro
if(MSVC)
  add_compile_options(-Zc:__cplusplus)
endif()

enable_testing()

add_executable(tester tester.cpp)
target_link_libraries(tester PRIVATE  --coverage)
add_test(Tester tester)
