###############################################################################
# Copyright (c) Lawrence Livermore National Security, LLC and other Ascent
# Project developers. See top-level LICENSE AND COPYRIGHT files for dates and
# other details. No copyright assignment is required to contribute to Ascent.
###############################################################################


###############################################################################
#
# file: src/ascent/CMakeLists.txt
#
###############################################################################

#if(ENABLE_CUDA AND NOT "${BUILD_SHARED_LIBS}" STREQUAL "OFF")
#  message(FATAL_ERROR "Ascent can only be built statically when cuda is enabled")
#endif()

convert_to_native_escaped_file_path(${CMAKE_INSTALL_PREFIX}
                                    ASCENT_INSTALL_PREFIX)

set(ASCENT_INSTALL_PREFIX ${ASCENT_INSTALL_PREFIX} CACHE STRING "" FORCE )
# ASCENT_INSTALL_PREFIX is basically used as an output var so
# we mark it as advanced b/c otherwise folks may see it in ccmake or
# the cmake gui and think they should be setting it
mark_as_advanced(ASCENT_INSTALL_PREFIX)


if(FORTRAN_FOUND)
    set(ASCENT_FORTRAN_COMPILER ${CMAKE_Fortran_COMPILER})
endif()

set(ASCENT_SOURCE_WEB_CLIENT_ROOT ${CMAKE_CURRENT_SOURCE_DIR}/web_clients)

set(ASCENT_VTKM_ENABLED ${VTKM_FOUND})
set(ASCENT_VTKH_ENABLED ${VTKM_FOUND})

set(ASCENT_HDF5_ENABLED ${CONDUIT_RELAY_HDF5_ENABLED})
set(ASCENT_MFEM_ENABLED ${MFEM_FOUND})
set(ASCENT_DRAY_ENABLED ${DRAY_FOUND})
set(ASCENT_PYTHON_ENABLED ${PYTHON_FOUND})
set(ASCENT_FIDES_ENABLED ${FIDES_FOUND})
set(ASCENT_GENTEN_ENABLED ${GENTEN_FOUND})
set(ASCENT_KOKKOS_ENABLED ${KOKKOS_FOUND})


if(OCCA_FOUND AND UMPIRE_FOUND)
  set(ASCENT_JIT_ENABLED TRUE)
else()
  set(ASCENT_JIT_ENABLED FALSE)
endif()

set(ASCENT_WEBSERVER_ENABLED ${CONDUIT_RELAY_WEBSERVER_ENABLED})


if(ASCENT_VTKM_ENABLED)
    set(ASCENT_VTKM_USE_CUDA ${CUDA_FOUND})
    set(ASCENT_VTKM_USE_OPENMP ${OPENMP_FOUND})
endif()

set(ASCENT_USE_OPENMP  ${OPENMP_FOUND})

configure_file ("${CMAKE_CURRENT_SOURCE_DIR}/ascent_config.h.in"
                "${CMAKE_CURRENT_BINARY_DIR}/ascent_config.h")

set(ascent_device_sources )

set(ascent_sources
    # ascent interface
    ascent.cpp
    c/ascent_c.cpp
    # runtimes
    ascent_runtime.cpp
    runtimes/ascent_main_runtime.cpp
    runtimes/ascent_metadata.cpp
    runtimes/ascent_empty_runtime.cpp
    runtimes/ascent_expression_eval.cpp
    runtimes/ascent_transmogrifier.cpp
    runtimes/expressions/ascent_blueprint_architect.cpp
    runtimes/expressions/ascent_blueprint_topologies.cpp
    runtimes/expressions/ascent_conduit_reductions.cpp
    runtimes/expressions/ascent_expression_filters.cpp
    runtimes/expressions/ascent_expressions_ast.cpp
    runtimes/expressions/ascent_expressions_tokens.cpp
    runtimes/expressions/ascent_expressions_parser.cpp
    runtimes/expressions/ascent_derived_jit.cpp
    runtimes/expressions/ascent_jit_array.cpp
    runtimes/expressions/ascent_jit_field.cpp
    runtimes/expressions/ascent_jit_fusion.cpp
    runtimes/expressions/ascent_jit_kernel.cpp
    runtimes/expressions/ascent_jit_math.cpp
    runtimes/expressions/ascent_jit_topology.cpp
    runtimes/expressions/ascent_insertion_ordered_set.cpp
    runtimes/expressions/ascent_expression_jit_filters.cpp
    runtimes/ascent_flow_runtime.cpp
    runtimes/flow_filters/ascent_runtime_filters.cpp
    runtimes/flow_filters/ascent_runtime_param_check.cpp
    runtimes/flow_filters/ascent_runtime_relay_filters.cpp
    runtimes/flow_filters/ascent_runtime_blueprint_filters.cpp
    runtimes/flow_filters/ascent_runtime_htg_filters.cpp
    runtimes/flow_filters/ascent_runtime_trigger_filters.cpp
    runtimes/flow_filters/ascent_runtime_query_filters.cpp
    runtimes/flow_filters/ascent_runtime_utils.cpp
    # utils
    utils/ascent_actions_utils.cpp
    utils/ascent_data_logger.cpp
    utils/ascent_file_system.cpp
    utils/ascent_block_timer.cpp
    utils/ascent_logging.cpp
    utils/ascent_png_compare.cpp
    utils/ascent_png_decoder.cpp
    utils/ascent_png_encoder.cpp
    utils/ascent_mpi_utils.cpp
    utils/ascent_string_utils.cpp
    utils/ascent_web_interface.cpp
    utils/ascent_resources.cpp
    # hola
    hola/ascent_hola.cpp)

set(ascent_headers
    # ascent interface
    ${CMAKE_CURRENT_BINARY_DIR}/ascent_config.h
    ascent_exports.h
    ascent.hpp
    c/ascent.h
    # runtimes
    ascent_runtime.hpp
    runtimes/ascent_empty_runtime.hpp
    runtimes/ascent_expression_eval.hpp
    runtimes/ascent_transmogrifier.hpp
    runtimes/expressions/ascent_blueprint_architect.hpp
    runtimes/expressions/ascent_blueprint_topologies.hpp
    runtimes/expressions/ascent_conduit_reductions.hpp
    runtimes/expressions/ascent_expression_filters.hpp
    runtimes/expressions/ascent_expressions_ast.hpp
    runtimes/expressions/ascent_expressions_tokens.hpp
    runtimes/expressions/ascent_expressions_parser.hpp
    runtimes/expressions/ascent_derived_jit.hpp
    runtimes/expressions/ascent_jit_array.hpp
    runtimes/expressions/ascent_jit_field.hpp
    runtimes/expressions/ascent_jit_fusion.hpp
    runtimes/expressions/ascent_jit_kernel.hpp
    runtimes/expressions/ascent_jit_math.hpp
    runtimes/expressions/ascent_jit_topology.hpp
    runtimes/expressions/ascent_insertion_ordered_set.hpp
    runtimes/expressions/ascent_expression_jit_filters.hpp
    # flow
    runtimes/ascent_main_runtime.hpp
    runtimes/ascent_metadata.hpp
    runtimes/ascent_flow_runtime.hpp
    runtimes/flow_filters/ascent_runtime_filters.hpp
    runtimes/flow_filters/ascent_runtime_param_check.hpp
    runtimes/flow_filters/ascent_runtime_relay_filters.hpp
    runtimes/flow_filters/ascent_runtime_blueprint_filters.hpp
    runtimes/flow_filters/ascent_runtime_htg_filters.hpp
    runtimes/flow_filters/ascent_runtime_trigger_filters.hpp
    runtimes/flow_filters/ascent_runtime_query_filters.hpp
    runtimes/flow_filters/ascent_runtime_vtkh_utils.hpp
    runtimes/flow_filters/ascent_runtime_utils.hpp
    # utils
    utils/ascent_actions_utils.hpp
    utils/ascent_data_logger.hpp
    utils/ascent_logging.hpp
    utils/ascent_file_system.hpp
    utils/ascent_block_timer.hpp
    utils/ascent_png_compare.hpp
    utils/ascent_png_decoder.hpp
    utils/ascent_png_encoder.hpp
    utils/ascent_mpi_utils.hpp
    utils/ascent_string_utils.hpp
    utils/ascent_web_interface.hpp
    utils/ascent_resources.hpp
    utils/ascent_resources_cinema_web.hpp
    # hola
    hola/ascent_hola.hpp)

if(ASCENT_JIT_ENABLED)
    list(APPEND ascent_sources runtimes/expressions/ascent_array.cpp)
    list(APPEND ascent_sources runtimes/expressions/ascent_array_internals.cpp)
    list(APPEND ascent_sources runtimes/expressions/ascent_array_internals_base.cpp)
    list(APPEND ascent_sources runtimes/expressions/ascent_array_registry.cpp)

    list(APPEND ascent_headers runtimes/expressions/ascent_array.hpp)
    list(APPEND ascent_headers runtimes/expressions/ascent_array_internals.hpp)
    list(APPEND ascent_headers runtimes/expressions/ascent_array_internals_base.hpp)
    list(APPEND ascent_headers runtimes/expressions/ascent_array_registry.hpp)
    # this has device calls so we need to tell cmake to use the cuda compiler
    if(CUDA_FOUND)
      set_source_files_properties(runtimes/expressions/ascent_array.cpp  PROPERTIES LANGUAGE CUDA)
      set_source_files_properties(runtimes/expressions/ascent_array_internals.cpp  PROPERTIES LANGUAGE CUDA)
      set_source_files_properties(runtimes/expressions/ascent_derived_jit.cpp  PROPERTIES LANGUAGE CUDA)
    endif()
    list(APPEND ascent_device_sources
      runtimes/expressions/ascent_array.cpp
      runtimes/expressions/ascent_array_internals.cpp
      runtimes/expressions/ascent_derived_jit.cpp)
endif()

if(VTKM_FOUND)

    set(ascent_vtkh_dep_headers
        runtimes/ascent_data_object.hpp
        runtimes/ascent_vtkh_data_adapter.hpp
        runtimes/ascent_vtkh_collection.hpp
        runtimes/flow_filters/ascent_runtime_vtkh_filters.hpp
        runtimes/flow_filters/ascent_runtime_vtkh_utils.hpp
        runtimes/flow_filters/ascent_runtime_rendering_filters.hpp
        runtimes/flow_filters/ascent_runtime_rover_filters.hpp
        runtimes/flow_filters/ascent_runtime_conduit_to_vtkm_parsing.hpp
        runtimes/ascent_main_runtime.hpp)

    set(ascent_vtkh_dep_sources
        runtimes/ascent_data_object.cpp
        runtimes/ascent_vtkh_data_adapter.cpp
        runtimes/ascent_vtkh_collection.cpp
        runtimes/flow_filters/ascent_runtime_vtkh_filters.cpp
        runtimes/flow_filters/ascent_runtime_vtkh_utils.cpp
        runtimes/flow_filters/ascent_runtime_rendering_filters.cpp
        runtimes/flow_filters/ascent_runtime_rover_filters.cpp
        runtimes/flow_filters/ascent_runtime_conduit_to_vtkm_parsing.cpp
        runtimes/ascent_main_runtime.cpp)

    set(ascent_vtkm_device_sources
        ${ascent_vtkh_dep_sources}
        runtimes/flow_filters/ascent_runtime_blueprint_filters.cpp)
    list(APPEND ascent_device_sources ${ascent_vtkm_device_sources})

    foreach( _itm ${ascent_vtkh_dep_headers} )
        list(APPEND ascent_headers ${_itm})
    endforeach()

    foreach( _itm ${ascent_vtkh_dep_sources} )
        list(APPEND ascent_sources ${_itm})
    endforeach()
else()
    list(APPEND ascent_headers runtimes/ascent_data_object.hpp)
    list(APPEND ascent_sources runtimes/ascent_data_object.cpp)
endif()

if(MFEM_FOUND)
    # data adapter
    list(APPEND ascent_headers runtimes/ascent_mfem_data_adapter.hpp)
    list(APPEND ascent_sources runtimes/ascent_mfem_data_adapter.cpp)
endif()

if(DRAY_FOUND)
  list(APPEND ascent_headers runtimes/flow_filters/ascent_runtime_dray_filters.hpp)
  list(APPEND ascent_sources runtimes/flow_filters/ascent_runtime_dray_filters.cpp)
endif()

if(GENTEN_FOUND)
    list(APPEND ascent_headers runtimes/flow_filters/ascent_runtime_genten_filters.hpp)
    list(APPEND ascent_sources runtimes/flow_filters/ascent_runtime_genten_filters.cpp)
    list(APPEND ascent_device_sources runtimes/flow_filters/ascent_runtime_genten_filters.cpp)
endif()

##############################
# optional features
#############################

if(FORTRAN_FOUND)
    add_library(ascent_fortran OBJECT fortran/ascent_fortran.f90)
    list(APPEND ascent_sources $<TARGET_OBJECTS:ascent_fortran>)

    #####################################################
    # Setup install to copy the fortran modules
    #####################################################
    install(FILES
            ${CMAKE_Fortran_MODULE_DIRECTORY}/ascent.mod
            DESTINATION include/ascent)
endif()



################################
# Add python wrappers if python
# support was selected
################################
if(PYTHON_FOUND)
   add_subdirectory(python)

   list(APPEND ascent_sources
        runtimes/flow_filters/ascent_python_script_filter.cpp)

   list(APPEND ascent_headers
        runtimes/flow_filters/ascent_python_script_filter.hpp)
endif()

##################
# req'd libs
##################

set(ascent_thirdparty_libs
    conduit::conduit
    ascent_flow
    ascent_lodepng)

##################
# optional libs
##################
if(OCCA_FOUND)
    list(APPEND ascent_thirdparty_libs occa)
endif()

if(UMPIRE_FOUND)
    list(APPEND ascent_thirdparty_libs umpire)
endif()

if(VTKM_FOUND)
    list(APPEND ascent_thirdparty_libs vtkm vtkh rover)
endif()

if(MFEM_FOUND)
    list(APPEND ascent_thirdparty_libs mfem)
    # mfem may require MPI, even for serial builds
    if(MFEM_MPI_ENABLED)
        list(APPEND ascent_thirdparty_libs ${ascent_blt_mpi_deps})
    endif()
endif()

if(CUDA_FOUND)
    list(APPEND ascent_thirdparty_libs cuda_runtime)
    # record some extra libs for our config.mk
    # dl and rt
    set(ASCENT_CUDA_MAKE_EXTRA_LIBS "-ldl -lrt "  CACHE STRING "" FORCE)
    # pthreads
    if(CMAKE_THREAD_LIBS_INIT)
        set(ASCENT_CUDA_MAKE_EXTRA_LIBS "${ASCENT_CUDA_MAKE_EXTRA_LIBS} ${CMAKE_THREAD_LIBS_INIT}"  CACHE STRING "" FORCE)
    endif()
endif()

if(OPENMP_FOUND)
    list(APPEND ascent_thirdparty_libs ${ascent_blt_openmp_deps})
endif()

if(DRAY_FOUND)
    list(APPEND ascent_thirdparty_libs dray)
endif()

if(GENTEN_FOUND)
    list(APPEND ascent_thirdparty_libs genten)
endif()


##########################################
# Build a serial version of ascent
##########################################
if (ENABLE_SERIAL)
    blt_add_library(
        NAME        ascent
        SOURCES     ${ascent_sources}
        HEADERS     ${ascent_headers}
        DEPENDS_ON  ${ascent_thirdparty_libs})

    set_target_properties(ascent PROPERTIES CXX_VISIBILITY_PRESET hidden)
    target_compile_definitions(ascent PRIVATE ASCENT_EXPORTS_FLAG)

    if(CUDA_FOUND)
      target_compile_definitions(ascent PRIVATE ASCENT_CUDA_ENABLED)
    endif()

    if(ENABLE_LOGGING)
      target_compile_definitions(ascent PRIVATE ASCENT_LOGGING_ENABLED)
    endif()

    if(VTKM_FOUND)
        vtkm_add_target_information(ascent DEVICE_SOURCES ${ascent_vtkm_device_sources})
    endif()

    # install target for serial ascent lib
    install(TARGETS ascent
            EXPORT  ascent
            LIBRARY DESTINATION lib
            ARCHIVE DESTINATION lib
            RUNTIME DESTINATION lib
    )


    # Static linking CUDA
    if(VTKM_FOUND AND CUDA_FOUND AND NOT BUILD_SHARED_LIBS)
      set(locations_on_disk "")

      set(binary_dir "${CMAKE_CURRENT_BINARY_DIR}/CMakeFiles/ascent.dir")

      set(targets_to_link rover
        vtkh_rendering
        vtkh_compositing
        vtkh_filters
        vtkh_core
        vtkh_lodepng
        vtkh_utils
        vtkmdiympi_nompi
        vtkm_io
        vtkm_rendering
        vtkm_filter_common
        vtkm_filter_contour
        vtkm_filter_gradient
        vtkm_filter_extra
        vtkm_worklet
        vtkm_source
        vtkm_cont
        vtkm_lodepng
      )

      if(ENABLE_TESTS)
        if (NOT TARGET vtkm_cont_testing)
          message(FATAL_ERROR "ENABLE_TESTS requires a VTKm that provides vtkm_cont_testing")
        endif ()
        list(APPEND targets_to_link
          vtkm_cont_testing)
      endif()

      source_objects(${binary_dir}
                     "${ascent_device_sources}"
                     "$<TARGET_FILE:rover>"
                     "${targets_to_link}"
                     locations_on_disk)

      separate_arguments(params NATIVE_COMMAND "${CMAKE_CUDA_FLAGS} ${locations_on_disk}")
      add_custom_command(
        TARGET ascent PRE_LINK
        DEPENDS ascent rover
        COMMAND ${CMAKE_CUDA_COMPILER} --device-link ${params} -lcudadevrt -lcudart_static --output-file bcal_vtkm.o
        COMMENT "manual device link step for Ascent"
        )
      install(FILES ${CMAKE_BINARY_DIR}/ascent/bcal_vtkm.o DESTINATION lib)
    endif()

endif()

################################################
# Build Parallel (MPI) version of ascent
################################################
if(MPI_FOUND)

    # req'd libs
    set(ascent_mpi_thirdparty_libs ${ascent_blt_mpi_deps} conduit::conduit_mpi)

    if (BABELFLOW_FOUND)
      if(CMAKE_BUILD_TYPE STREQUAL "DEBUG")
        add_compile_definitions("BABELFLOW_DEBUG")
      endif()

      list(APPEND ascent_headers runtimes/flow_filters/ascent_runtime_babelflow_filters.hpp)
      list(APPEND ascent_sources runtimes/flow_filters/ascent_runtime_babelflow_pmt.cpp)
      list(APPEND ascent_sources runtimes/flow_filters/ascent_runtime_babelflow_comp_utils.hpp)
      list(APPEND ascent_sources runtimes/flow_filters/ascent_runtime_babelflow_comp_utils.cpp)
      list(APPEND ascent_sources runtimes/flow_filters/ascent_runtime_babelflow_compose.cpp)

      list(APPEND ascent_mpi_thirdparty_libs BabelFlow::babelflow BabelFlow::babelflow_mpi)
      list(APPEND ascent_mpi_thirdparty_libs PMT::pmt)
      add_compile_definitions(ASCENT_BABELFLOW_ENABLED)
      include_directories(${BABELFLOW_INCLUDE_DIRS})
      include_directories(${PMT_INCLUDE_DIRS})
    endif ()

    # hola mpi support is only in par (mpi ver)

    list(APPEND ascent_headers runtimes/flow_filters/ascent_runtime_hola_filters.hpp)
    list(APPEND ascent_sources runtimes/flow_filters/ascent_runtime_hola_filters.cpp)

    list(APPEND ascent_headers hola/ascent_hola_mpi.hpp)
    list(APPEND ascent_sources hola/ascent_hola_mpi.cpp)

    # adios support is only in par (mpi ver)
    if (ASCENT_FIDES_ENABLED AND MPI_FOUND)
       list(APPEND ascent_headers runtimes/flow_filters/ascent_runtime_adios2_filters.hpp)
       list(APPEND ascent_sources runtimes/flow_filters/ascent_runtime_adios2_filters.cpp)
       list(APPEND ascent_mpi_thirdparty_libs adios2::adios2 fides)
    endif()

    # this combines our mpi specific deps to the broader list of ascent deps
    list(APPEND ascent_mpi_thirdparty_libs ${ascent_thirdparty_libs})

    if(VTKM_FOUND)
        if (BABELFLOW_FOUND)
            list(APPEND ascent_sources runtimes/flow_filters/ascent_runtime_babelflow_iso.cpp)
        endif()

        # swap vtkh_mpi for vtkh
        list(REMOVE_ITEM ascent_mpi_thirdparty_libs vtkh)
        list(APPEND ascent_mpi_thirdparty_libs vtkh_mpi)
        list(REMOVE_ITEM ascent_mpi_thirdparty_libs rover)
        list(APPEND ascent_mpi_thirdparty_libs rover_mpi)
    endif()
    if(DRAY_FOUND)
        # swap dray_mpi for dray
        list(REMOVE_ITEM ascent_mpi_thirdparty_libs dray)
        list(APPEND ascent_mpi_thirdparty_libs dray_mpi)
    endif()

    blt_add_library(
        NAME        ascent_mpi
        SOURCES     ${ascent_sources} ${ascent_mpi_sources}
        HEADERS     ${ascent_headers} ${ascent_mpi_headers}
        DEPENDS_ON  ${ascent_mpi_thirdparty_libs})

    set_target_properties(ascent_mpi PROPERTIES CXX_VISIBILITY_PRESET hidden)
    target_compile_definitions(ascent_mpi PRIVATE ASCENT_EXPORTS_FLAG)

    if(ENABLE_LOGGING)
      target_compile_definitions(ascent_mpi PRIVATE ASCENT_LOGGING_ENABLED)
    endif()

    if(CUDA_FOUND)
      target_compile_definitions(ascent_mpi PRIVATE ASCENT_CUDA_ENABLED)
    endif()

    if(VTKM_FOUND)
        vtkm_add_target_information(ascent_mpi DEVICE_SOURCES ${ascent_vtkm_device_sources})
    endif()
    blt_add_target_compile_flags(TO ascent_mpi FLAGS "-DASCENT_MPI_ENABLED")

    # install target for mpi ver
    install(TARGETS ascent_mpi
            EXPORT  ascent
            LIBRARY DESTINATION lib
            ARCHIVE DESTINATION lib
            RUNTIME DESTINATION lib
    )

    # Static linking CUDA
    if(VTKM_FOUND AND CUDA_FOUND AND NOT BUILD_SHARED_LIBS)
      set(locations_on_disk "")
      set(binary_dir "${CMAKE_CURRENT_BINARY_DIR}/CMakeFiles/ascent_mpi.dir")

      set(targets_to_link
        rover
        vtkh_rendering_mpi
        vtkh_compositing_mpi
        vtkh_filters_mpi
        vtkh_core_mpi
        vtkh_utils_mpi
        vtkm_io
        vtkm_rendering
        vtkm_filter_common
        vtkm_filter_contour
        vtkm_filter_gradient
        vtkm_filter_extra
        vtkm_worklet
        vtkm_source
        vtkm_cont
        vtkm_lodepng
      )

      if(TARGET vtkmdiympi_nompi)
        list(APPEND targets_to_link vtkmdiympi_nompi)
      endif()
      if(TARGET vtkmdiympi)
        list(APPEND targets_to_link vtkmdiympi)
      endif()

      if(ENABLE_TESTS)
        if (NOT TARGET vtkm_cont_testing)
          message(FATAL_ERROR "ENABLE_TESTS requires a VTKm that provides vtkm_cont_testing")
        endif ()
        list(APPEND targets_to_link
          vtkm_cont_testing)
      endif()

      source_objects(${binary_dir}
                     "${ascent_device_sources}"
                     "$<TARGET_FILE:rover_mpi>"
                     "${targets_to_link}"
                     locations_on_disk)

      separate_arguments(params NATIVE_COMMAND "${CMAKE_CUDA_FLAGS} ${locations_on_disk}")
      #message(STATUS "${CMAKE_CUDA_COMPILER} --device-link ${params} -lcudadevrt -lcudart_static --output-file bcal_vtkm_mpi.o")
      add_custom_command(
        TARGET ascent_mpi PRE_LINK
        DEPENDS ascent_mpi rover_mpi
        COMMAND ${CMAKE_CUDA_COMPILER} --device-link ${params} -lcudadevrt -lcudart_static --output-file bcal_vtkm_mpi.o
        COMMENT "manual device link step for Ascent parallel"
        )
      install(FILES ${CMAKE_BINARY_DIR}/ascent/bcal_vtkm_mpi.o DESTINATION lib)
    endif()

endif()

########################################
# Extra Install Targets for ascent
########################################
install(FILES ${ascent_headers} DESTINATION include/ascent)

##############################################################
# install files needed for cinema viewer and web client
#############################################################
install(DIRECTORY web_clients/
        DESTINATION share/ascent/web_clients/)
