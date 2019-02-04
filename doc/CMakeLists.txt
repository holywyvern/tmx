cmake_minimum_required(VERSION 3.0)

project(tmxHelp NONE)

option(DOC_HTML "Build html help with Sphinx" OFF)
option(DOC_SINGLEHTML "Build html single page help with Sphinx" OFF)
find_program(SPHINX_EXECUTABLE NAMES sphinx-build DOC "Sphinx Documentation Builder (sphinx-doc.org)")

set(SPHINX_FLAGS "" CACHE STRING "Flags to pass to sphinx-build")
mark_as_advanced(SPHINX_FLAGS)

if(DOC_HTML AND NOT DOC_SINGLEHTML)
  return()
elseif(NOT SPHINX_EXECUTABLE)
  message(FATAL_ERROR "SPHINX_EXECUTABLE (sphinx-build) is not found!")
endif()


