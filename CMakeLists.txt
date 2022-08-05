cmake_minimum_required(VERSION 3.0.0)
project(7f VERSION 0.1.0)

add_subdirectory(src)
add_subdirectory(lib)

add_custom_target(7f_all ALL DEPENDS main)

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)
