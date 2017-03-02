cmake_minimum_required(VERSION 3.5)

project(kdl_parser)

find_package(ament_cmake REQUIRED)
find_package(orocos_kdl REQUIRED)
set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
find_package(TinyXML REQUIRED)
find_package(urdf REQUIRED)
find_package(urdfdom_headers REQUIRED)
include_directories(include ${orocos_kdl_INCLUDE_DIRS} ${urdf_INCLUDE_DIRS} ${urdfdom_headers_INCLUDE_DIRS} ${TinyXML_INCLUDE_DIRS})

link_directories(${orocos_kdl_LIBRARY_DIRS} ${urdf_LIBRARY_DIRS})

find_library(KDL_LIBRARY REQUIRED NAMES orocos-kdl HINTS ${orocos_kdl_LIBRARY_DIRS})

add_compile_options(-std=c++11)

add_library(${PROJECT_NAME} SHARED src/kdl_parser.cpp)
target_link_libraries(${PROJECT_NAME}
  ${TinyXML_LIBRARIES} ${orocos_kdl_LIBRARIES} ${urdf_LIBRARIES})

#add_executable(check_kdl_parser src/check_kdl_parser.cpp )
#target_link_libraries(check_kdl_parser ${PROJECT_NAME})

install(TARGETS ${PROJECT_NAME} 
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME})

install(DIRECTORY cmake
  DESTINATION share/${PROJECT_NAME}/)

set(kdl_parser_lib_dir "$<TARGET_FILE_DIR:${PROJECT_NAME}>")
ament_export_dependencies(TinyXML)
ament_export_include_directories(include)
ament_export_libraries(${PROJECT_NAME})
ament_package()
