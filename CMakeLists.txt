cmake_minimum_required(VERSION 3.0.2)

project(net VERSION 1.3.14 DESCRIPTION "Reliable UDP networking library"  LANGUAGES C)

# The "configure" step.
include(CheckFunctionExists)
include(CheckStructHasMember)
include(CheckTypeSize)
check_function_exists("fcntl" HAS_FCNTL)
check_function_exists("poll" HAS_POLL)
check_function_exists("getaddrinfo" HAS_GETADDRINFO)
check_function_exists("getnameinfo" HAS_GETNAMEINFO)
check_function_exists("gethostbyname_r" HAS_GETHOSTBYNAME_R)
check_function_exists("gethostbyaddr_r" HAS_GETHOSTBYADDR_R)
check_function_exists("inet_pton" HAS_INET_PTON)
check_function_exists("inet_ntop" HAS_INET_NTOP)
check_struct_has_member("struct msghdr" "msg_flags" "sys/types.h;sys/socket.h" HAS_MSGHDR_FLAGS)
set(CMAKE_EXTRA_INCLUDE_FILES "sys/types.h" "sys/socket.h")
check_type_size("socklen_t" HAS_SOCKLEN_T BUILTIN_TYPES_ONLY)
unset(CMAKE_EXTRA_INCLUDE_FILES)

## options:
option(BUILD_SHARED "Builds the lib Shared" OFF)

## create target:

if(BUILD_SHARED)
    add_library(enet SHARED)
    target_compile_definitions(enet PUBLIC ENET_DLL)
else(BUILD_SHARED)
    add_library(enet STATIC)
endif(BUILD_SHARED)
add_library(enet::enet ALIAS enet)

if(MSVC)
target_compile_options(enet PRIVATE -W3)
else()
target_compile_options(enet PRIVATE -Wno-error)
endif()

if(HAS_FCNTL)
    target_compile_options(enet PRIVATE -DHAS_FCNTL=1)
endif()
if(HAS_POLL)
    target_compile_options(enet PRIVATE -DHAS_POLL=1)
endif()
if(HAS_GETNAMEINFO)
    target_compile_options(enet PRIVATE -DHAS_GETNAMEINFO=1)
endif()
if(HAS_GETADDRINFO)
    target_compile_options(enet PRIVATE -DHAS_GETADDRINFO=1)
endif()
if(HAS_GETHOSTBYNAME_R)
    target_compile_options(enet PRIVATE -DHAS_GETHOSTBYNAME_R=1)
endif()
if(HAS_GETHOSTBYADDR_R)
    target_compile_options(enet PRIVATE -DHAS_GETHOSTBYADDR_R=1)
endif()
if(HAS_INET_PTON)
    target_compile_options(enet PRIVATE -DHAS_INET_PTON=1)
endif()
if(HAS_INET_NTOP)
    target_compile_options(enet PRIVATE -DHAS_INET_NTOP=1)
endif()
if(HAS_MSGHDR_FLAGS)
    target_compile_options(enet PRIVATE -DHAS_MSGHDR_FLAGS=1)
endif()
if(HAS_SOCKLEN_T)
    target_compile_options(enet PRIVATE -DHAS_SOCKLEN_T=1)
endif()

target_sources(enet PRIVATE
    callbacks.c
    compress.c
    host.c
    list.c
    packet.c
    peer.c
    protocol.c
    unix.c
    win32.c)


target_include_directories(enet PUBLIC
PRIVATE
$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
PUBLIC
$<INSTALL_INTERFACE:${PROJECT_SOURCE_DIR}/include>
)
target_sources(enet PRIVATE
    callbacks.c
    compress.c
    host.c
    list.c
    packet.c
    peer.c
    protocol.c
    unix.c
    win32.c)

    set_target_properties( enet
    PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY libs
    LIBRARY_OUTPUT_DIRECTORY libs
    RUNTIME_OUTPUT_DIRECTORY bin
)

if (MSVC)
    target_link_libraries(enet winmm ws2_32)
endif()

install(FILES ${INCLUDE_FILES}
              DESTINATION include/enet)

install(TARGETS enet
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)
