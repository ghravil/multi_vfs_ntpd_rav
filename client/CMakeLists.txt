project (client)

# Toolchain for working with the NK-parser.
include (platform/nk)

# Set compiler flags.
project_header_default ("STANDARD_GNU_11:YES" "STRICT_WARNINGS:NO")

add_executable (Client "src/client.c")
add_dependencies(Client multi_vfs_ntpd_client)
target_link_libraries (Client
                       ${${PROJECT_NAME}_LIBS}
                       rump::dhcpcd_client
                       ${vfs_CLIENT_LIB})

# We do not need the default VFS entity which comes from ${vfs_CLIENT_LIB}.
set_target_properties (Client PROPERTIES ${vfs_ENTITY}_REPLACEMENT "")
set_target_properties (Client PROPERTIES LINK_FLAGS ${VULN_LDFLAGS})
