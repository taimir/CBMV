project(cpython_exam1)
cmake_minimum_required(VERSION 2.8)
add_definitions(-std=c++14)

#set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")
set(CMAKE_CXX_FLAGS "-Wall")
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse4")
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse4.1")
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse4.1 -march=core-avx2 -O3 -funroll-loops -ftree-vectorizer-verbose=1")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse4 -O3 -funroll-loops -ftree-vectorizer-verbose=1")

#set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall")
set(CMAKE_BUILD_TYPE Release)

set(OpenCV_DIR "/usr/local/")
find_package(OpenCV 3 REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS} 
				# add the following line for vision 6 server
				/usr/local/lib/python3.5/dist-packages/numpy/core/include
	)

# see https://stackoverflow.com/questions/6646405/how-do-you-add-boost-libraries-in-cmakelists-txt
set(Boost_USE_STATIC_LIBS OFF) 
set(Boost_USE_MULTITHREADED ON)  
set(Boost_USE_STATIC_RUNTIME OFF) 
find_package( Boost REQUIRED COMPONENTS python-py35 system filesystem )
include_directories(${BOOST_INCLUDE_DIRS})
find_package(PythonLibs 3.5 REQUIRED)
include_directories(${PYTHON_INCLUDE_DIRS})

MESSAGE(STATUS "** Search Boost root: ${BOOST_ROOT}")
MESSAGE(STATUS "** Boost Include: ${Boost_INCLUDE_DIRS}")
MESSAGE(STATUS "** Boost Libraries: ${Boost_LIBRARIES}")
MESSAGE("Include dirs of Python: " ${PYTHON_INCLUDE_DIRS} )
MESSAGE("Libs of Python: " ${PYTHON_LIBRARIES} )

FIND_PACKAGE( OpenMP REQUIRED)
if(OPENMP_FOUND)
		message("OPENMP FOUND")
		set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
		set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
		set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
endif()


SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
#SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

add_library(rectification SHARED rectification/rectification.cpp)
target_link_libraries( rectification  ${Boost_PYTHON_LIBRARY} ${Python_LIBRARIES} ${OpenCV_LIBS})

#add_library(matchers SHARED matchers/matchers.cpp matchers/censusmmx.cpp matchers/nccmmx.cpp matchers/sadmmx.cpp )
add_library(matchers SHARED matchers/matchers.cpp)
target_link_libraries( matchers  ${Boost_PYTHON_LIBRARY} ${Python_LIBRARIES} ${OpenCV_LIBS})

add_library(featextract SHARED featextract/featextract.cpp)
target_link_libraries(featextract  ${Boost_PYTHON_LIBRARY} ${Python_LIBRARIES} ${OpenCV_LIBS})
