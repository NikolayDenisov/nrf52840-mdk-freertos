cmake_minimum_required(VERSION 3.16)

set(PROJECT_NAME nrf52840-mdk-freertos)
set(CMAKE_C_STANDARD 99)
SET(CMAKE_C_COMPILER /usr/bin/arm-none-eabi-gcc)
SET(CMAKE_ASM_COMPILER /usr/bin/arm-none-eabi-gcc)
# Indicate the toolchain path in your system
set(ARM_TOOLCHAIN_PATH /usr/bin/)
set(ARM_TOOLCHAIN_PREFIX arm-none-eabi-gcc)

SET(SDK_ROOT /home/nick/nrf52840-mdk-freertos/nRF5_SDK_17)
SET(PROJ_DIR /home/nick/nrf52840-mdk-freertos)

set(CMAKE_TRY_COMPILE_TARGET_TYPE "STATIC_LIBRARY")
SET(PROJ_DIR .)
set(NRF5_LINKER_SCRIPT "/home/nick/nrf52840-mdk-freertos/blinky_FreeRTOS_gcc_nrf52.ld")
SET(CPU_FLAGS "-mcpu=cortex-m4 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16")
set(COMMON_FLAGS "-MP -MD -mthumb -mabi=aapcs -Wall -Werror -O3 -g3 -ffunction-sections -fdata-sections -fno-strict-aliasing -fno-builtin --short-enums ${CPU_FLAGS}")
set(BOARD_VAR "-DBOARD_CUSTOM -DNRF52840_MDK -DBSP_DEFINES_ONLY -DCONFIG_GPIO_AS_PINRESET -DNRF52840_XXAA")
include_directories(".")
add_definitions(-DBOARD_CUSTOM -DNRF52840_MDK -DBSP_DEFINES_ONLY -DCONFIG_GPIO_AS_PINRESET -DFLOAT_ABI_HARD -DNRF52840_XXAA)

include_directories(${SDK_ROOT}/components)
include_directories(${SDK_ROOT}/modules/nrfx/mdk)
include_directories(${PROJ_DIR}/config)
include_directories(${SDK_ROOT}/components/libraries/timer)
include_directories(${SDK_ROOT}/components/libraries/strerror)
include_directories(${SDK_ROOT}/components/toolchain/cmsis/include)
include_directories(${SDK_ROOT}/external/freertos/source/include)
include_directories(${SDK_ROOT}/external/freertos/config)
include_directories(${SDK_ROOT}/components/libraries/util)
include_directories(${SDK_ROOT}/components/libraries/balloc)
include_directories(${SDK_ROOT}/components/libraries/ringbuf)
include_directories(${SDK_ROOT}/modules/nrfx/hal)
include_directories(${SDK_ROOT}/components/libraries/bsp)
include_directories(${SDK_ROOT}/components/libraries/log)
include_directories(${SDK_ROOT}/components/libraries/button)
include_directories(${SDK_ROOT}/modules/nrfx)
include_directories(${SDK_ROOT}/components/libraries/experimental_section_vars)
include_directories(${SDK_ROOT}/integration/nrfx/legacy)
include_directories(${SDK_ROOT}/external/freertos/portable/CMSIS/nrf52)
include_directories(${SDK_ROOT}/integration/nrfx)
include_directories(${SDK_ROOT}/components/drivers_nrf/nrf_soc_nosd)
include_directories(${SDK_ROOT}/components/libraries/atomic)
include_directories(${SDK_ROOT}/components/boards)
include_directories(${SDK_ROOT}/components/libraries/memobj)
include_directories(${SDK_ROOT}/external/freertos/portable/GCC/nrf52)
include_directories(${SDK_ROOT}/modules/nrfx/drivers/include)
include_directories(${SDK_ROOT}/external/fprintf)
include_directories(${SDK_ROOT}/components/libraries/log/src)

set(SRC_FILES ${SDK_ROOT}/modules/nrfx/mdk/gcc_startup_nrf52840.S main.c ${SDK_ROOT}/modules/nrfx/mdk/system_nrf52840.c)
list(APPEND CFLAGS "-undef" "-D__GNUC__")
list(FILTER CFLAGS EXCLUDE REGEX mcpu)
string(REPLACE ";" " " CFLAGS "${CFLAGS}")
set(CMAKE_C_FLAGS ${CFLAGS})

add_executable(${PROJECT_NAME} ${SRC_FILES})



add_custom_target( erase
        COMMAND pyocd erase -t nrf52840 --chip
)

add_custom_target( flash
        COMMAND pyocd flash -t nrf52840 $(OUTPUT_DIRECTORY)/nrf52840_xxaa.hex
)

project(${PROJECT_NAME})

