cmake_minimum_required(VERSION 3.21)

set(CMAKE_C_STANDARD 17)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_EXTENSIONS OFF)

# project_options
# https://github.com/cpp-best-practices/project_options
include(FetchContent)
FetchContent_Declare(_project_options URL  https://github.com/cpp-best-practices/project_options/archive/refs/tags/v0.33.0.zip)

FetchContent_MakeAvailable(_project_options)
include(${_project_options_SOURCE_DIR}/Index.cmake)

# install vcpkg dependencies: - should be called before defining project()
run_vcpkg()

# T4CB
project(
    t4cb
    VERSION 0.0.1
    DESCRIPTION "The 4th Coming Build Planner"
    LANGUAGES CXX C
)

# always enable testing
enable_testing()

# set options
project_options(
    ENABLE_CACHE
    # ENABLE_CPPCHECK
    # ENABLE_CLANG_TIDY
    # ENABLE_INTERPROCEDURAL_OPTIMIZATION
    # ENABLE_NATIVE_OPTIMIZATION
    # ENABLE_COVERAGE
    # ENABLE_SANITIZER_ADDRESS
    # ENABLE_SANITIZER_UNDEFINED_BEHAVIOR
    # ENABLE_SANITIZER_LEAK
    # ENABLE_SANITIZER_THREAD
    # ENABLE_SANITIZER_MEMORY
    # ENABLE_VS_ANALYSIS
    WARNINGS_AS_ERRORS
    # ENABLE_BUILD_WITH_TIME_TRACE
    CPPCHECK_OPTIONS
    --enable=style,performance,warning,portability
    --inline-suppr
    # We cannot act on a bug/missing feature of cppcheck
    --suppress=cppcheckError
    --suppress=internalAstError
    # if a file does not have an internalAstError, we get an unmatchedSuppression error
    --suppress=unmatchedSuppression
    --suppress=passedByValue
    --suppress=syntaxError
    --suppress=functionStatic
    --inconclusive
)

# project options/warnings
target_compile_features(project_options INTERFACE cxx_std_${CMAKE_CXX_STANDARD})

add_library(t4cb::project_options INTERFACE IMPORTED)
add_library(t4cb::project_warnings INTERFACE IMPORTED)

# project directories
add_subdirectory(t4cb)

# project package
package_project(
    TARGETS
    t4cb
    project_options
    project_warnings
)

# git sha
set(CPPS_GIT_SHA "Unknown" CACHE STRING "SHA this build was generated from")
string(SUBSTRING "${CPPS_GIT_SHA}" 0 8 CPPS_GIT_SHORT_SHA)

set(CPACK_PACKAGE_FILE_NAME
    "${CMAKE_PROJECT_NAME}-${CMAKE_PROJECT_VERSION}-${CPPS_GIT_SHORT_SHA}-${CMAKE_SYSTEM_NAME}-${CMAKE_BUILD_TYPE}-${CMAKE_CXX_COMPILER_ID}-${CMAKE_CXX_COMPILER_VERSION}"
)

include(CPack)