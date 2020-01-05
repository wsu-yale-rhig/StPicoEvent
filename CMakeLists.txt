cmake_minimum_required(VERSION 2.8.12 FATAL_ERROR)
cmake_policy(SET CMP0042 NEW)
cmake_policy(SET CMP0048 NEW)

# project title
project(StPicoEvent VERSION 0.1.0)

set(CMAKE_BUILD_TYPE RELEASE)

# options
option(BUILD_SHARED_LIBS "Build shared libs" ON)
option(PICO_SL18H "Build the SL18h version of StPicoEvent" OFF)
option(Pico_SL18C "Build the SL19C version of StPicoEvent" OFF)
option(PICO_INSTALL_HEADERS "Install StPicoEvent headers" ON)

# compiler & system checks
include(cmake/SysCheck.cmake)

# add module path to cmake
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake/Modules)

# helper functions
include(cmake/Utils.cmake)

# dependencies
include(cmake/Dependencies.cmake)

# build options
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2 -fPIC")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-narrowing")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11")

# add -D_VANILLA_ROOT_ to CMAKE_CXX_FLAGS. Necessary to turn off all StRoot
# dependencies
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D_VANILLA_ROOT_")

# start the build - define source file and header variables
set(PICO_SRCS)
set(PICO_HDRS)
set(PICO_LINKDEF)

# check option logic
if (PICO_SL18H AND PICO_SL19C)
  message(FATAL_ERROR "Cannot select multiple library versions of StPicoEvent")
endif (PICO_SL18H AND PICO_SL19C)

if (NOT PICO_SL18H AND NOT PICO_SL19C)
  message(STATUS "Selecting SL19C library version by default")
  set(PICO_SL19C TRUE)
endif (NOT PICO_SL18H AND NOT PICO_SL19C)

# finally, add the source tree - dependent on library version
if (PICO_SL18H)
	add_subdirectory(SL18h/StPicoEvent)
	set(PICO_INCLUDE ${CMAKE_CURRENT_SOURCE_DIR}/SL18h)
	include_directories(${PICO_INCLUDE})
elseif (PICO_SL19C)
 add_subdirectory(SL19c/StPicoEvent)
 set(PICO_INCLUDE ${CMAKE_CURRENT_SOURCE_DIR}/SL19c)
else(PICO_SL18H)
	message(FATAL_ERROR "No library version specified for StPicoEvent")
endif(PICO_SL18H)

# use ROOT to generate the dictionary file
set(PICO_DICT_NAME StPicoEvent_dict)
set(PICO_DICT_TARGET StPicoEvent_dict.cxx)
ROOT_GENERATE_DICTIONARY(${PICO_DICT_NAME} ${PICO_HDRS} LINKDEF ${PICO_LINKDEF}
                         OPTIONS "-D_VANILLA_ROOT_")

# build target library
add_library(StPicoEvent ${PICO_SRCS} ${PICO_DICT_TARGET})
target_link_libraries(StPicoEvent PRIVATE ${PICO_DEPENDENCY_LIBS})
target_include_directories(StPicoEvent INTERFACE $<INSTALL_INTERFACE:include>)
target_compile_options(StPicoEvent INTERFACE "--std=c++11")
install(TARGETS StPicoEvent EXPORT StPicoEvent DESTINATION lib)
install(EXPORT StPicoEvent NAMESPACE pico_ DESTINATION lib/StPicoEvent)
link_directories(${CMAKE_INSTALL_PREFIX}/lib)

set (PICO_LIBRARIES StPicoEvent)

# add an uninstall option, since cmake does not provide one by default
if(NOT TARGET uninstall)
  configure_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/cmake_uninstall.cmake.in
                 ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake
                 IMMEDIATE
                 @ONLY)

  add_custom_target(uninstall
                    COMMAND ${CMAKE_COMMAND} -P
                            ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)
endif()

# export the analysis variables if not root directory
if(NOT CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
	set(PICO_BUILD_SL18H ${PICO_SL18H} PARENT_SCOPE)
	set(PICO_BUILD_SL18B ${PICO_SL18B} PARENT_SCOPE)
	set(PICO_INCLUDE_DIRS ${PICO_INCLUDE} ${ROOT_INCLUDE_DIRS} PARENT_SCOPE)
	set(PICO_LIBS ${PICO_LIBRARIES} PARENT_SCOPE)
  set(PICO_DEPENDENCY_LIBS ${PICO_DEPENDENCY_LIBS} PARENT_SCOPE)
endif(NOT CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
