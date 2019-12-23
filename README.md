# qwt-cmake
CMake integration for qwt. Qwt is the most popular library for charts and 
plots in combination with the Qt library. Sadly, the most common build system
generator CMake is not supported. This repository provides a CMakeLists.txt file
that will download the qwt source code from svn and add the source files to your
CMake build.

The option QWT_BUILD_EXAMPLES can be used to add the examples to the build as well.

## Why do you not provide the full source code?
By downloading the source code from the official subversion repository,
the users of qwt have the guarantee to get an unaltered version. Additionally,
switching versions only requires to change the tag in the FetchContent_Declare
step and everything should still work if the directory structure stays the same.

Keeping the CMake file up to date should be a lot easier than if you also had to
merge a new qwt release.

## Dependencies

 - subversion
 - Qt5
 - CMake 3.11 or later
 