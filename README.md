# STAR StPicoDst library

Implementation of the STAR experiment's StPicoDst and associated utility classes in a convenient CMake wrapper, with options for different STAR Library versions of the code to be used.

## Dependencies

Only depends on vanilla ROOT. CMAKE must be able to find ROOT using the find_package(...) CMake method: https://cmake.org/cmake/help/latest/command/find_package.html. 

## Build 

By default, builds the shared library, and installs it in ${CMAKE_INSTALL_PREFIX}/lib. BUILD_SHARED_LIBS can be turned off to build the static library instead. Headers are installed in ${CMAKE_INSTALL_PREFIX}/include. 

## Build options

You can specify which version of the StPicoEvent code to compile using the two CMake flags PICO_SL18H and PICO_SL18B. If none are specified, SL18B is built by default. Header installation can be turned off by setting PICO_INSTALL_HEADERS=Off