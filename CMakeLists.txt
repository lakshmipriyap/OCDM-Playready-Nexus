# If not stated otherwise in this file or this component's license file the
# following copyright and licenses apply:
#
# Copyright 2020 RDK Management
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required(VERSION 2.8)

set(DRM_PLUGIN_NAME PlayReady)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

if(NOT CMAKE_CROSSCOMPILING)
   set(STAGING_DIR "${CMAKE_INSTALL_PREFIX}")
else()
   # TODO: this can be multiple directories
   set(STAGING_DIR "${CMAKE_FIND_ROOT_PATH}")
endif()

find_package(PlayReady REQUIRED)
find_package(BcmNxServer REQUIRED)
find_package(WPEFramework REQUIRED)

file(GLOB DRM_PLUGIN_INCLUDES *.h)

set(DRM_PLUGIN_INCLUDES
    ${DRM_PLUGIN_INCLUDES}
    ${WPEFRAMEWORK_INCLUDE_DIRS})

set(DRM_PLUGIN_LIBS
    ${PLAYREADY_LIBRARIES}
    ${WPEFRAMEWORK_LIBRARY_WPEFrameworkCore}
    ${MODULE_NAME})

set(DRM_PLUGIN_SOURCES 
    MediaSession.cpp 
    MediaSystem.cpp)

# add the library
add_library(${DRM_PLUGIN_NAME} SHARED ${DRM_PLUGIN_SOURCES})
target_compile_definitions(${DRM_PLUGIN_NAME} PRIVATE ${PLAYREADY_FLAGS})
target_include_directories(${DRM_PLUGIN_NAME} PRIVATE ${PLAYREADY_INCLUDE_DIRS} ${LIBNXCLIENT_INCLUDE_DIRS} ${DRM_PLUGIN_INCLUDES})
target_link_libraries(${DRM_PLUGIN_NAME} ${DRM_PLUGIN_LIBS} ${LIBNXCLIENT_LIBRARY} )
set_target_properties(${DRM_PLUGIN_NAME} PROPERTIES SUFFIX ".drm")
set_target_properties(${DRM_PLUGIN_NAME} PROPERTIES PREFIX "")

#TODO: this should come from platform_app.inc
target_compile_definitions(${DRM_PLUGIN_NAME}
        PRIVATE
            "BSTD_CPU_ENDIAN=BSTD_ENDIAN_LITTLE")


install(TARGETS ${DRM_PLUGIN_NAME} DESTINATION ${CMAKE_INSTALL_PREFIX}/share/WPEFramework/OCDM)

