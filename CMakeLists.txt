cmake_minimum_required(VERSION 3.15)
project(libggml LANGUAGES C CXX)

add_subdirectory(ggml)

target_link_libraries(ggml
    PUBLIC
        ggml-base
        ggml-cpu
)

add_library(ggml::ggml ALIAS ggml)

function(ggml_install_path target)
    if(NOT TARGET ${target})
        return()
    endif()

    if(APPLE)
        set_target_properties(${target} PROPERTIES
            BUILD_WITH_INSTALL_RPATH TRUE
            INSTALL_RPATH "@loader_path"
        )
    else()
        set_target_properties(${target} PROPERTIES
            BUILD_WITH_INSTALL_RPATH TRUE
            INSTALL_RPATH "$ORIGIN"
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

set(GGML_INSTALL_TARGETS
    ggml
    ggml-base
    ggml-amx
    ggml-blas
    ggml-can
    ggml-cpu
    ggml-cuda
    ggml-hip
    ggml-kompute
    ggml-metal
    ggml-musa
    ggml-rpc
    ggml-sycl
    ggml-vulkan
)

set(GGML_EXISTING_TARGETS "")
foreach(t ${GGML_INSTALL_TARGETS})
    if(TARGET ${t})
        list(APPEND GGML_EXISTING_TARGETS ${t})
    endif()
endforeach()

install(
    TARGETS ${GGML_EXISTING_TARGETS}
    EXPORT ggmlTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION lib
)

include(CMakePackageConfigHelpers)

configure_package_config_file(
    cmake/ggmlConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/ggmlConfig.cmake
    INSTALL_DESTINATION lib/cmake/ggml
)

install(
    FILES
        ${CMAKE_CURRENT_BINARY_DIR}/ggmlConfig.cmake
    DESTINATION lib/cmake/ggml
)

install(
    EXPORT ggmlTargets
    FILE ggmlTargets.cmake
    NAMESPACE ggml::
    DESTINATION lib/cmake/ggml
)
