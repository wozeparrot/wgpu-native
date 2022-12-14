cmake_minimum_required(VERSION 3.11b)

project(capture)

set(TARGET_NAME capture)

add_executable(${TARGET_NAME} main.c ../framework.c)

if(MSVC)
    add_definitions(-DWGPU_TARGET=WGPU_TARGET_WINDOWS)
    target_compile_options(${TARGET_NAME} PRIVATE /W4)
    set(OS_LIBRARIES "userenv" "ws2_32" "Dwmapi" "dbghelp" "d3dcompiler" "D3D12" "D3D11" "DXGI" "setupapi" "Bcrypt")
elseif(APPLE)
    add_definitions(-DWGPU_TARGET=WGPU_TARGET_MACOS)
    set(OS_LIBRARIES "-framework Cocoa" "-framework CoreVideo" "-framework IOKit" "-framework QuartzCore")
    target_compile_options(${TARGET_NAME} PRIVATE -x objective-c)
else(MSVC)
    add_definitions(-DWGPU_TARGET=WGPU_TARGET_LINUX)
    target_compile_options(${TARGET_NAME} PRIVATE -Wall -Wextra -pedantic)
endif(MSVC)

find_library(WGPU_LIBRARY wgpu_native
    HINTS "${CMAKE_CURRENT_SOURCE_DIR}/../../target/debug"
)

find_library(HELPER_LIBRARY helper
    HINTS "${CMAKE_CURRENT_SOURCE_DIR}/../../target/debug"
)

target_include_directories(${TARGET_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../../ffi)
target_include_directories(${TARGET_NAME} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../)

target_link_libraries(${TARGET_NAME} ${WGPU_LIBRARY} ${HELPER_LIBRARY} ${OS_LIBRARIES})
