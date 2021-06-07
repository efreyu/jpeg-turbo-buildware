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

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    set(WINDOWS TRUE)
    set(SYSTEM_STRING "Windows Desktop")
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Android")
    set(SYSTEM_STRING "Android")
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    if(ANDROID)
        set(SYSTEM_STRING "Android")
    else()
        set(LINUX TRUE)
        set(SYSTEM_STRING "Linux")
    endif()
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    if(IOS)
        set(APPLE TRUE)
        set(SYSTEM_STRING "IOS")
    else()
        set(APPLE TRUE)
        set(MACOSX TRUE)
        set(SYSTEM_STRING "Mac OSX")
    endif()
endif()

# set platform specific path
set(_path_prefix ${CMAKE_CURRENT_SOURCE_DIR}/prebuilt/)
if(IOS)
    set(platform_name ios)
    set(platform_spec_path ios)
elseif(ANDROID)
    set(platform_name android)
    set(platform_spec_path android/${ANDROID_ABI})
elseif(WINDOWS)
    set(platform_name win32)
    set(platform_spec_path win32)
elseif(MACOSX)
    set(platform_name mac)
    set(platform_spec_path mac)
elseif(LINUX)
    set(platform_name linux)
    set(platform_spec_path linux/64-bit)
endif()

set(platform_spec_path "${_path_prefix}${platform_spec_path}")

macro(set_sub_target sub_target_name)

    if(WINDOWS)
        add_library(${sub_target_name} SHARED IMPORTED GLOBAL)
    else()
        add_library(${sub_target_name} STATIC IMPORTED GLOBAL)
    endif()

    set_target_properties(${sub_target_name} PROPERTIES
            INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/include"
            )

    set(imp_lib_name "")
    if(WINDOWS)
        set(imp_lib_name "${platform_spec_path}/lib${sub_target_name}.lib")
        set_target_properties(${sub_target_name} PROPERTIES
                IMPORTED_LOCATION "${platform_spec_path}/lib${sub_target_name}-1_1.dll"
                IMPORTED_IMPLIB "${platform_spec_path}/lib${sub_target_name}.lib"
                )
    else()
        set(imp_lib_name "${platform_spec_path}/lib${sub_target_name}.a")
        set_target_properties(${sub_target_name} PROPERTIES
                IMPORTED_LOCATION "${platform_spec_path}/lib${sub_target_name}.a"
                )
    endif()
endmacro()

set_sub_target(${LIB_NAME} jpeg)
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
#target_link_libraries(external jpeg)
#target_include_directories(external INTERFACE
#        "${CMAKE_CURRENT_LIST_DIR}/jpeg"
#        "${JPEG_CONFIG_OUT_DIR}")