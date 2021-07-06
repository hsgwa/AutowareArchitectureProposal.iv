cmake_minimum_required(VERSION 3.5)
project(pointcloud_preprocessor)

### Compile options
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
  set(CMAKE_CXX_EXTENSIONS OFF)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wno-unused-parameter -Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake_auto REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(Boost REQUIRED)
find_package(PCL REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(OpenMP)
ament_auto_find_build_dependencies()


###########
## Build ##
###########

include_directories(
  include
  ${Boost_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
)

ament_auto_add_library(pointcloud_preprocessor_filter SHARED
  src/filter.cpp
  src/concatenate_data/concatenate_data_nodelet.cpp
  src/crop_box_filter/crop_box_filter_nodelet.cpp
)

target_link_libraries(pointcloud_preprocessor_filter
  ${Boost_LIBRARIES}
  ${OpenCV_LIBRARIES}
  ${PCL_LIBRARIES}
)

if(OPENMP_FOUND)
  set_target_properties(pointcloud_preprocessor_filter PROPERTIES
    COMPILE_FLAGS ${OpenMP_CXX_FLAGS}
    LINK_FLAGS ${OpenMP_CXX_FLAGS}
  )
endif()

# ========== Compare Map Filter ==========
# -- Distance Based Compare Map Filter --

# -- Voxel Based Approximate Compare Map Filter --

# -- Voxel Based Compare Map Filter --

# -- Voxel Distance Based Compare Map Filter --


# ========== Concatenate data ==========


# ========== CropBox ==========
rclcpp_components_register_node(pointcloud_preprocessor_filter
  PLUGIN "pointcloud_preprocessor::CropBoxFilterComponent"
  EXECUTABLE crop_box_filter_node)


# ========== Down Sampler Filter ==========
# -- Voxel Grid Downsample Filter --

# -- Random Downsample Filter --


# ========== Ground Filter ==========
# -- Ray Ground Filter --

# -- RANSAC Ground Filter --


# ========== Outlier Filter ==========
# -- Ring Outlier Filter --

# -- Voxel Grid Outlier Filter --

# -- Radius Search 2D Outlier Filter --


# ========== Passthrough Filter ==========
# -- Passthrough Filter --

# -- Passthrough Filter Uint16 --


# ========== Pointcloud Accumulator Filter ==========


# ========== Vector Map Filter ==========
rclcpp_components_register_node(pointcloud_preprocessor_filter
  PLUGIN "pointcloud_preprocessor::Lanelet2MapFilterComponent"
  EXECUTABLE vector_map_filter_node)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
  list(APPEND AMENT_LINT_AUTO_EXCLUDE
    # To avoid conflicts between cpplint and uncrustify w.r.t. inclusion guards
    ament_cmake_uncrustify
  )
endif()

#############
## Install ##
#############

ament_auto_package(INSTALL_TO_SHARE
  launch
)
