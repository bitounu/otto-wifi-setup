cmake_minimum_required(VERSION 2.8) 

# --- BEGIN your changes here --
project(otto-wifi-setup)
# --- END your changes here --

string(TOUPPER "${PROJECT_NAME}" PROJECT_NAME_UPPER)
string(REPLACE "-" "_" PROJECT_NAME_UPPER ${PROJECT_NAME_UPPER})

set( ${PROJECT_NAME}_MAJOR_VERSION 0 )
set( ${PROJECT_NAME}_MINOR_VERSION 1 )
set( ${PROJECT_NAME}_PATCH_VERSION 0 )
set( ${PROJECT_NAME}_VERSION ${PROJECT_NAME}_MAJOR_VERSION.${PROJECT_NAME}_MINOR_VERSION.${PROJECT_NAME}_PATCH_VERSION )

# --- BEGIN your changes here --
find_package( OpenSSL REQUIRED )
find_package( Threads REQUIRED )

message("PROJECT_NAME=${PROJECT_NAME}")
message("PROJECT_NAME_UPPER=${PROJECT_NAME_UPPER}")

add_executable( otto-wifi-setup-server otto-wifi-setup-server.c fossa.c )
target_link_libraries( otto-wifi-setup-server ${OPENSSL_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT} )
install( TARGETS otto-wifi-setup-server EXPORT ${PROJECT_NAME}Targets RUNTIME DESTINATION bin )

export(TARGETS otto-wifi-setup-server FILE "${PROJECT_BINARY_DIR}/${PROJECT_NAME}Targets.cmake" )
export(PACKAGE ${PROJECT_NAME})
# --- END your changes here --

#... for the build tree
set(CONF_INCLUDE_DIRS "${PROJECT_SOURCE_DIR}" "${PROJECT_BINARY_DIR}")
configure_file(${PROJECT_NAME}Config.cmake.in "${PROJECT_BINARY_DIR}/${PROJECT_NAME}Config.cmake" @ONLY)

# ... for the install tree
set(CONF_INCLUDE_DIRS "\${${PROJECT_NAME_UPPER}_CMAKE_DIR}/include")
configure_file(${PROJECT_NAME}Config.cmake.in "${PROJECT_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/${PROJECT_NAME}Config.cmake" @ONLY)

# create ${PROJECT_NAME}ConfigVersion.cmake
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
  VERSION ${${PROJECT_NAME}_VERSION}
  COMPATIBILITY AnyNewerVersion
)

# Install the ${PROJECT_NAME}Config.cmake and ${PROJECT_NAME}ConfigVersion.cmake
install(
  FILES
  "${PROJECT_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/${PROJECT_NAME}Config.cmake"
  "${PROJECT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
  DESTINATION lib/CMake/${PROJECT_NAME} COMPONENT dev)

# Install the export set for use with the install-tree
install(EXPORT ${PROJECT_NAME}Targets DESTINATION lib/CMake/${PROJECT_NAME} COMPONENT dev)
