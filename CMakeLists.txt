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
  GIT_REPOSITORY https://git.code.sf.net/p/qwt/git
  GIT_TAG 878e90e5fe02f8e752d5d5eefea629fb4fbe54c0 # version 6.2.1
  PATCH_COMMAND git apply ${CMAKE_CURRENT_SOURCE_DIR}/qwt-6.2.1.patch --unsafe-paths
)

FetchContent_GetProperties(qwt)
if(NOT ${qwt_POPULATED})
	message(STATUS "Populating target qwt...")
	FetchContent_Populate(qwt)
endif()

find_package(Qt5 COMPONENTS Core Gui Widgets OpenGL Concurrent Svg PrintSupport)

file(GLOB QWT_SOURCES ${qwt_SOURCE_DIR}/src/*.cpp)
file(GLOB QWT_HEADERS ${qwt_SOURCE_DIR}/src/*.h)
file(GLOB QWT_CLASS_HEADERS ${qwt_SOURCE_DIR}/classincludes/*)

qt5_wrap_cpp(QWT_SOURCES ${QWT_HEADERS} ${QWT_CLASS_HEADERS})
add_library(qwt ${QWT_SOURCES})


target_include_directories(qwt PUBLIC ${qwt_SOURCE_DIR}/src/ ${qwt_SOURCE_DIR}/classincludes/)

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
				WIN32_EXECUTABLE true
				AUTOMOC ON
			)
		endif()
	endforeach()
endif()

