cmake_minimum_required (VERSION 3.0)
project(RunTask)

#set
set(BOOST_INCLUDE_DIRS $ENV{BOOST_LIBRARY_DIR} cache path "BOOST_INCLUDE_DIRS")
set(CMAKE_PREFIX_PATH ${CMAKE_BINARY_DIR} cache path "CMAKE_PREFIX_PATH")

if(CMAKE_SYSTEM_NAME MATCHES "Linux")
	set(PLATFORM "Linux")
elseif(CMAKE_SYSTEM_NAME MATCHES "Windows")
	if(CMAKE_CL_64)  
		set(PLATFORM "x64")
	else(CMAKE_CL_64)  
		set(PLATFORM "WIN32")
	endif(CMAKE_CL_64)
else()
	set(PLATFORM "others")
endif(CMAKE_SYSTEM_NAME MATCHES "Linux")

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin/${PLATFORM})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib/${PLATFORM})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib/${PLATFORM})

if(MSVC)
	set(CMAKE_CXX_STANDARD 11)
	set(CMAKE_CXX_STANDARD_REQUIRED yes)
    	add_definitions(-DNOMINMAX)
    	add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    	string(APPEND CMAKE_EXE_LINKER_FLAGS_DEBUG " /NODEFAULTLIB:LIBCMT /NODEFAULTLIB:MSVCRT")
else()
	add_compile_options(-w -Wno-strict-aliasing)
endif()

# set MD to MT
if(OPTION_BUILD_MT)
set(CompilerFlags
    CMAKE_CXX_FLAGS
    CMAKE_CXX_FLAGS_DEBUG
    CMAKE_CXX_FLAGS_RELEASE
    CMAKE_C_FLAGS
    CMAKE_C_FLAGS_DEBUG
    CMAKE_C_FLAGS_RELEASE
  )
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x -lboost_system")
foreach(CompilerFlags ${CompilerFlags})
  string(REPLACE "/MD" "/MT" ${CompilerFlags} "${${CompilerFlags}}")
endforeach()
endif()

find_package(Boost REQUIRED)

if(BOOST_NOT_FOUND)
	message(STATUS "couldn't find Boost")
endif(BOOST_NOT_FOUND)

include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})

file(GLOB SOURCES "*.cpp" "*.h")
set(SRCS_FILES ${SOURCES})

add_executable (${PROJECT_NAME} ${SRCS_FILES})

target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES} /lib/x86_64-linux-gnu/libpthread.so.0 /usr/lib/libboost_system.a /usr/lib/libboost_thread.a)