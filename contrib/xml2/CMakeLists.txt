include_directories(
	"${PROJECT_SOURCE_DIR}/src/include"
	${LIBXML2_INCLUDE_DIR}
	${LIBXSLT_INCLUDE_DIR}
)

set(extension_name xml2)
set(extension_lib_name pgxml)

add_library(${extension_lib_name} SHARED xpath.c xslt_proc.c)
target_link_libraries(${extension_lib_name}
	${contrib_libs}
	${LIBXML2_LIBRARIES}
	${LIBXSLT_LIBRARIES}
)
set_target_properties(${extension_lib_name} PROPERTIES PREFIX "")
add_dependencies(${extension_lib_name} postgres)
install(TARGETS ${extension_lib_name}
		RUNTIME DESTINATION ${PGBINDIR}
		LIBRARY DESTINATION ${LIBDIR})
install(FILES
	${extension_name}.control
	${extension_name}--1.0.sql
	${extension_name}--unpackaged--1.0.sql
	DESTINATION ${PGSHAREDIR}/extension)

set(regress_files xml2)
CONTRIB_REGRESS_CHECK(${extension_name} "" "${regress_files}")