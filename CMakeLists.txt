set(LIB_NAME jpeg)

project(${LIB_NAME})

function(configure_target_outdir target)
    set_target_properties(${target} PROPERTIES
            ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
            LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
            RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
            FOLDER "External"
            )
endfunction(configure_lib_outdir)

if(WINDOWS)
    add_library(${LIB_NAME} SHARED IMPORTED GLOBAL)
else()
    add_library(${LIB_NAME} STATIC IMPORTED GLOBAL)
endif()

set(WITH_SIMD ${CMAKE_ASM_NASM_COMPILER_LOADED} CACHE BOOL "Build TurboJPEG with simd speed up" FORCE)
set(WITH_JPEG8 TRUE CACHE BOOL "Build libjpeg-turbo with JEPG8 API" FORCE)
set(ENABLE_SHARED FALSE CACHE BOOL "Build shared libraries" FORCE)
set(WITH_TURBOJPEG FALSE CACHE BOOL "Include the TurboJPEG API library and associated test programs" FORCE)
set(JTURBO_ENABLE_INSTALL FALSE CACHE BOOL "Enable libjpeg-turbo install" FORCE)
set(JTURBO_BUILD_TESTS FALSE CACHE BOOL "Build TurboJPEG tests and examples" FORCE)
set(JTURBO_BUILD_BINARIES FALSE CACHE BOOL "Build TurboJPEG binaries" FORCE)
set(WITH_CRT_DLL TRUE CACHE BOOL "Build TurboJPEG with crt dll" FORCE)
add_subdirectory(jpeg)
configure_target_outdir(jpeg)
configure_target_outdir(simd)
if(TARGET simd-objs)
    configure_target_outdir(simd-objs)
endif()
if(TARGET jsimdcfg)
    configure_target_outdir(jsimdcfg)
endif()