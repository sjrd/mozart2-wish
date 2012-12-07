cmake_minimum_required(VERSION 2.8.6)
project(MOZARTWISH)

# Tcl/Tk

# Find the package
find_package(TCL REQUIRED)

SET(TCLTK_POSSIBLE_STDLIB_PATHS
  "${TCL_LIBRARY_PATH_PARENT}/share/tcltk"
  "${TK_LIBRARY_PATH_PARENT}/share/tcltk"
  ${TCL_FRAMEWORK_INCLUDES}
  ${TK_FRAMEWORK_INCLUDES}
  "${TCL_TCLSH_PATH_PARENT}/share/tcltk"
  "${TK_WISH_PATH_PARENT}/share/tcltk"
  /usr/share/tcltk/tcl${TK_LIBRARY_VERSION}
  /usr/share/tcltk/tcl${TCL_LIBRARY_VERSION}
  /usr/share/tcltk/tcl8.6
  /usr/share/tcltk/tcl8.5
  /usr/share/tcltk/tcl8.4
  /usr/share/tcltk/tcl8.3
  /usr/share/tcltk/tcl8.2
  /usr/share/tcltk/tcl8.0
  /usr/share/tcltk/tk${TK_LIBRARY_VERSION}
  /usr/share/tcltk/tk${TCL_LIBRARY_VERSION}
  /usr/share/tcltk/tk8.6
  /usr/share/tcltk/tk8.5
  /usr/share/tcltk/tk8.4
  /usr/share/tcltk/tk8.3
  /usr/share/tcltk/tk8.2
  /usr/share/tcltk/tk8.0
  )

IF(WIN32)
  SET(TCLTK_POSSIBLE_STDLIB_PATHS ${TCLTK_POSSIBLE_STDLIB_PATHS}
    "[HKEY_LOCAL_MACHINE\\SOFTWARE\\ActiveState\\ActiveTcl\\${ActiveTcl_CurrentVersion}]/share/tcltk"
    "[HKEY_LOCAL_MACHINE\\SOFTWARE\\Scriptics\\Tcl\\8.6;Root]/share/tcltk"
    "[HKEY_LOCAL_MACHINE\\SOFTWARE\\Scriptics\\Tcl\\8.5;Root]/share/tcltk"
    "[HKEY_LOCAL_MACHINE\\SOFTWARE\\Scriptics\\Tcl\\8.4;Root]/share/tcltk"
    "[HKEY_LOCAL_MACHINE\\SOFTWARE\\Scriptics\\Tcl\\8.3;Root]/share/tcltk"
    "[HKEY_LOCAL_MACHINE\\SOFTWARE\\Scriptics\\Tcl\\8.2;Root]/share/tcltk"
    "[HKEY_LOCAL_MACHINE\\SOFTWARE\\Scriptics\\Tcl\\8.0;Root]/share/tcltk"
    "$ENV{ProgramFiles}/Tcl/share/tcltk"
    "C:/Program Files/Tcl/share/tcltk"
    "C:/Tcl/share/tcltk"
    )
ENDIF(WIN32)

FIND_PATH(TCL_STDLIB_PATH
  NAMES init.tcl
  HINTS ${TCLTK_POSSIBLE_STDLIB_PATHS}
  )

FIND_PATH(TK_STDLIB_PATH
  NAMES tk.tcl
  HINTS ${TCLTK_POSSIBLE_STDLIB_PATHS}
  )

# Our application

include_directories("${TCL_INCLUDE_PATH}" "${TK_INCLUDE_PATH}")

if(WIN32)
  set(MAIN_SRC winmain.cc)
else()
  set(MAIN_SRC unixmain.cc)
endif()

add_executable(ozwish ${MAIN_SRC})
target_link_libraries(ozwish "${TCL_LIBRARY}" "${TK_LIBRARY}")

if(NOT MINGW)
  target_link_libraries(ozwish pthread)
endif()

# Tcl and Tk "standard" libraries

file(GLOB TCL_STDLIB_FILES "${TCL_STDLIB_PATH}/*.tcl")
file(GLOB TK_STDLIB_FILES "${TK_STDLIB_PATH}/*.tcl")

# Install
install(TARGETS ozwish DESTINATION bin)
install(FILES ${TCL_STDLIB_FILES} DESTINATION share/mozart/tcltk/tcl)
install(FILES ${TK_STDLIB_FILES} DESTINATION share/mozart/tcltk/tk)
