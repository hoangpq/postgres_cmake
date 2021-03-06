option(PGXS "Separate build" OFF)
if(PGXS)
	cmake_minimum_required(VERSION 2.8)
	find_package(PostgreSQL)
	include(${PostgreSQL_LIBRARY_DIRS}/cmake/PGXS.cmake)
else()
	include_directories("${PROJECT_SOURCE_DIR}/src/include")
endif()

set(extension_name test_shm_mq)

add_library(${extension_name} ${PLUGIN_TYPE}
	test.c
	setup.c
	worker.c
)
target_link_libraries(${extension_name} ${modules_libs})
set_target_properties(${extension_name} PROPERTIES PREFIX "")
if (MSVC)
	gen_def(${extension_name})
endif()
if(NOT PGXS)
	add_dependencies(${extension_name} postgres)
	CMAKE_SET_TARGET_FOLDER(${extension_name} modules)
endif()
install(TARGETS ${extension_name}
		RUNTIME DESTINATION ${PGBINDIR}
		LIBRARY DESTINATION ${LIBDIR})
install(FILES
	${extension_name}.control
	${extension_name}--1.0.sql
	DESTINATION ${PGSHAREDIR}/extension)

MODULES_REGRESS_CHECK(${extension_name} "" "${extension_name}")
