cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

# ---- Cross Compiler ----

set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR aarch64)
set(CMAKE_C_COMPILER "aarch64-linux-gnu-gcc")
set(CMAKE_CXX_COMPILER "aarch64-linux-gnu-g++")

# ---- Project ----

project(gf LANGUAGES C)

# ---- Create executable ----
set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_C_FLAGS_DEBUG "$ENV{CMAKE_CXX_FLAGS} -O0 -Wall -g -Wno-pointer-sign")


# ---- CONFIG_* ----
add_definitions(-D__KERNEL__="1")


# add your source files here

file(GLOB_RECURSE sources CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/../libkernmgr/src/*.c"
                                            "${CMAKE_CURRENT_SOURCE_DIR}/../libdrvmgr/src/*.c"
                                            "${CMAKE_CURRENT_SOURCE_DIR}/../libbase/src/*.c")

file(GLOB_RECURSE headers CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/../libbase/include/*.h")
file(GLOB_RECURSE headers CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/../libbase/src/*.h")
file(GLOB_RECURSE headers CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/../libdrvmgr/include/*.h")
file(GLOB_RECURSE headers CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/../libdrvmgr/src/*.h")
file(GLOB_RECURSE headers CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/../libkernmgr/include/*.h")
file(GLOB_RECURSE headers CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/../libkernmgr/src/*.h")

add_library(gf_lib ${headers} ${sources})
target_include_directories(
  gf_lib PUBLIC $<      $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/../libbase/include>
                        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/../libbase/include/linux>
                        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/../libbase/arch/arm64/include>
                        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/../libdrvmgr/include>
                        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/../libdrvmgr/include/linux>
                        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/../libdrvmgr/arch/arm64/include>
                        $<INSTALL_INTERFACE:include/gf_lib-${PROJECT_VERSION}>
)

add_executable(gf_bin ${sources})
# link to your dependencies' targets here
target_link_libraries(gf_bin gf_lib)
# setup your target's properties
set_target_properties(gf_bin PROPERTIES C_STANDARD 11)
