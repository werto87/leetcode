cmake_minimum_required(VERSION 3.27)
project(project VERSION 1.0.0
        DESCRIPTION project
        LANGUAGES CXX
        )
cmake_policy(SET CMP0015 NEW)
include(cmake/Cache.cmake)
find_package(Boost REQUIRED)
set(PROJECT_WARNINGS project_warning)
add_library(${PROJECT_WARNINGS} INTERFACE)
include(cmake/CompilerWarnings.cmake)
set_project_warnings(${PROJECT_WARNINGS})
set(PROJECT_OPTIONS project_options)
add_library(${PROJECT_OPTIONS} INTERFACE)
target_compile_features(${PROJECT_OPTIONS} INTERFACE cxx_std_20)
include(cmake/StaticAnalyzers.cmake)
include(cmake/Sanitizers.cmake)
enable_sanitizers(${PROJECT_OPTIONS})
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(TARGET_NAME_LIB project_lib)
add_library(${TARGET_NAME_LIB} INTERFACE)
add_subdirectory(src)
#target_include_directories(${TARGET_NAME_LIB} PUBLIC ${CMAKE_CURRENT_BINARY_DIR})
#target_include_directories(${TARGET_NAME_LIB} PUBLIC
#        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
#        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
#        )
target_link_libraries(${TARGET_NAME_LIB} INTERFACE
        ${PROJECT_WARNINGS}
        ${PROJECT_OPTIONS}
        Boost::headers
        Boost::json
        )
#target_compile_definitions(${TARGET_NAME_LIB} PUBLIC "$<$<CONFIG:DEBUG>:DEBUG>")
#target_compile_definitions(${TARGET_NAME_LIB} PUBLIC _LIBCPP_ENABLE_CXX20_REMOVED_TYPE_TRAITS)


enable_testing()
add_subdirectory(test)
set(TARGET_NAME project)
add_executable(${TARGET_NAME}
        main.cxx
        )
target_link_libraries(${TARGET_NAME} PUBLIC
        ${TARGET_NAME_LIB}
        )