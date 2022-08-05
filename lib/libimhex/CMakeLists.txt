cmake_minimum_required(VERSION 3.16)
project(libimhex)

set(CMAKE_CXX_STANDARD 20)

set(BUILD_SHARED_LIBS OFF)



# Find packages
find_package(PkgConfig REQUIRED)

find_package(nlohmann_json REQUIRED)
set(NLOHMANN_JSON_LIBRARIES nlohmann_json::nlohmann_json)

if(NOT USE_SYSTEM_FMT)
  add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../fmt ${CMAKE_CURRENT_BINARY_DIR}/fmt EXCLUDE_FROM_ALL)
  set_target_properties(fmt PROPERTIES POSITION_INDEPENDENT_CODE ON)
  set(FMT_LIBRARIES fmt-header-only)
else()
  find_package(fmt 8.0.0 REQUIRED)
  set(FMT_LIBRARIES fmt::fmt)
endif()

find_package(PkgConfig REQUIRED)
pkg_check_modules(LIBCURL REQUIRED IMPORTED_TARGET libcurl>=7.78.0)

find_package(LLVM REQUIRED Demangle)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")

if (WIN32)
    set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,--export-all-symbols")
endif()

set(CMAKE_SHARED_LIBRARY_PREFIX "")

set(LIBIMHEX_SOURCES
    source/api/event.cpp
    source/api/imhex_api.cpp
    source/api/content_registry.cpp
    source/api/task.cpp
    source/api/keybinding.cpp
    source/api/localization.cpp

    source/data_processor/attribute.cpp
    source/data_processor/link.cpp
    source/data_processor/node.cpp

    source/helpers/utils.cpp
    source/helpers/fs.cpp
    source/helpers/net.cpp
    source/helpers/file.cpp
    source/helpers/socket.cpp
    source/helpers/patches.cpp
    source/helpers/project_file_handler.cpp
    source/helpers/encoding_file.cpp
    source/helpers/logger.cpp

    source/providers/provider.cpp

    source/ui/imgui_imhex_extensions.cpp
    source/ui/view.cpp
    )


add_compile_definitions(IMHEX_PROJECT_NAME="${PROJECT_NAME}")

add_library(libimhex SHARED ${LIBIMHEX_SOURCES})
set_target_properties(libimhex PROPERTIES POSITION_INDEPENDENT_CODE ON)
target_compile_options(libimhex PRIVATE -Wall -Wextra -Werror)
if (WIN32)
    add_compile_definitions(OS_WINDOWS)
endif()

target_include_directories(libimhex PUBLIC include ${LLVM_INCLUDE_DIRS} ${FMT_INCLUDE_DIRS} ${CURL_INCLUDE_DIRS})

target_link_libraries(libimhex PUBLIC dl imgui nfd ws2_32 LLVMDemangle  ${NLOHMANN_JSON_LIBRARIES}  ${LIBCURL_LIBRARIES}  ${FMT_LIBRARIES})
