# define the minimum cmake version (as required by cmake)
cmake_minimum_required(VERSION 2.8)

# set the avr toochain
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/cmake/generic-gcc-avr.cmake)

# define the project name
project(hyperion-usbasp ASM C CXX)

# tools to beused for programming the AVR
set(AVR_UPLOADTOOL avrdude)
set(AVR_PROGRAMMER usbasp)
set(AVR_UPLOADTOOL_PORT usb)

# AVR and fuses needs to be set
set(AVR_MCU atmega8)
set(AVR_H_FUSE 0xc9)
set(AVR_L_FUSE 0xef)

# AVR and fuses needs to be set
# untested but likely works with atmega88 based boards
#set(AVR_MCU atmega88)
#set(AVR_H_FUSE 0xdf)
#set(AVR_L_FUSE 0xff)

# status messages
message(STATUS "Current MCU:    ${AVR_MCU}")
message(STATUS "Current H_FUSE: ${AVR_H_FUSE}")
message(STATUS "Current L_FUSE: ${AVR_L_FUSE}")

# set build type
if(NOT CMAKE_BUILD_TYPE)
   set(CMAKE_BUILD_TYPE Release)
endif(NOT CMAKE_BUILD_TYPE)

# needs to be defined for AVR toolchain
set(MCU_SPEED "12000000UL")

# some cmake cross-compile necessities
if(CMAKE_HOST_SYSTEM MATCHES "Windows")
    if(DEFINED ENV{AVR_FIND_ROOT_PATH})
        set(CMAKE_FIND_ROOT_PATH $ENV{AVR_FIND_ROOT_PATH})
    else(DEFINED ENV{AVR_FIND_ROOT_PATH})
        message(FATAL_ERROR "Please set AVR_FIND_ROOT_PATH in your environment.")
    endif(DEFINED ENV{AVR_FIND_ROOT_PATH})
else(CMAKE_HOST_SYSTEM MATCHES "Windows")
    set(CMAKE_FIND_ROOT_PATH "/usr/lib/avr")
endif(CMAKE_HOST_SYSTEM MATCHES "Windows")
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
# not added automatically, since CMAKE_SYSTEM_NAME is "avr"
set(CMAKE_SYSTEM_INCLUDE_PATH "${CMAKE_FIND_ROOT_PATH}/include")
set(CMAKE_SYSTEM_LIBRARY_PATH "${CMAKE_FIND_ROOT_PATH}/lib")

# status messages for generating
message(STATUS "Set CMAKE_SYSTEM_INCLUDE_PATH to ${CMAKE_SYSTEM_INCLUDE_PATH}")
message(STATUS "Set CMAKE_SYSTEM_LIBRARY_PATH to ${CMAKE_SYSTEM_LIBRARY_PATH}")

# set compiler options for build types
if(CMAKE_BUILD_TYPE MATCHES Release)
   set(CMAKE_C_FLAGS_RELEASE "-Os")
   set(CMAKE_CXX_FLAGS_RELEASE "-Os")
endif(CMAKE_BUILD_TYPE MATCHES Release)

if(CMAKE_BUILD_TYPE MATCHES RelWithDebInfo)
   set(CMAKE_C_FLAGS_RELWITHDEBINFO "-Os -save-temps -g -gdwarf-3 -gstrict-dwarf")
   set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "-Os -save-temps -g -gdwarf-3 -gstrict-dwarf")
endif(CMAKE_BUILD_TYPE MATCHES RelWithDebInfo)

if(CMAKE_BUILD_TYPE MATCHES Debug)
   set(CMAKE_C_FLAGS_DEBUG "-O0 -save-temps -g -gdwarf-3 -gstrict-dwarf")
   set(CMAKE_CXX_FLAGS_DEBUG "-O0 -save-temps -g -gdwarf-3 -gstrict-dwarf")
endif(CMAKE_BUILD_TYPE MATCHES Debug)

# compiler options for all build types
add_definitions("-DF_CPU=${MCU_SPEED}")
add_definitions("-fpack-struct")
add_definitions("-fshort-enums")
add_definitions("-Wall")
add_definitions("-Werror")
add_definitions("-pedantic")
add_definitions("-pedantic-errors")
add_definitions("-funsigned-char")
add_definitions("-funsigned-bitfields")
add_definitions("-ffunction-sections")
add_definitions("-c")
set(CMAKE_C_FLAGS ${CMAKE_C_FLAGS} "-std=gnu99")

# Additional compiler flags which are useful according to the usbdrv documentation
add_definitions("-fno-move-loop-invariants")
add_definitions("-fno-tree-scev-cprop")
add_definitions("-fno-inline-small-functions")

# Set assembler options
SET(ASM_OPTIONS "-x assembler-with-cpp")
SET(CMAKE_ASM_FLAGS "${CFLAGS} ${ASM_OPTIONS}" )

# include search paths
include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/usbdrv
    ${CMAKE_CURRENT_SOURCE_DIR}/leddrv
    ${CMAKE_CURRENT_SOURCE_DIR}/light_ws2812
)

# building library and application in their subdirectories
add_subdirectory(usbdrv)
add_subdirectory(light_ws2812)
add_subdirectory(leddrv)
#add_subdirectory(usbasp)
add_subdirectory(hyperion)
