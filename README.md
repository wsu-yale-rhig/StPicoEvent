# STAR StPicoEvent library

Implementation of the STAR experiment's StPicoEvent and associated utility classes in a convenient CMake wrapper, with options for different STAR Library versions of the code to be used.

## Dependencies

Only depends on vanilla ROOT. CMAKE must be able to find ROOT using the find_package(...) CMake method: https://cmake.org/cmake/help/latest/command/find_package.html. 

## Build 

By default, builds the shared library, and installs it in ${CMAKE_INSTALL_PREFIX}/lib. BUILD_SHARED_LIBS can be turned off to build the static library instead. Headers are installed in ${CMAKE_INSTALL_PREFIX}/include. 

## Build options

You can specify which version of the StPicoEvent code to compile using a cmake flag. Currently, only supports the most recent version of the SL18h StPicoEvent library, which can be selected with the flag PICO_SL18H (on by default). As other versions of the library are added, the corresponding flags will be added as well. If no library flags are specified, SL18H is built by default. Header installation can be turned off by setting PICO_INSTALL_HEADERS=Off
