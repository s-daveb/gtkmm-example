cmake_minimum_required(VERSION 3.24)

# Prevent in-source builds @
if (CMAKE_BINARY_DIR STREQUAL CMAKE_SOURCE_DIR)
    message(FATAL_ERROR "Source and build directories cannot be the same.")
endif()

if (CMAKE_VERSION VERSION_GREATER_EQUAL "3.24.0")
	cmake_policy(SET CMP0135 NEW)
endif()

# When this package is included as a subproject, there's no need to
# build and run the unit-tests. setting -D ENABLE_TESTS to false could
# speed up build time for pacakge maintainers
if (NOT DEFINED PROJECT_NAME)
	option(ENABLE_TESTS "Build and run unit tests" ON)
else()
	option(ENABLE_TESTS "Build and run unit tests" OFF)
endif()

project(gtkmm-test
	VERSION 0.1
	LANGUAGES C CXX
	# HOMEPAGE_URL
	DESCRIPTION "A CGI script written in C++ that converts markdown to HTML"
)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 17)
set(CMAKE_C_EXTENSIONS OFF)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build/bin)

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
	set(custom_dbg_flags "-O0 -ggdb")

    # Custom flags for Debug configuration
    set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} ${custom_dbg_flags}")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} ${custom_dbg_flags}")

	# Set static linking of things here
	#
	# if(USE_BOOST_STACKTRACE)
	# 	set(Boost_USE_STATIC_LIBS ON)
	# endif()
elseif(CMAKE_BUILD_TYPE STREQUAL "Release")
	set(custom_rel_flags "-Os")

    set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} ${custom_rel_flags}")
    set(
		CMAKE_CXX_FLAGS_RELEASE
		"${CMAKE_CXX_FLAGS_RELEASE} ${custom_rel_flags}"
	)

	# Set shared linking of things here
	#
	# if(USE_BOOST_STACKTRACE)
	# 	set(Boost_USE_STATIC_LIBS ON)
	# endif()
endif()

find_package(PkgConfig)
pkg_check_modules(GTKMM gtkmm-4.0 REQUIRED)

link_directories(${GTKMM_LIBRARY_DIRS})
include_directories(${CMAKE_SOURCE_DIR} ${GTKMM_INCLUDE_DIRS})

set(APP_SOURCES main.cpp)
add_executable(gtkmm-test ${APP_SOURCES})
target_link_libraries(gtkmm-test PRIVATE ${GTKMM_LIBRARIES})
