# package level CMakeLists

cmake_minimum_required (VERSION 3.5)

project(repo2_2
    VERSION 1.0
)


message("CMAKE_PROJECT_NAME: ${CMAKE_PROJECT_NAME}")
message("CMAKE_SOURCE_DIR: ${CMAKE_SOURCE_DIR}")
message("CMAKE_BINARY_DIR: ${CMAKE_BINARY_DIR}")

message("PROJECT_NAME: ${PROJECT_NAME}")
message("PROJECT_SOURCE_DIR: ${PROJECT_SOURCE_DIR}")
message("PROJECT_BINARY_DIR: ${PROJECT_BINARY_DIR}")

message("CMAKE_CURRENT_SOURCE_DIR: ${CMAKE_CURRENT_SOURCE_DIR}")
message("CMAKE_CURRENT_BINARY_DIR: ${CMAKE_CURRENT_BINARY_DIR}")
message("CMAKE_CURRENT_LIST_DIR: ${CMAKE_CURRENT_LIST_DIR}")

message("CMAKE_PREFIX_PATH: ${CMAKE_PREFIX_PATH}")
message("CMAKE_MODULE_PATH: ${CMAKE_MODULE_PATH}")
message("CMAKE_TOOLCHAIN_FILE: ${CMAKE_TOOLCHAIN_FILE}")




# configurations

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)




function(echo_target_property tgt prop)

    get_target_property(prop_val ${tgt} ${prop})
    message(${tgt} ", " ${prop} ": " ${prop_val})

endfunction()


function(echo_target tgt)
  if(NOT TARGET ${tgt})
    message("There is no target named '${tgt}'")
    return()
  endif()
 
  set(props
    OUTPUT_NAME
    TYPE
    SOURCES
    VERSION
    BUNDLE
    COMPILE_DEFINITIONS
    COMPILE_FLAGS
    COMPILE_OPTIONS
    INCLUDE_DIRECTORIES
    LINKER_LANGUAGE
    LINK_DEPENDS
    LINK_FLAGS
    LINK_LIBRARIES
    LINK_INTERFACE_LIBRARIES
    DEFINE_SYMBOL
    HAS_CXX
    IMPORTED
    IMPORTED_LINK_DEPENDENT_LIBRARIES
    IMPORTED_LINK_INTERFACE_LIBRARIES
    PRIVATE_HEADER
    PUBLIC_HEADER
    INSTALL_RPATH
  )
 
  foreach(p ${props})
    echo_target_property("${tgt}" "${p}")
  endforeach()

endfunction()



# dependencies


# find_package(Repo2_1 REQUIRED)
include(Repo2_2Dependencies.cmake)



# targets and directories
add_subdirectory(library1)
add_subdirectory(binary1)




# package export


set(PACKAGE_PATH_NAME repo2_2)
set(PACKAGE_FILE_NAME Repo2_2)

# now only supports /usr/local installation




#  libraries
install(
    TARGETS
        library1
        # library1_1
    EXPORT ${PACKAGE_PATH_NAME}-export
    LIBRARY DESTINATION lib/${PACKAGE_PATH_NAME}
    ARCHIVE DESTINATION lib/${PACKAGE_PATH_NAME}
)


#  headers
install(
  FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/library1/public/library1.h
  DESTINATION include/${PACKAGE_PATH_NAME}/library1/public
)

# install(
#   FILES
#     ${CMAKE_CURRENT_SOURCE_DIR}/library1_1/public/library1_1.h
#   DESTINATION "include/repo1/library1_1/public"
# )










#  targets
install(
    EXPORT ${PACKAGE_PATH_NAME}-export
    FILE ${PACKAGE_FILE_NAME}Targets.cmake
    NAMESPACE ${PACKAGE_FILE_NAME}::
    DESTINATION lib/cmake/${PACKAGE_PATH_NAME}
)


export(
    EXPORT ${PACKAGE_PATH_NAME}-export
    FILE ${CMAKE_CURRENT_BINARY_DIR}/${PACKAGE_FILE_NAME}Targets.cmake
    NAMESPACE ${PACKAGE_FILE_NAME}::
)

# # export(PACKAGE Repo1)


#  package config
include(CMakePackageConfigHelpers)

configure_package_config_file(
    ${CMAKE_CURRENT_LIST_DIR}/${PACKAGE_FILE_NAME}Config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/${PACKAGE_FILE_NAME}Config.cmake
    INSTALL_DESTINATION lib/cmake/${PACKAGE_PATH_NAME}
)

#  the config.cmake here is not suitable for installation
install(
  FILES ${CMAKE_CURRENT_BINARY_DIR}/${PACKAGE_FILE_NAME}Config.cmake
  DESTINATION lib/cmake/${PACKAGE_PATH_NAME}
)

file(COPY ${CMAKE_CURRENT_LIST_DIR}/${PACKAGE_FILE_NAME}Dependencies.cmake
    DESTINATION ${CMAKE_BINARY_DIR}/
)

install(
  FILES ${CMAKE_CURRENT_LIST_DIR}/${PACKAGE_FILE_NAME}Dependencies.cmake
  DESTINATION lib/cmake/${PACKAGE_PATH_NAME}/
)



#  package config version
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/${PACKAGE_FILE_NAME}ConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY ExactVersion
)

install(
  FILES ${CMAKE_CURRENT_BINARY_DIR}/${PACKAGE_FILE_NAME}ConfigVersion.cmake
  DESTINATION lib/cmake/${PACKAGE_FILE_NAME}
)

