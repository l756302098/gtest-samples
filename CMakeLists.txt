cmake_minimum_required(VERSION 3.5)

# Set the project name
project (google_test_example)
# coverage option
OPTION (ENABLE_COVERAGE "Use gcov" OFF)
MESSAGE(STATUS ENABLE_COVERAGE=${ENABLE_COVERAGE})
IF(ENABLE_COVERAGE)
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fprofile-arcs -ftest-coverage")
    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fprofile-arcs -ftest-coverage")
    # 其中 -fprofile-arcs 用来生成 gcno 文件； -ftest-coverage 用来在统计时生成 gcda 文件
    SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fprofile-arcs -ftest-coverage") 
ENDIF()
find_package(Boost REQUIRED COMPONENTS filesystem system)

# check if boost was found
if(Boost_FOUND)
    message ("boost found")
else()
    message (FATAL_ERROR "Cannot find Boost")
endif()
# Add an library for the example classes
add_library(example_google_test 
    Reverse.cpp
    Palindrome.cpp
)

include_directories(${Boost_INCLUDE_DIRS})
add_executable(example_google_main main.cpp)

target_link_libraries(example_google_main
${Boost_LIBRARIES}
#Boost::filesystem
example_google_test)

#############################################
# Unit tests

add_subdirectory(3rd_party/google-test)

# enable CTest testing
enable_testing()

# Add a testing executable
add_executable(unit_tests unit_tests.cpp)

target_link_libraries(unit_tests
    example_google_test
    GTest::GTest 
    GTest::Main
)

add_test(test_all unit_tests)
