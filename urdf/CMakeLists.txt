cmake_minimum_required(VERSION 3.5)
project(urdf)

find_package(ament_cmake REQUIRED)
find_package(urdfdom REQUIRED)
find_package(urdfdom_headers REQUIRED)
message("urdfdom_headers ${urdfdom_headers_INCLUDE_DIRS}")
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
find_package(TinyXML REQUIRED)

# Find version components
if(NOT urdfdom_headers_VERSION)
set(urdfdom_headers_VERSION "0.0.0")
endif()
string(REGEX REPLACE "^([0-9]+).*" "\\1" URDFDOM_HEADERS_MAJOR_VERSION "${urdfdom_headers_VERSION}")
string(REGEX REPLACE "^[0-9]+\\.([0-9]+).*" "\\1" URDFDOM_HEADERS_MINOR_VERSION "${urdfdom_headers_VERSION}")
string(REGEX REPLACE "^[0-9]+\\.[0-9]+\\.([0-9]+).*" "\\1" URDFDOM_HEADERS_REVISION_VERSION "${urdfdom_headers_VERSION}")
set(generated_compat_header "${CMAKE_CURRENT_SOURCE_DIR}/include/${PROJECT_NAME}/urdfdom_compatibility.h")
include_directories(include)
configure_file(urdfdom_compatibility.h.in "${generated_compat_header}" @ONLY)

add_compile_options(-std=c++11)

install(FILES ${generated_compat_header} DESTINATION include)

include_directories(
  include
  ${urdfdom_INCLUDE_DIRS}
  ${urdfdom_headers_INCLUDE_DIRS}
  ${TinyXML_INCLUDE_DIRS}
  )

add_library(${PROJECT_NAME} SHARED src/model.cpp)
message("urdfdom libs ${urdfdom_LIBRARIES}")
target_link_libraries(${PROJECT_NAME} ${TinyXML_LIBRARIES} ${urdfdom_LIBRARIES})

if(APPLE)
  set_target_properties(${PROJECT_NAME} PROPERTIES LINK_FLAGS "-undefined dynamic_lookup")
endif(APPLE)

install(TARGETS ${PROJECT_NAME} 
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME})

set(urdf_lib_dir "$<TARGET_FILE_DIR:${PROJECT_NAME}>")
ament_export_libraries(${PROJECT_NAME})
ament_export_include_directories(include)
ament_package()
