cmake_minimum_required (VERSION 3.9)

include(GNUInstallDirs)

project (TestLibrariesOnMac VERSION 1.0 DESCRIPTION "Test libraries on mac")

if("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")
	message(SEND_ERROR "In-source builds are not allowed.")
endif("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")

# https://cmake.org/cmake/help/v3.0/policy/CMP0042.html
# https://cmake.org/cmake/help/v3.0/prop_tgt/MACOSX_RPATH.html

#set(MACOSX_RPATH ON)

add_library(testlib SHARED testlib.cpp)
set_target_properties(testlib PROPERTIES VERSION ${PROJECT_VERSION} PUBLIC_HEADER testlib.h)
install(TARGETS testlib LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

add_executable(testbin testbin.cpp)
target_link_libraries(testbin testlib)
install (TARGETS testbin RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
