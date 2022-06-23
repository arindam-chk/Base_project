project(Base_project)
cmake_minimum_required(VERSION 3.0)
enable_testing()

#Add_directory only for non-app submodules
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/core_submodule ${CMAKE_CURRENT_SOURCE_DIR}/core_submodule/build)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/core_submodule/src)


#Build the apps in the usual way as already there in cogent
file(GLOB_RECURSE APPS_SRCS ${CMAKE_CURRENT_SOURCE_DIR}/apps/**/app*.cc)
foreach(app_src ${APPS_SRCS})
  get_filename_component(APP_DIRNAME ${app_src} DIRECTORY)
  get_filename_component(APP_FILENAME ${app_src} NAME_WE)
  string(REPLACE ${COGENT_ROOT}/apps/ "" APP_TEMP_NAME ${APP_DIRNAME})
  string(REPLACE "/" "_" APP_TEMP1_NAME ${APP_TEMP_NAME})
  set(APP_NAME ${APP_TEMP1_NAME}_${APP_FILENAME})
  message(STATUS "found app  : " ${APP_NAME})
  add_executable(${APP_NAME} ${app_src})
  add_test(NAME ${APP_NAME} COMMAND ${APP_NAME})
  target_link_libraries(${APP_NAME} core_submodule pthread)
  set_target_properties(${APP_NAME} PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${APP_DIRNAME})
  set_target_properties(${APP_NAME} PROPERTIES RUNTIME_OUTPUT_NAME "run_${CMAKE_SYSTEM_PROCESSOR}")
endforeach()
