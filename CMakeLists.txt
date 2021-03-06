cmake_minimum_required(VERSION 2.8)

project(webview)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/webview.c "#define WEBVIEW_IMPLEMENTATION\n#include <webview.h>")
add_library(webview ${CMAKE_CURRENT_BINARY_DIR}/webview.c)


if(APPLE)
	set(WEBVIEW_COMPILE_DEFS "-DWEBVIEW_COCOA=1")
	set(WEBVIEW_LIBS "-framework WebKit")
	set(CMAKE_XCODE_ATTRIBUTE_ENABLE_STRICT_OBJC_MSGSEND NO) #seems to not work
	set_property (TARGET webview PROPERTY XCODE_ATTRIBUTE_ENABLE_STRICT_OBJC_MSGSEND NO)

	set(CMAKE_VERBOSE_MAKEFILE ON)
elseif(WIN32)
	set(WEBVIEW_COMPILE_DEFS "-DWEBVIEW_WINAPI=1")
	set(WEBVIEW_LIBS ole32 comctl32 oleaut32 uuid)
else()
	set(WEBVIEW_COMPILE_DEFS "-DWEBVIEW_GTK=1")
	find_package(PkgConfig REQUIRED)
	pkg_check_modules(GTK3 REQUIRED gtk+-3.0)
	pkg_check_modules(WEBKIT2 REQUIRED webkit2gtk-4.0)
	set(WEBVIEW_COMPILE_INCS ${GTK3_INCLUDE_DIRS} ${WEBKIT2_INCLUDE_DIRS} ${PROJECT_SOURCE_DIR})
	set(WEBVIEW_LIBS ${GTK3_LIBRARIES} ${WEBKIT2_LIBRARIES})
endif()


target_include_directories(webview PUBLIC ${PROJECT_SOURCE_DIR} ${WEBVIEW_COMPILE_INCS})
target_compile_definitions(webview PUBLIC ${WEBVIEW_COMPILE_DEFS})
target_compile_options(webview PUBLIC ${WEBVIEW_COMPILE_OPTS})
target_link_libraries(webview ${WEBVIEW_LIBS})



# add_executable(webview_test WIN32 MACOSX_BUNDLE webview_test.cc)
# set_target_properties(webview_test PROPERTIES CXX_STANDARD 11 CXX_STANDARD_REQUIRED YES CXX_EXTENSIONS NO)
# find_package(Threads)
# target_link_libraries(webview_test PRIVATE webview ${CMAKE_THREAD_LIBS_INIT})
# enable_testing ()
# add_test(NAME webview_test COMMAND webview_test)
