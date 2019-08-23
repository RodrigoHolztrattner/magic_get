cmake_minimum_required(VERSION 3.1.1 FATAL_ERROR)

############
# INCLUDES #
############
include(FetchContent)

##########
# CONFIG #
##########

if(WIN32)
	# Resource VersionInfo
	set(PROJECT_PRODUCT_NAME "Packet Library")
	set(PROJECT_COMPANY_NAME "Rodrigo Holztrattner Reis")
endif()

#######
# GIT #
#######

# Git config
find_package(Git)
if(EXISTS "${CMAKE_SOURCE_DIR}/.git" AND Git_FOUND)
	execute_process(
		COMMAND "${GIT_EXECUTABLE}" rev-list --count --topo-order v${VERSION_FULL}..HEAD
		WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
		OUTPUT_STRIP_TRAILING_WHITESPACE
		ERROR_STRIP_TRAILING_WHITESPACE
		ERROR_QUIET
	)
endif()

################
# DEPENDENCIES #
################

macro(PrintFetchMessage target_string)
	string(CONCAT end_string "Fetching " ${target_string})
	message(STATUS "")
	message(STATUS "################################")
	message(STATUS "# ${end_string}")
	message(STATUS "################################")
	message(STATUS "")
endmacro()

###########
# PROJECT #
###########

set(PROJECT_NAME "magic_get")
project(${PROJECT_NAME} VERSION 1.0.0 DESCRIPTION "blob")

SET(CMAKE_CXX_FLAGS "/Zi /sdl /MP /Oi /Gy /permissive- /GR- /TP /EHsc")
SET(CMAKE_CXX_FLAGS_DEBUG "/ZI /sdl /MP /JMC /permissive- /GR- /TP /EHsc /MDd /Od")

set(MAGIC_GET_FILES "include/boost")
include_directories(MAGIC_GET_FILES)

# Gather all files
file (GLOB_RECURSE MAGIC_GET_SOURCE_FILES ${MAGIC_GET_FILES}/*.cpp ${MAGIC_GET_FILES}/*.cc) 
file (GLOB_RECURSE MAGIC_GET_HEADER_FILES ${MAGIC_GET_FILES}/*.hpp ${MAGIC_GET_FILES}/*.h) 
set (ALL_MAGIC_GET_FILES ${MAGIC_GET_SOURCE_FILES} ${MAGIC_GET_HEADER_FILES})

# Setup the source groups
foreach(FILE ${ALL_MAGIC_GET_FILES}) 

    # Get the directory of the source file
    get_filename_component(PARENT_DIR "${FILE}" DIRECTORY)

    # Remove common directory prefix to make the group
    string(REPLACE "${CMAKE_CURRENT_SOURCE_DIR}/${MAGIC_GET_FILES}" "" GROUP "${PARENT_DIR}")

    # Make sure we are using windows slashes
    string(REPLACE "/" "\\" GROUP "${GROUP}")

    source_group("${GROUP}" FILES "${FILE}")
	
endforeach()

add_library(${PROJECT_NAME} STATIC
  ${ALL_MAGIC_GET_FILES})

###########
# INSTALL #
###########

include(GNUInstallDirs)

install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  COMPONENT library)

set_target_properties(${PROJECT_NAME} PROPERTIES
  LINKER_LANGUAGE CXX
  CXX_STANDARD 17
	CXX_STANDARD_REQUIRED ON)    
	
add_definitions(-DUNICODE 
	-D_UNICODE)