# Copyright (C) 2019 by domohuhn
#
# Permission to use, copy, modify, and/or distribute this software for any purpose with or without fee is hereby granted.
# 
# THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED
# WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR 
# CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN ACTION OF CONTRACT,
# NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
#

cmake_minimum_required(VERSION 3.11)

project(qwt-download)

option(QWT_BUILD_EXAMPLES "If the examples of qwt shall be built as well." OFF)

include(FetchContent)

FetchContent_Declare(
  qwt
  SVN_REPOSITORY https://svn.code.sf.net/p/qwt/code/tags/qwt-6.1.6/
  SVN_REVISION -r3379
)

FetchContent_GetProperties(qwt)
if(NOT ${qwt_POPULATED})
	message(STATUS "Populating target qwt...")
	FetchContent_Populate(qwt)
endif()

find_package(Qt5 COMPONENTS Core Gui Widgets OpenGL Concurrent Svg PrintSupport)

file(GLOB QWT_SOURCES ${qwt_SOURCE_DIR}/src/*.cpp)
file(GLOB QWT_HEADERS ${qwt_SOURCE_DIR}/src/*.h)

add_library(qwt ${QWT_HEADERS} ${QWT_SOURCES})

set_target_properties(qwt
    PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${qwt_BINARY_DIR}"
    LIBRARY_OUTPUT_DIRECTORY "${qwt_BINARY_DIR}"
    RUNTIME_OUTPUT_DIRECTORY "${qwt_BINARY_DIR}"
    AUTOMOC ON
)

target_include_directories(qwt PUBLIC ${qwt_SOURCE_DIR}/src/)

target_link_libraries(qwt PUBLIC Qt5::Core Qt5::Gui Qt5::Widgets Qt5::OpenGL Qt5::Concurrent Qt5::Svg Qt5::PrintSupport)

if(${QWT_BUILD_EXAMPLES})
	macro(GET_SUBDIRECTORIES RESULTVAR DIRECTORY)
	  file(GLOB DIR_CONTENTS RELATIVE ${DIRECTORY} ${DIRECTORY}/*)
	  set(${RESULTVAR} "")
	  foreach(CANDIDATE ${DIR_CONTENTS})
		if(IS_DIRECTORY ${DIRECTORY}/${CANDIDATE})
		  list(APPEND ${RESULTVAR} ${CANDIDATE})
		endif()
	  endforeach()
	endmacro()
	
	get_subdirectories(QWT_EXAMPLES ${qwt_SOURCE_DIR}/examples/)
	
	foreach(EXAMPLE ${QWT_EXAMPLES})
		file(GLOB EXAMPLE_HEADERS ${qwt_SOURCE_DIR}/examples/${EXAMPLE}/*.h)
		file(GLOB EXAMPLE_SOURCES ${qwt_SOURCE_DIR}/examples/${EXAMPLE}/*.cpp)
		if(EXAMPLE_SOURCES)
			add_executable(qwt-${EXAMPLE} ${EXAMPLE_HEADERS} ${EXAMPLE_SOURCES})
			target_link_libraries(qwt-${EXAMPLE} PRIVATE qwt)
			set_target_properties(qwt-${EXAMPLE}
				PROPERTIES
				ARCHIVE_OUTPUT_DIRECTORY "${qwt_BINARY_DIR}"
				LIBRARY_OUTPUT_DIRECTORY "${qwt_BINARY_DIR}"
				RUNTIME_OUTPUT_DIRECTORY "${qwt_BINARY_DIR}"
				WIN32_EXECUTABLE true
				AUTOMOC ON
			)
		endif()
	endforeach()
endif()

