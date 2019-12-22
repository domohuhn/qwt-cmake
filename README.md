# qwt-cmake
CMake integration for qwt. Qwt is the most popular library for charts and 
plots in combination with the Qt library. Sadly, the most common build system
generator CMake is not supported. This repository provides a CMakeLists.txt file
that will check out the qwt source code from svn and add the source files to your
CMake build.

The option QWT_BUILD_EXAMPLES can be used to add the examples to the build as well.
