# ****************************************************************************
# * Project:  wxGIS
# * Purpose:  cmake script
# * Author:   Dmitry Baryshnikov (aka Bishop), polimax@mail.ru
# ****************************************************************************
# *   Copyright (C) 2013-2014 Dmitry Baryshnikov
# *
# *    This program is free software: you can redistribute it and/or modify
# *    it under the terms of the GNU General Public License as published by
# *    the Free Software Foundation, either version 2 of the License, or
# *    (at your option) any later version.
# *
# *    This program is distributed in the hope that it will be useful,
# *    but WITHOUT ANY WARRANTY; without even the implied warranty of
# *    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# *    GNU General Public License for more details.
# *
# *    You should have received a copy of the GNU General Public License
# *    along with this program.  If not, see <http://www.gnu.org/licenses/>.
# ****************************************************************************
cmake_minimum_required (VERSION 2.8)
set(PROJECT_NAME framework)

include(lib)
include(common)

set(LIB_HEADERS ${WXGIS_CURRENT_SOURCE_DIR}/include/wxgis/framework)
set(LIB_SOURCES ${WXGIS_CURRENT_SOURCE_DIR}/src/framework)

if(WIN32)
    set(wxWidgets_EXCLUDE_COMMON_LIBRARIES TRUE)
endif(WIN32)

find_package(wxWidgets 2.9 REQUIRED core base xml html aui adv propgrid)
# wxWidgets include (this will do all the magic to configure everything)
if(wxWidgets_FOUND)
    include(${wxWidgets_USE_FILE})
endif(wxWidgets_FOUND)

if(wxGIS_USE_PROJ)
    find_package(PROJ REQUIRED)
    if(PROJ_FOUND)
        include_directories(${PROJ_INCLUDE_DIR})
        add_definitions(-DHAVE_PROJ -DPROJ_STATIC)
    endif(PROJ_FOUND)
endif(wxGIS_USE_PROJ)

if(wxGIS_HAVE_GTK_INTEGRATION)
     find_package(GTK3)
     if(GTK3_FOUND)
        include_directories(${GTK3_INCLUDE_DIRS})
        add_definitions(-DHAVE_GTK3)
        message(STATUS "GTK3 libs=[${GTK3_LIBRARIES}] headers=[${GTK3_INCLUDE_DIRS}]")
     else(GTK3_FOUND)
        find_package(GTK2 REQUIRED)
        if(GTK2_FOUND)
            include_directories(${GTK2_INCLUDE_DIRS})
            add_definitions(-DHAVE_GTK2)
            message(STATUS "GTK2 libs=[${GTK2_LIBRARIES}] headers=[${GTK2_INCLUDE_DIRS}]")
        endif(GTK2_FOUND)
    endif(GTK3_FOUND)
endif(wxGIS_HAVE_GTK_INTEGRATION)

if(wxGIS_HAVE_UNITY_INTEGRATION)
     find_package(UNITY REQUIRED)
     if(UNITY_FOUND)
        include_directories(${UNITY_INCLUDE_DIR})
        add_definitions(-DHAVE_UNITY)
    endif(UNITY_FOUND)
    message(STATUS "UNITY libs=[${UNITY_LIBRARIES}] headers=[${UNITY_INCLUDE_DIR}]")
endif(wxGIS_HAVE_UNITY_INTEGRATION)

set(PROJECT_HHEADERS ${PROJECT_HHEADERS}
    ${LIB_HEADERS}/accelerator.h
    ${LIB_HEADERS}/addcommanddlg.h
    ${LIB_HEADERS}/animation.h
    ${LIB_HEADERS}/applicationbase.h
    ${LIB_HEADERS}/application.h
    ${LIB_HEADERS}/applicationex.h
    ${LIB_HEADERS}/checklist.h
    ${LIB_HEADERS}/command.h
    ${LIB_HEADERS}/commandbar.h
    ${LIB_HEADERS}/createcbdlg.h
    ${LIB_HEADERS}/customizedlg.h
    ${LIB_HEADERS}/framework.h
    ${LIB_HEADERS}/keycodedlg.h
    ${LIB_HEADERS}/menubar.h
    ${LIB_HEADERS}/progressdlg.h
    ${LIB_HEADERS}/progressor.h
    ${LIB_HEADERS}/propertypages.h
    ${LIB_HEADERS}/statusbar.h
    ${LIB_HEADERS}/toolbarmenu.h
    ${LIB_HEADERS}/dataobject.h
    ${LIB_HEADERS}/tabstyle.h
    ${LIB_HEADERS}/icon.h
)

set(PROJECT_CSOURCES ${PROJECT_CSOURCES}
    ${LIB_SOURCES}/accelerator.cpp
    ${LIB_SOURCES}/addcommanddlg.cpp
    ${LIB_SOURCES}/animation.cpp
    ${LIB_SOURCES}/applicationbase.cpp
    ${LIB_SOURCES}/application.cpp
    ${LIB_SOURCES}/applicationex.cpp
    ${LIB_SOURCES}/checklist.cpp
    ${LIB_SOURCES}/command.cpp
    ${LIB_SOURCES}/commandbar.cpp
    ${LIB_SOURCES}/createcbdlg.cpp
    ${LIB_SOURCES}/customizedlg.cpp
    ${LIB_SOURCES}/keycodedlg.cpp
    ${LIB_SOURCES}/menubar.cpp
    ${LIB_SOURCES}/progressdlg.cpp
    ${LIB_SOURCES}/progressor.cpp
    ${LIB_SOURCES}/propertypages.cpp
    ${LIB_SOURCES}/statusbar.cpp
    ${LIB_SOURCES}/toolbarmenu.cpp
    ${LIB_SOURCES}/dataobject.cpp
    ${LIB_SOURCES}/tabstyle.cpp
    ${LIB_SOURCES}/icon.cpp
)

add_definitions(-DWXMAKINGDLL_GIS_FRW -DwxUSE_GUI=1)
set( WXGISFRAMEWORK_LIB_NAME ${LIB_NAME} PARENT_SCOPE )

add_library(${LIB_NAME} SHARED ${PROJECT_HHEADERS} ${PROJECT_CSOURCES})

if(wxWidgets_FOUND)
   target_link_libraries(${LIB_NAME} ${wxWidgets_LIBRARIES})
endif(wxWidgets_FOUND)

if(GDAL_FOUND)
    target_link_libraries(${LIB_NAME} ${GDAL_LIBRARIES})
endif(GDAL_FOUND)

if(PROJ_FOUND)
    target_link_libraries(${LIB_NAME} ${PROJ_LIBRARY})
endif(PROJ_FOUND)

if(GTK3_FOUND)
   target_link_libraries(${LIB_NAME} ${GTK3_LIBRARIES})
elseif(GTK2_FOUND)
   target_link_libraries(${LIB_NAME} ${GTK2_LIBRARIES})
endif(GTK3_FOUND)

if(UNITY_FOUND)
   target_link_libraries(${LIB_NAME} ${UNITY_LIBRARIES})
endif(UNITY_FOUND)

target_link_libraries(${LIB_NAME} ${WXGISCORE_LIB_NAME})

include(installlib)

