# Copyright (C) 2007-2012 LuaDist.
# Created by Peter Kapec
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.
CMAKE_POLICY(SET CMP0048 NEW)

PROJECT(freetype
		LANGUAGES C
        DESCRIPTION "Freetype library"
		VERSION 1.3.1)
CMAKE_MINIMUM_REQUIRED(VERSION 3.14)

include(cmake/dist.cmake)
# Specify library include directories


include_directories(lib)
set(BASE_SRCS
		lib/ttapi.c
		lib/ttcache.c
		lib/ttcalc.c
		lib/ttcmap.c
		lib/ttdebug.c
		lib/ttextend.c
		lib/ttfile.c
		lib/ttgload.c
		lib/ttinterp.c
		lib/ttload.c
		lib/ttmemory.c
		lib/ttmutex.c
		lib/ttobjs.c
		lib/ttraster.c
	)
set(BASE_HDRS
		lib/freetype.h
		lib/fterrid.h
		lib/ftnameid.h
		lib/header.h
		lib/ttcache.h
		lib/ttcalc.h
		lib/ttcmap.h
		lib/ttconfig.h
		lib/ttdebug.h
		lib/ttengine.h
		lib/ttextend.h
		lib/ttfile.h
		lib/ttgload.h
		lib/ttinterp.h
		lib/ttload.h
		lib/ttmemory.h
		lib/ttmutex.h
		lib/ttobjs.h
		lib/ttraster.h
		lib/tttables.h
		lib/tttags.h
		lib/tttypes.h
		)

if(APPLE)
	SET(ARCH_SPECIFIC
			lib/arch/mac/freetype.c
			lib/arch/mac/ft_conf.h
			lib/arch/mac/ttmmap.c)
	SET(ARCH_INCLUDE "${PROJECT_SOURCE_DIR}/lib/arch/mac")
elseif(WIN32)
    set(ARCH_SPECIFIC
			lib/arch/win32/freetype.c
			lib/arch/win32/ft_conf.h)
	SET(ARCH_INCLUDE "${PROJECT_SOURCE_DIR}/lib/arch/win32")
else()
	set(ARCH_SPECIFIC
			lib/arch/unix/freetype.c
			lib/arch/unix/ttmmap.c)
	SET(ARCH_INCLUDE "${PROJECT_SOURCE_DIR}/lib/arch/unix")
endif()

ADD_LIBRARY(freetype STATIC ${BASE_SRCS} ${BASE_HDRS} ${ARCH_INCLUDE})
TARGET_INCLUDE_DIRECTORIES(freetype PUBLIC "${PROJECT_SOURCE_DIR}/lib" ${ARCH_INCLUDE})

set(targets_export_name freetype-targets)

include(CMakePackageConfigHelpers)
set(FREETYPE_CMAKE_DIR "lib/cmake/freetype" CACHE STRING
		"Installation directory for cmake files, relative to ${CMAKE_INSTALL_PREFIX}.")
set(version_config "${PROJECT_BINARY_DIR}/freetype-config-version.cmake")
set(project_config "${PROJECT_BINARY_DIR}/freetype-config.cmake")
set(targets_export_name freetype-targets)
# Generate the version, config and target files into the build directory.
write_basic_package_version_file(
		${version_config}
		VERSION ${VERSION}
		COMPATIBILITY AnyNewerVersion)
configure_package_config_file(
		${PROJECT_SOURCE_DIR}/freetype-config.cmake.in
		${project_config}
		INSTALL_DESTINATION ${FREETYPE_CMAKE_DIR})
export(TARGETS freetype NAMESPACE freetype::
		FILE ${PROJECT_BINARY_DIR}/${targets_export_name}.cmake)
# Install the header file and export the target
install(TARGETS freetype EXPORT ${targets_export_name} DESTINATION lib)
install(FILES ${PROJECT_SOURCE_DIR}/lib/freetype.h DESTINATION include)