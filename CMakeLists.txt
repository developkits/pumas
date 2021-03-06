cmake_minimum_required(VERSION 2.8)
project(PUMAS)
include_directories(src include)
set(HEADER_FILES include/helpers.hpp include/Serializer.hpp include/Simulator.hpp)

message(status "${CMAKE_CURRENT_SOURCE_DIR}")
add_executable(solver src/solver.cpp src/Simulator.cpp src/Serializer.cpp src/helpers.cpp ${HEADER_FILES})
add_executable(test-suite src/test-suite.cpp src/Simulator.cpp
    src/Serializer.cpp src/helpers.cpp ${HEADER_FILES})

find_package(Doxygen)
if(DOXYGEN_FOUND)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile @ONLY)
    add_custom_target(doc
        ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
        COMMENT "Generating API documentation with Doxygen" VERBATIM
        )
endif(DOXYGEN_FOUND)

if (CMAKE_COMPILER_IS_GNUCXX)
    message(status "Setting GCC flags")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2 -Wall -Wextra -W -pedantic -std=c++0x")
else()
    message(status "GCC not found!")
    return()
endif()

unset(Boost_INCLUDE_DIR CACHE)
unset(Boost_LIBRARY_DIRS CACHE)

set(Boost_USE_STATIC_LIBS        OFF)
set(Boost_USE_MULTITHREADED      ON)
set(Boost_USE_STATIC_RUNTIME     OFF)
set(BOOST_ALL_DYN_LINK           ON)   # force dynamic linking for all libraries

find_package(Boost 1.53 COMPONENTS unit_test_framework program_options random REQUIRED)
ADD_DEFINITIONS(-DBOOST_TEST_DYN_LINK) 
message(status "** Boost Include: ${Boost_INCLUDE_DIR}")
message(status "** Boost Libraries: ${Boost_LIBRARY_DIRS}")
message(status "** Boost Libraries: ${Boost_LIBRARIES}")

INCLUDE_DIRECTORIES(${Boost_INCLUDE_DIR})
LINK_DIRECTORIES(${Boost_LIBRARY_DIRS})

target_link_libraries(solver -lm ${Boost_LIBRARIES})
target_link_libraries(test-suite ${Boost_LIBRARIES})

