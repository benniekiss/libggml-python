cmake_minimum_required(VERSION 3.15)
project(libggml LANGUAGES C CXX)

add_subdirectory(ggml)

function(ggml_install_path target)
    if(NOT TARGET ${target})
        return()
    endif()

    if(APPLE)
        set_target_properties(${target} PROPERTIES
            INSTALL_RPATH "@loader_path"
            BUILD_WITH_INSTALL_RPATH TRUE
        )
    else()
        set_target_properties(${target} PROPERTIES
            INSTALL_RPATH "$ORIGIN"
            BUILD_WITH_INSTALL_RPATH TRUE
        )
    endif()
endfunction()

ggml_install_path(ggml)
ggml_install_path(ggml-base)
ggml_install_path(ggml-amx)
ggml_install_path(ggml-blas)
ggml_install_path(ggml-can)
ggml_install_path(ggml-cpu)
ggml_install_path(ggml-cuda)
ggml_install_path(ggml-hip)
ggml_install_path(ggml-kompute)
ggml_install_path(ggml-metal)
ggml_install_path(ggml-musa)
ggml_install_path(ggml-rpc)
ggml_install_path(ggml-sycl)
ggml_install_path(ggml-vulkan)
