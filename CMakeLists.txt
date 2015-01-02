cmake_minimum_required(VERSION 2.8.3)
project (stair_perception)

set(CMAKE_CXX_FLAGS "-W -Wall -Wno-unused-parameter -frounding-math")

find_package(catkin REQUIRED COMPONENTS roslib roscpp std_msgs sensor_msgs cv_bridge)

FIND_PACKAGE( OpenCV REQUIRED )

find_package(PCL 1.6 REQUIRED COMPONENTS common io search filters kdtree registration visualization surface segmentation)
#include_directories(${PCL_INCLUDE_DIRS} /usr/local/MATLAB/R2012a/extern/include)
include_directories(${PCL_INCLUDE_DIRS} )
#link_directories(${PCL_LIBRARY_DIRS} /usr/local/MATLAB/R2012a/bin/glnxa64)
#link_libraries(osg osgText osgViewer osgGA CGAL eng mat mx boost_program_options boost_thread boost_filesystem boost_system)
add_definitions(${PCL_DEFINITIONS})

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)
#PCL_ADD_INCLUDES(${SUBSYS_NAME} ${SUBSYS_NAME} ${incs})
#PCL_ADD_INCLUDES(${SUBSYS_NAME} ${SUBSYS_NAME}/impl ${impl_incs})

#add_library(fhg_bindings src/fhg_bindings.cpp)
#target_link_libraries(fhg_bindings ${PCL_LIBRARIES} )
#add_library(fhg_pcl src/fhg_pcl.cpp)
#target_link_libraries(fhg_pcl ${PCL_LIBRARIES} )

catkin_package()

add_library(fhg_binding_common STATIC src/fhg_binding_common.cpp)
target_link_libraries(fhg_binding_common ${PCL_LIBRARIES} )

add_library(fhg_binding_filters STATIC src/fhg_binding_filters.cpp)
target_link_libraries(fhg_binding_filters ${PCL_LIBRARIES}  )

add_library(fhg_binding_sac STATIC src/fhg_binding_sac.cpp)
target_link_libraries(fhg_binding_sac ${PCL_LIBRARIES}  )

add_library(fhg_binding_kdtree STATIC src/fhg_binding_kdtree.cpp)
target_link_libraries(fhg_binding_kdtree ${PCL_LIBRARIES}  fhg_binding_common)

add_library(fhg_binding_search STATIC src/fhg_binding_search.cpp)
target_link_libraries(fhg_binding_search ${PCL_LIBRARIES} fhg_binding_common fhg_binding_kdtree)

add_library(fhg_binding_surface STATIC src/fhg_binding_surface.cpp)
target_link_libraries(fhg_binding_surface ${PCL_LIBRARIES}  fhg_binding_common fhg_binding_search)

add_library(fhg_binding_features STATIC src/fhg_binding_features.cpp)
target_link_libraries(fhg_binding_features ${PCL_LIBRARIES} fhg_binding_common fhg_binding_kdtree fhg_binding_search fhg_binding_filters )

add_library(fhg_binding_segmentation STATIC src/fhg_binding_segmentation.cpp)
target_link_libraries(fhg_binding_segmentation ${PCL_LIBRARIES} fhg_binding_search fhg_binding_sac fhg_binding_filters fhg_binding_features )

add_library(fhg_binding_registration STATIC src/fhg_binding_registration.cpp)
target_link_libraries(fhg_binding_registration ${PCL_LIBRARIES} fhg_binding_common fhg_binding_search fhg_binding_sac fhg_binding_features fhg_binding_filters )

add_library(fhg_binding_visualization STATIC src/fhg_binding_visualization.cpp)
target_link_libraries(fhg_binding_visualization ${PCL_LIBRARIES}  )

set(FHG_PCL_BINDINGS fhg_binding_common fhg_binding_filters fhg_binding_sac fhg_binding_kdtree fhg_binding_search fhg_binding_surface fhg_binding_features fhg_binding_segmentation fhg_binding_registration fhg_binding_visualization)

add_library(fhg_kdtree STATIC src/pcl/fhg_kdtree.cpp)
target_link_libraries(fhg_kdtree ${PCL_LIBRARIES} )

add_library(fhg_registration STATIC src/pcl/fhg_registration.cpp)
target_link_libraries(fhg_registration ${PCL_LIBRARIES}  )

add_library(fhg_utils STATIC src/pcl/fhg_utils.cpp)
target_link_libraries(fhg_utils ${PCL_LIBRARIES} ${FHG_PCL_BINDINGS})

add_library(fhg_surface STATIC src/pcl/fhg_surface.cpp)
target_link_libraries(fhg_surface ${PCL_LIBRARIES}  ${FHG_PCL_BINDINGS})

add_library(fhg_common STATIC src/pcl/fhg_common.cpp)
target_link_libraries(fhg_common ${PCL_LIBRARIES} ${FHG_PCL_BINDINGS} fhg_io fhg_utils)

add_library(fhg_types STATIC src/pcl/fhg_types.cpp)
target_link_libraries(fhg_types ${PCL_LIBRARIES} ${FHG_PCL_BINDINGS})

add_library(fhg_opencv STATIC src/pcl/fhg_opencv.cpp)
target_link_libraries(fhg_opencv ${PCL_LIBRARIES} ${OpenCV_LIBS} ${FHG_PCL_BINDINGS})

add_library(fhg_histogram STATIC src/pcl/fhg_histogram.cpp)
target_link_libraries(fhg_common ${PCL_LIBRARIES} ${FHG_PCL_BINDINGS} fhg_common fhg_types)

add_library(fhg_models STATIC src/pcl/fhg_models.cpp)
target_link_libraries(fhg_models ${PCL_LIBRARIES} ${FHG_PCL_BINDINGS} fhg_common fhg_types fhg_registration)

add_library(fhg_io STATIC src/pcl/fhg_io.cpp)
target_link_libraries(fhg_io ${PCL_LIBRARIES} ${FHG_PCL_BINDINGS} fhg_utils fhg_models)

add_library(fhg_segmentation STATIC src/pcl/fhg_segmentation.cpp)
target_link_libraries(fhg_models ${PCL_LIBRARIES} ${FHG_PCL_BINDINGS} fhg_common fhg_surface fhg_types)

add_library(fhg_apps STATIC src/pcl/fhg_apps.cpp)
target_link_libraries(fhg_models ${PCL_LIBRARIES} ${FHG_PCL_BINDINGS} fhg_kdtree fhg_utils fhg_io fhg_common fhg_histogram fhg_types fhg_models fhg_segmentation fhg_opencv fhg_registration fhg_surface )

#add_library(binding_lib src/binding_lib.cpp)
#target_link_libraries(binding_lib ${PCL_LIBRARIES} )

set(FHG_PCL_LIBS fhg_kdtree fhg_utils fhg_io fhg_common fhg_histogram fhg_types fhg_models fhg_segmentation fhg_opencv fhg_registration fhg_surface )
#set(FHG_PCL_LIBS fhg_kdtree fhg_utils fhg_io fhg_common fhg_histogram  )
#set(FHG_PCL_LIBS fhg_pcl)

add_executable(stairdetectorpcds src/pcl/apps/stairdetectorpcds.cpp)
target_link_libraries(stairdetectorpcds boost_thread boost_filesystem boost_system ${OpenCV_LIBS} ${PCL_LIBRARIES} ${FHG_PCL_BINDINGS} fhg_apps)


##########################################################################################################

add_executable(playground tests/playground.cpp)


add_executable(normalsvis src/pcl/apps/normalsvis.cpp)
target_link_libraries(normalsvis boost_thread boost_filesystem boost_system ${PCL_LIBRARIES})

#target_link_libraries(playground boost_thread boost_filesystem boost_system ${PCL_LIBRARIES}  )
add_executable(pcd_to_rgb src/pcl/apps/pcd_to_rgb.cpp)
target_link_libraries(pcd_to_rgb ${OpenCV_LIBS} ${PCL_LIBRARIES} )

add_executable(icp_vis tests/icp_vis.cpp)
target_link_libraries(icp_vis ${OpenCV_LIBS} ${PCL_LIBRARIES} )



