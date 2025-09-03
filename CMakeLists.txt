cmake_minimum_required(VERSION 3.21)
set(PROJECT_NAME CLAPPy)

if(NOT DEFINED CLAPPY_VERSION)
	if(DEFINED SKBUILD_PROJECT_VERSION)
		# If using skbuild, set the version from the project version
		set(CLAPPY_VERSION ${SKBUILD_PROJECT_VERSION})
	else()
		# If not using skbuild, set a default version
		set(CLAPPY_VERSION 0.1.0)
	endif()
endif()

project(${PROJECT_NAME} LANGUAGES CXX VERSION ${CLAPPY_VERSION})

# Use the new FindPython mode
set(PYBIND11_FINDPYTHON ON)

# Try to find pybind11 first
find_package(pybind11 QUIET)
if(NOT pybind11_FOUND)
    include(FetchContent)
    FetchContent_Declare(
        pybind11
        GIT_REPOSITORY https://github.com/pybind/pybind11.git
        GIT_TAG 0630807c3070287c716f6be3eacb00b8816b4215 # v2.11.1
    )
    FetchContent_MakeAvailable(pybind11)
endif()

# Try to find CLAP quietly first
find_package(CLAP QUIET)
if(NOT CLAP_FOUND)
    message (STATUS "CMAKE ############## Finding clap: ### second try!")
    include(FetchContent)
    FetchContent_Declare(
        CLAP
          GIT_REPOSITORY https://github.com/fporrmann/CLAP.git
        # GIT_REPOSITORY https://github.com/mkaiser/CLAP.git
        # GIT_REPOSITORY /data_fast/storage/mkaiser/hadefla/git_hadefla/zybo_z7/CLAP/
    )
    FetchContent_MakeAvailable(CLAP)

    find_package(CLAP REQUIRED)
endif()

# message (DEBUG "CLAP_INCLUDE_DIR=${CLAP_INCLUDE_DIR}")

# include_directories(${CLAP_INCLUDE_DIR})

add_definitions(-DMODULE_NAME=${PROJECT_NAME})

# If CLAPPY_VERSION is defined add it as a preprocessor definition
if(DEFINED CLAPPY_VERSION)
	add_definitions(-DCLAPPY_VERSION=${CLAPPY_VERSION})
endif()

pybind11_add_module(${PROJECT_NAME} 
    src/CLAPPy.cpp
)

# target_link_libraries(${PROJECT_NAME} PRIVATE ${CLAP_LIBS})
target_link_libraries(${PROJECT_NAME} PRIVATE CLAP::CLAP)

install(TARGETS ${PROJECT_NAME} DESTINATION CLAPPy)
install(DIRECTORY CLAPPy/ DESTINATION CLAPPy)