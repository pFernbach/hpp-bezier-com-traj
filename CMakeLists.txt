cmake_minimum_required(VERSION 2.6)
INCLUDE(cmake/base.cmake)
INCLUDE(cmake/python.cmake)
INCLUDE(cmake/test.cmake)

SET(PROJECT_ORG stonneau)
SET(PROJECT_NAME hpp-bezier-com-traj)
SET(PROJECT_DESCRIPTION
  "Multi contact trajectory generation for the COM using Bezier curves"
  )
SET(PROJECT_URL "https://gitlab.com/${PROJECT_ORG}/${PROJECT_NAME}")

set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/;${PROJECT_SOURCE_DIR}/cmake2/")

SET(CUSTOM_HEADER_DIR "hpp/bezier-com-traj")

SETUP_PROJECT()

# Inhibit all warning messages.
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -w")

# remove flag that makes all warnings into errors
string (REPLACE "-Werror" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
MESSAGE( STATUS "CMAKE_CXX_FLAGS: " ${CMAKE_CXX_FLAGS} )

OPTION (BUILD_PYTHON_INTERFACE "Build the python binding" ON)
OPTION (USE_GLPK "Use sparse lp solver" OFF)
OPTION (PRINT_QHULL_INEQ "generate text file containing last inequality computed" OFF)

IF(BUILD_PYTHON_INTERFACE)
  # search for python
  FINDPYTHON(2.7  REQUIRED)
  find_package( PythonLibs 2.7 REQUIRED )
  include_directories( ${PYTHON_INCLUDE_DIRS} )

  find_package( Boost COMPONENTS python REQUIRED )
  include_directories( ${Boost_INCLUDE_DIR} )
ENDIF(BUILD_PYTHON_INTERFACE)


ADD_REQUIRED_DEPENDENCY(eigen3)
ADD_REQUIRED_DEPENDENCY(hpp-centroidal-dynamics)
ADD_REQUIRED_DEPENDENCY(hpp-spline)
IF(USE_GLPK)
  find_package (glpk REQUIRED)
  add_definitions ( -DUSE_GLPK_SOLVER=1)
  INCLUDE_DIRECTORIES(${GLPK_INCLUDE_DIR})
ENDIF(USE_GLPK)

add_subdirectory(include/${CUSTOM_HEADER_DIR})
add_subdirectory(src)
add_subdirectory(tests)
IF(BUILD_PYTHON_INTERFACE)
  add_subdirectory (python)
ENDIF(BUILD_PYTHON_INTERFACE)

SETUP_PROJECT_FINALIZE()
