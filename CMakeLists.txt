cmake_minimum_required(VERSION 3.1.0)

project(psplash LANGUAGES C)

option(HAVE_SYSTEMD "Build with systemd support" OFF)
option(DISABLE_STARTUP_MSG "Disable text banner output on startup" OFF)
option(DISABLE_PROGRESS_BAR "Disable progress bar" OFF)
option(IMG_FULLSCREEN "Enable the logo image in fullscreen mode" OFF)

set(PSPLASH_SRCS psplash-console.c
                 psplash-fb.c
                 psplash.c)

add_executable(psplash ${PSPLASH_SRCS})
add_executable(psplash-write psplash-write.c)

if (HAVE_SYSTEMD)
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(SYSTEMD REQUIRED libsystemd>=221)
    add_executable(psplash-systemd psplash-systemd.c)
    target_link_libraries(psplash-systemd systemd)
endif()

if (NOT FONT_NAME)
    set(FONT_NAME radeon)
endif()

add_definitions(-DFONT_HEADER=\"${FONT_NAME}-font.h\")
add_definitions(-DFONT_DEF=${FONT_NAME}_font)

if(DISABLE_STARTUP_MSG)
    add_definitions(-DPSPLASH_DISABLE_STARTUP_MSG)
endif()

if(DISABLE_PROGRESS_BAR)
    add_definitions(-DPSPLASH_DISABLE_PROGRESS_BAR)
endif()

if(IMG_FULLSCREEN)
    add_definitions(-DPSPLASH_IMG_FULLSCREEN=1)
endif()
