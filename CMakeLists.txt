# Copyright (C) 2014 iCub Facility
# Authors: Ali Paikan
# CopyPolicy: Released under the terms of the LGPLv2.1 or later, see LGPL.TXT

cmake_minimum_required(VERSION 2.8.9)

find_package(YARP REQUIRED)
list(APPEND CMAKE_MODULE_PATH ${YARP_MODULE_PATH})
list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

# probably not needed ... to be tested
set(CMAKE_INCLUDE_CURRENT_DIR TRUE)

include(YarpPlugin)
include(YarpInstallationHelpers)

# probably not needed ... plugins should set this automatically ... to be tested
set(YARP_FORCE_DYNAMIC_PLUGINS TRUE CACHE INTERNAL "yarp_pm_zfp is always built with dynamic plugins")

yarp_configure_external_installation(yarp)

yarp_prepare_plugin(zfp        TYPE ZfpMonitorObject
                               INCLUDE zfpPortmonitor.h
                               CATEGORY portmonitor
                               #DEPENDS "CREATE_OPTIONAL_CARRIERS;ENABLE_yarpcar_portmonitor" #this make sense only when inside yarp repo
                               )

if(NOT SKIP_zfp)

find_package(ZFP REQUIRED)

if (ZFP_FOUND)
        message("-- Found ZFP library: ${ZFP_LIBRARY}")
        include_directories(${ZFP_INCLUDE_DIR})
        else (ZFP_FOUND)
        message("-- Did not find ZFP library.")
        return()
        endif (ZFP_FOUND)

include_directories(${YARP_INCLUDE_DIRS} ${ZFP_INCLUDE_LOCAL})

yarp_add_plugin(yarp_pm_zfp
                zfpPortmonitor.cpp
                zfpPortmonitor.h)
target_link_libraries(yarp_pm_zfp ${YARP_LIBRARIES} ${ZFP_LIBRARY})

yarp_install(TARGETS yarp_pm_zfp
             EXPORT YARP
             COMPONENT runtime
             LIBRARY DESTINATION ${YARP_DYNAMIC_PLUGINS_INSTALL_DIR}
             ARCHIVE DESTINATION ${YARP_STATIC_PLUGINS_INSTALL_DIR})

yarp_install(FILES zfp.ini
             COMPONENT runtime
             DESTINATION ${YARP_PLUGIN_MANIFESTS_INSTALL_DIR})
endif(NOT SKIP_zfp)
