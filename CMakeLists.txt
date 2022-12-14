cmake_minimum_required(VERSION 3.23)

project(AttendanceServer VERSION 1.0.0)

if (MSVC)
    # 4142 - benign redefinition of type, doesn't matter
    add_compile_options(-wd4142)
endif()

include_directories(deps/curl/include
                    deps/mbedtls/include
                    deps/mongoose
                    deps/oauth2/include
                    deps/tomlc99)

add_compile_definitions(MG_ENABLE_MBEDTLS=1)

set(BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)
set(BUILD_TESTING OFF CACHE BOOL "" FORCE)
set(BUILD_TESTING_SHARED OFF CACHE BOOL "" FORCE)
set(BUILD_TESTING_STATIC OFF CACHE BOOL "" FORCE)
add_subdirectory(deps/curl)
set(ENABLE_PROGRAMS OFF CACHE BOOL "" FORCE)
set(ENABLE_TESTING OFF CACHE BOOL "" FORCE)
set(USE_SHARED_MBEDTLS_LIBRARY OFF CACHE BOOL "" FORCE)
set(GEN_FILES ON CACHE BOOL "" FORCE)
add_subdirectory(deps/mbedtls)
add_library(mongoose STATIC deps/mongoose/mongoose.h deps/mongoose/mongoose.c)
add_library(oauth2 STATIC deps/oauth2/include/curl_request.h deps/oauth2/include/oauth2.h deps/oauth2/src/curl_request.c deps/oauth2/src/oauth2.c)
add_library(tomlc99 STATIC deps/tomlc99/toml.h deps/tomlc99/toml.c)

set(HEADERS server.h types.h)
set(SOURCES server.c)
set(DATA index.html)
add_executable(AttendanceServer ${HEADERS} ${SOURCES} ${DATA})
target_link_libraries(AttendanceServer PRIVATE libcurl mbedtls mbedcrypto mbedx509 mongoose oauth2 tomlc99)
set_target_properties(AttendanceServer PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
set_directory_properties(PROPERTIES VS_STARTUP_PROJECT AttendanceServer)
