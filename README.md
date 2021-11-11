# gtest-samples
gtest samples



# Introduction

Using link:https://cmake.org/Wiki/CMake/Testing_With_CTest[CTest] you can generate
a `make test` target to run automated unit-tests. This example shows how to
download and build the link:https://github.com/google/googletest[google test] library,
create tests and run them.

The files in this tutorial are below:

```
$ tree
.
├── 3rd_party
│   └── google-test
│       ├── CMakeLists.txt
│       └── CMakeLists.txt.in
├── CMakeLists.txt
├── Reverse.h
├── Reverse.cpp
├── Palindrome.h
├── Palindrome.cpp
├── unit_tests.cpp
```

  * link:3rd_party/google-test/CMakeLists.txt[] - CMake commands to build and prepare the google test library
  * link:3rd_party/google-test/CMakeLists.txt.in[] - Helper script to do the download of google test
  * link:CMakeLists.txt[] - Contains the CMake commands you wish to run
  * link:Reverse.h[] / link:Reverse.cpp[.cpp] - Class to reverse a string
  * link:Palindrome.h[] / link:Palindrome.cpp[.cpp] - Class to test if a string is a palindrome
  * link:unit_test.cpp[] - Unit Tests using google test unit test framework

# Requirements

An internet connection. This example will download the google test library the first time you run the CMake configure step. See the 
link:https://github.com/google/googletest/blob/master/googletest/README.md[google test readme] and link:http://crascit.com/2015/07/25/cmake-gtest/[here] for details. 

# Concepts

## Download and Build Google Test

[source,cmake]
----
add_subdirectory(3rd_party/google-test)
----

This will add the CMake files which download and build Google Test. This is the recommended way to add google test and there are 
more details from link:https://github.com/google/googletest/blob/master/googletest/README.md[google test readme] and link:http://crascit.com/2015/07/25/cmake-gtest/[here]

Alternatives to this method include:

  * Use something like `git submodule` to download the source to a folder in your tree and then do `add_subdirectory`
  * Vendor the google test source code within your repository
  * Build google test externally and link it using `find_package(GTest)` - Not recommended by the google test authors anymore

## Enabling testing

To enable testing you must include the following line in your top level CMakeLists.txt

[source,cmake]
----
enable_testing()
----

This will enable testing for the current folder and all folders below it.

## Adding a testing executable

The requirement for this step will depend on your unit-test framework. In the example
of google test, you can create binary(s) which includes all the unit tests that you want to run.

[source,cmake]
----
add_executable(unit_tests unit_tests.cpp)

target_link_libraries(unit_tests
    example_google_test
    GTest::GTest
    GTest::main
)
----

In the above code, a unit test binary is added, which links against the google test unit-test-framework using the
ALIAS target setup during the link:3rd_party/google-test/CMakeLists.txt[download and build] of GTest.

## Add A test

To add a test you call the link:https://cmake.org/cmake/help/v3.0/command/add_test.html[`add_test()`] function.
This will create a named test which will run the supplied command.

[source,cmake]
----
add_test(test_all unit_tests)
----

In this example, we create a test called `test_all` which will run the executable
created by the `unit_tests` executable created from the call to `add_executable`

# Building the Example

[source,bash]
----
$ mkdir build

$ cd build/

$ cmake -DENABLE_COVERAGE=ON ..

$ make -j2

$ make test
Running tests...
Test project /data/code/cmake-examples/05-unit-testing/google-test-download/build
    Start 1: test_all
1/1 Test #1: test_all .........................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.00 sec
----

If the code is changed and it causes the unit tests to produce an error.
Then when running the tests you will see the following output.

[lcov]
----
$ cd ./build

$ ./unit_tests

$ cd ./build/CMakeFiles/unit_tests.dir 

$ lcov -c -o result.info  -b . -d . 

$ genhtml result.info -o Report 