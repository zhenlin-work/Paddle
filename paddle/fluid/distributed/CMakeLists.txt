if(NOT WITH_PSCORE)
    add_subdirectory(fleet_executor)
    return()
endif()

proto_library(ps_framework_proto SRCS ps.proto)

set(DISTRIBUTE_COMPILE_FLAGS "-Wno-error=unused-value -Wno-non-virtual-dtor -Wno-error=non-virtual-dtor -Wno-error=delete-non-virtual-dtor -Wno-error=sign-compare -Wno-error=unused-variable -Wno-error=return-type -Wno-error=unused-but-set-variable -Wno-error=type-limits -Wno-error=unknown-pragmas -Wno-error=parentheses -Wno-error=unused-result")

if (CMAKE_CXX_COMPILER_VERSION VERSION_GREATER 7.0)
    set(DISTRIBUTE_COMPILE_FLAGS
            "${DISTRIBUTE_COMPILE_FLAGS} -faligned-new")
endif()

add_subdirectory(common)
add_subdirectory(service)
add_subdirectory(table)
add_subdirectory(test)
add_subdirectory(index_dataset)
add_subdirectory(fleet_executor)

get_property(RPC_DEPS GLOBAL PROPERTY RPC_DEPS)

set_source_files_properties(fleet.cc PROPERTIES COMPILE_FLAGS ${DISTRIBUTE_COMPILE_FLAGS})
cc_library(fleet
        SRCS fleet.cc
        DEPS framework_proto ps_framework_proto ps_service variable_helper scope op_registry fs shell ${RPC_DEPS})

target_link_libraries(fleet z)
