# Copyright (c) 2013 - 2016, Roland Bock
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without modification,
# are permitted provided that the following conditions are met:
#
#   Redistributions of source code must retain the above copyright notice, this
#   list of conditions and the following disclaimer.
#
#   Redistributions in binary form must reproduce the above copyright notice, this
#   list of conditions and the following disclaimer in the documentation and/or
#   other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
# ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
# WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
# DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR
# ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
# (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
# LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON
# ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
# SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

cmake_minimum_required(VERSION 3.2)
include(CheckCXXSymbolExists)
project (sqlpp11-connector-sqlite3)
enable_testing()
set(CMAKE_CXX_STANDARD 11)

option(ENABLE_TESTS "Build unit tests" On)
option(BUILD_SHARED_LIBS "Build shared libraries" Off)
option(SQLCIPHER "Build with SQLCipher" Off)

if (NOT DEFINED SQLPP11_DYNAMIC_LOADING)
   set(SQLPP11_DYNAMIC_LOADING Off)
endif()

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
if(SQLCIPHER )
	find_package(SQLCipher REQUIRED)
	set (SQLITE3_LIBRARIES "${SQLCIPHER_LIBRARIES}" )
	set (SQLITE3_INCLUDE_DIRS "${SQLCIPHER_INCLUDE_DIRS}" )
    set (SQLITE3_LIBRARY "${SQLCIPHER_LIBRARY}")
else()
	find_package(Sqlite3 REQUIRED)
endif()

find_package(HinnantDate REQUIRED)

message(STATUS "Using ${CMAKE_CXX_COMPILER} (compiler id: ${CMAKE_CXX_COMPILER_ID})")

set(SQLPP11_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../sqlpp11/include" CACHE FILEPATH "Path to sqlpp11 includes")

if(NOT EXISTS ${SQLPP11_INCLUDE_DIR}/sqlpp11/sqlpp11.h)
	message(SEND_ERROR "Can't find file sqlpp11/sqlpp11.h")
	message("Can't find sqlpp11/sqlpp11.h in ${SQLPP11_INCLUDE_DIR} ")
	message("Please either")
	message("  - git clone https://github.com/rbock/sqlpp11 ${SQLPP11_INCLUDE_DIR}")
	message("  - download and unzip a current version from https://github.com/rbock/sqlpp11 to ${SQLPP11_INCLUDE_DIR}")
	message("  - set SQLPP11_INCLUDE_DIR to point to the dir containing sqlpp11/sqlpp11.h")
	message("")
else()
	message("including sqlpp11 from ${SQLPP11_INCLUDE_DIR}")
endif()

set(include_dir "${PROJECT_SOURCE_DIR}/include")
file(GLOB_RECURSE sqlpp_headers ${include_dir}/*.h ${SQLPP11_INCLUDE_DIR}/*.h)

add_subdirectory(src)

if(ENABLE_TESTS)
	add_subdirectory(tests)
endif()

install(DIRECTORY "${PROJECT_SOURCE_DIR}/include/sqlpp11" DESTINATION include)

