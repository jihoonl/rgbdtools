cmake_minimum_required(VERSION 2.8)

project(rgbdtools)

set(CMAKE_CXX_FLAGS_RELEASE "-O3")
set(CMAKE_BUILD_TYPE Release)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

include_directories(${PROJECT_SOURCE_DIR}/include)

find_package(PkgConfig)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake-modules/")

# OpenCV
FIND_PACKAGE( OpenCV REQUIRED )
include_directories(${OpenCV_INCLUDE_DIRS})
link_directories(${OpenCV_LIBRARY_DIRS})
link_libraries(${OpenCV_LIBRARIES})

# PCL
find_package(PCL REQUIRED)
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
link_libraries(${PCL_LIBRARIES})   

# Boost
find_package(Boost REQUIRED)
include_directories(${Boost_INCLUDE_DIRS})
link_libraries(${Boost_LIBRARIES})    

#message("Boost: ${Boost_LIBRARIES}")
#message("OpenCV: ${OpenCV_LIBRARIES}")

# g2o:
#   use script to find g2o
#   if not installed, set G2O_INCLUDE_DIRS manually using -DG2O_INCLUDE_DIRS

find_package(G2O)
include_directories(${G2O_INCLUDE_DIRS})
link_directories(${G2O_LIBRARY_DIRS})
link_libraries(${G2O_LIBRARIES})    

#message("G2O inc. dirs: ${G2O_INCLUDE_DIRS}")
#message("G2O lib. dirs: ${G2O_LIBRARY_DIRS}")
#message("G2O libs: ${G2O_LIBRARIES}")

#suitesparse
FIND_PACKAGE(SuiteSparse)
include_directories(${SUITESPARSE_INCLUDE_DIRS})

#message("SUITESPARSE_INCLUDE_DIRS: ${SUITESPARSE_INCLUDE_DIRS}")

####################################################
# build rgbdtools library

add_library (rgbdtools SHARED
  src/rgbd_frame.cpp
  src/rgbd_keyframe.cpp
  src/rgbd_util.cpp
  src/map_util.cpp
  src/features/feature_detector.cpp
  src/features/orb_detector.cpp
  src/features/surf_detector.cpp
  src/features/gft_detector.cpp
  src/features/star_detector.cpp
  src/registration/motion_estimation.cpp
  src/registration/motion_estimation_icp_prob_model.cpp
  src/graph/keyframe_graph_detector.cpp
  src/graph/keyframe_graph_solver.cpp
  src/graph/keyframe_graph_solver_g2o.cpp)

target_link_libraries (rgbdtools
  ${Boost_LIBRARIES}
  ${OpenCV_LIBRARIES}
  ${PCL_LIBRARIES})
  
####################################################
# build global cloud align applications

add_executable(global_cloud_align
  apps/global_cloud_align.cpp)
  
target_link_libraries (global_cloud_align 
  rgbdtools
  ${Boost_LIBRARIES}
  ${OpenCV_LIBRARIES}
  ${PCL_LIBRARIES}) 

install(TARGETS global_cloud_align DESTINATION bin)

####################################################
# build keyframe associations applications

add_executable(keyframe_associations
  apps/keyframe_associations.cpp)
  
target_link_libraries (keyframe_associations 
  rgbdtools
  ${Boost_LIBRARIES}
  ${OpenCV_LIBRARIES}
  ${PCL_LIBRARIES})
  
install(TARGETS keyframe_associations DESTINATION bin)

install(TARGETS rgbdtools DESTINATION lib)
install(DIRECTORY include/rgbdtools DESTINATION include)

