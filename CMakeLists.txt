cmake_minimum_required(VERSION 3.13)

# ---- Set target board ----
set(PICO_BOARD pico2_w)
set(PICO_PLATFORM rp2350)

# ---- Import Pico SDK ----
include(pico_sdk_import.cmake)

# ---- Project ----
project(dht11_freertos C CXX ASM)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# ---- Init SDK ----
pico_sdk_init()

add_subdirectory(dht)

# ---- FreeRTOS ----
set(FREERTOS_KERNEL_PATH ${CMAKE_CURRENT_LIST_DIR}/FreeRTOS-Kernel)
include(FreeRTOS_Kernel_import.cmake)

# ---- Executable ----
add_executable(${PROJECT_NAME}
    src/main.c
)

# ---- Include dirs ----
target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_LIST_DIR}/..
    ${CMAKE_CURRENT_LIST_DIR}/.
    src/include
    ${CMAKE_CURRENT_LIST_DIR}/dht
)

# ---- Link Libraries ----
target_link_libraries(${PROJECT_NAME} PRIVATE
    dht
    pico_async_context_freertos
    FreeRTOS-Kernel-Heap4
    pico_stdlib
)

if (PICO_CYW43_SUPPORTED)
    target_link_libraries(${PROJECT_NAME} PRIVATE pico_cyw43_arch_none)
endif()

# ---- Enable I/O ----
pico_enable_stdio_usb(${PROJECT_NAME} 1)
pico_enable_stdio_uart(${PROJECT_NAME} 0)

# ---- Create UF2, bin, etc. ----
pico_add_extra_outputs(${PROJECT_NAME})

# Para activar warnings de compilación
target_compile_options(${PROJECT_NAME} PRIVATE -Wall)
