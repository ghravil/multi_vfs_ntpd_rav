project(multi_vfs_ntpd_example)

cmake_minimum_required (VERSION 3.12)

# Initialize CMake library for KasperskyOS SDK.
include (platform)
initialize_platform (FORCE_STATIC)

# Add functions for installing artefacts.
include (platform/install)

# Platform definitions
include (platform/options)

# Toolchain for working with the NK-parser.
include (platform/nk)

nk_build_edl_files (multi_vfs_ntpd_client
                    NK_MODULE "multi_vfs_ntpd"
                    EDL "resources/edl/Client.edl")

# Add a package for working with the virtual file system.
find_package (vfs REQUIRED)
include_directories (${vfs_INCLUDE})

# Add a package with the dhcpcd program implementation.
find_package (rump REQUIRED COMPONENTS DHCPCD_ENTITY)
include_directories (${rump_INCLUDE})

# Add a package with the VFS program implementations.
find_package (precompiled_vfs REQUIRED)
include_directories (${precompiled_vfs_INCLUDE})

# Choose the storage devices for the configuration files.
# If none of the options is selected, the ramfs device is used.
option (ROOTFS_SDCARD "Use SD card to mount root filesystem" OFF)
message (STATUS "Use SD card to mount root filesystem - ${ROOTFS_SDCARD}")

# Attach the directory with the generated config header files.
include_directories(${CMAKE_CURRENT_BINARY_DIR})

# Recommended compiler flags against vulnerabilities.
set(VULN_LDFLAGS "\
    -Wl,-z,noexecstack \
    -Wl,-z,separate-code \
    -Wl,-z,now")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} \
    -Wall -Wextra -Wconversion \
    -fPIE -pie -D_FORTIFY_SOURCE=2 -O2 \
    -fstack-protector-strong -fstack-clash-protection \
    -mbranch-protection=standard \
    -Wsign-conversion -Wformat=2 -Wformat-security -Werror=format-security \
    -fsanitize=undefined -fsanitize-undefined-trap-on-error")

add_subdirectory (client)

add_subdirectory (einit)
