cmake_minimum_required(VERSION 3.8)
project(yalg)

set(CMAKE_CXX_STANDARD 11)

# place libs into lib and executables into bin directories
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

include_directories(include)

# place all our code into one static library
add_library(alg
	STATIC
	src/par.cpp
)

# optionally build doxygen docs
find_package(Doxygen)
if (DOXYGEN_FOUND)
    # need to render Doxyfile during the build    
    configure_file(${CMAKE_SOURCE_DIR}/doc/Doxyfile.in ${CMAKE_BINARY_DIR}/Doxyfile @ONLY)
    add_custom_target(doc
        COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_BINARY_DIR}/Doxyfile
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
        COMMENT "Generating API documentation with Doxygen"
        VERBATIM )
else (DOXYGEN_FOUND)
    message("Doxygen need to be installed to generate the doxygen documentation")
endif (DOXYGEN_FOUND)

# googletest mantra
ADD_SUBDIRECTORY(extern/googletest)
enable_testing()
include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})

# all tests
add_executable(segtree_test test/segtree_test.cpp include/segtree.hpp)
target_link_libraries(segtree_test gtest gtest_main)

add_executable(binomial_test test/binomial_test.cpp include/binomial.hpp)
target_link_libraries(binomial_test gtest gtest_main)

add_executable(par_test test/par_test.cpp include/par.hpp)
target_link_libraries(par_test alg gtest gtest_main)

add_executable(heap_test test/heap_test.cpp include/heap.hpp)
target_link_libraries(heap_test gtest gtest_main)

add_test(NAME segtree_test COMMAND segtree_test)
add_test(NAME binomial_test COMMAND binomial_test)
add_test(NAME par_test COMMAND par_test)
add_test(NAME heap_test COMMAND heap_test)