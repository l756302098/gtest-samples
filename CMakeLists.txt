cmake_minimum_required(VERSION 3.5)

# Set the project name
project (google_test_example)

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

add_executable(example_google_main main.cpp)

target_link_libraries(example_google_main
Boost::filesystem
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
