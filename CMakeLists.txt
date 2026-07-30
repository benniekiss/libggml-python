cmake_minimum_required(VERSION 3.15)
project(libggml LANGUAGES C CXX)

include(GNUInstallDirs)

add_subdirectory(ggml)

add_library(ggml::ggml ALIAS ggml)

if(APPLE)
    target_link_options(ggml INTERFACE
        "$<INSTALL_INTERFACE:LINKER:-rpath,@loader_path/../libggml/lib>"
        "$<INSTALL_INTERFACE:LINKER:-rpath,@loader_path/../../libggml/lib>"
    )
elseif(UNIX)
    target_link_options(ggml INTERFACE
        "$<INSTALL_INTERFACE:LINKER:-rpath,$ORIGIN/../libggml/lib>"
        "$<INSTALL_INTERFACE:LINKER:-rpath,$ORIGIN/../../libggml/lib>"
    )
endif()

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

set(GGML_INSTALL_TARGETS
    ggml
    ggml-base
)

if(GGML_AVAILABLE_BACKENDS)
    list(APPEND GGML_INSTALL_TARGETS ${GGML_AVAILABLE_BACKENDS})
endif()

set(GGML_EXISTING_TARGETS "")
foreach(t ${GGML_INSTALL_TARGETS})
    if(TARGET ${t})
        ggml_install_path(${t})
        list(APPEND GGML_EXISTING_TARGETS ${t})
    endif()
endforeach()

install(
    TARGETS ${GGML_EXISTING_TARGETS}
    EXPORT ggmlTargets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

include(CMakePackageConfigHelpers)

configure_package_config_file(
    cmake/ggmlConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/ggmlConfig.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ggml
)

install(
    FILES
        ${CMAKE_CURRENT_BINARY_DIR}/ggmlConfig.cmake
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ggml
)

install(
    EXPORT ggmlTargets
    FILE ggmlTargets.cmake
    NAMESPACE ggml::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ggml
)
