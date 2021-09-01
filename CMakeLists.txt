cmake_minimum_required(VERSION 3.21.2)
project(directory_test)

find_package(GTest REQUIRED)

#Bring the headers, such as Student.h into the project
include_directories(include)
include_directories(src)

# Create OBJECT_DIR variable
# set(OBJECT_DIR ${CMAKE_BINARY_DIR}/CMakeFiles/student.dir)
# message("-- Object files will be output to: ${OBJECT_DIR}")

#gcov setting
# add_compile_options("--coverage")
set(CMAKE_CXX_FLAGS "-g -O0 -Wall -fprofile-arcs -ftest-coverage")
set(CMAKE_CXX_OUTPUT_EXTENSION_REPLACE ON)

#optional google test parameters.
enable_testing()
include(GoogleTest)
#add_subdirectory(test) #Build all the gtest stuff
#include_directories(gtest/include)

#Can manually add the sources using the set command as follows:
#set(SOURCES src/mainapp.cpp src/Student.cpp)
#However, the file(GLOB...) allows for wildcard additions:
file(GLOB SOURCES "src/*.cpp")
add_executable(student ${SOURCES})

#test folder contains test files
set (PROJECT_SOURCE_DIR test) 
file(GLOB SOURCES "test/*.cpp")
add_executable(test_student ${PROJECT_SOURCE_DIR}/g_main.cpp)
target_link_libraries(test_student gtest gtest_main pthread )

# Create the gcov target. Run coverage tests with 'make gcov'
add_custom_target(gcov
    COMMAND mkdir -p coverage
    COMMAND ${CMAKE_MAKE_PROGRAM} test_student
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    )
add_custom_command(TARGET gcov
    COMMAND echo "=================== GCOV ===================="
    COMMAND gcovr -r . --html --html-details -o coverage/coverage.html
    )

#clean all cmake and make generated folder and files
add_custom_target(cleanall
    COMMAND echo "=================== clean build ===================="
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    COMMAND rm -rf CMakeFiles/ coverage/ temp/ *.cmake *.info CMakeCache.txt *.cmake Makefile
    )
