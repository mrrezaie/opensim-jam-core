
if(TROPTER_COPY_DEPENDENCIES AND APPLE)

    # Extract actual name of the library libgfortran, gcc, quadmath to install along on osx
    # these show only as dependencies of libipopt, and libgcc is in same folder as libgfortran
    # not very robust but works for our artifacts.
    execute_process(COMMAND bash "-c"
        "otool -L ${IPOPT_LIBDIR}/libipopt.dylib  | grep 'libgfortran' | awk '{print $1}'"
        OUTPUT_VARIABLE libgfortran_name
        OUTPUT_STRIP_TRAILING_WHITESPACE)

    string(REPLACE "libgfortran.5" "libgcc_s.1.1" libgcc_name ${libgfortran_name})
    
    execute_process(COMMAND bash "-c"
        "otool -L ${IPOPT_LIBDIR}/libipopt.dylib  | grep 'libquadmath' | awk '{print $1}'"
        OUTPUT_VARIABLE libquadmath_name
        OUTPUT_STRIP_TRAILING_WHITESPACE)

    install(FILES
            ${ADOLC_DIR}/lib64/libadolc.2.dylib
            ${ADOLC_DIR}/lib64/libadolc.dylib
            ${ColPack_ROOT_DIR}/lib/libColPack.0.dylib
            ${IPOPT_LIBDIR}/libipopt.3.dylib
            ${IPOPT_LIBDIR}/libipopt.dylib
            ${IPOPT_LIBDIR}/libcoinmumps.3.dylib
            ${IPOPT_LIBDIR}/libcoinmumps.dylib
            ${IPOPT_LIBDIR}/libmetis.dylib
            ${libgfortran_name}
            ${libquadmath_name}
            ${libgcc_name}

            DESTINATION ${CMAKE_INSTALL_LIBDIR})

    # This command must be invoked from the cmake subdirectory so that the
    # editing of libtropter's link libraries is done after libtropter.dylib
    # is installed (add_subdirectory(cmake) must be after
    # add_subdirectory(tropter)).
    set(script
     ${CMAKE_CURRENT_BINARY_DIR}/tropter_install_mac_dependency_libraries.cmake)
    configure_file(tropter_install_mac_dependency_libraries.cmake.in
            "${script}" @ONLY)
    install(SCRIPT "${script}")

elseif(TROPTER_COPY_DEPENDENCIES AND UNIX)
    message(STATUS "Finding tropter dependencies")
    message(STATUS "getting dir for ${pkgcfg_lib_IPOPT_gfortran}" )
    message(STATUS "ADOLC_DIR dir ${ADOLC_DIR}" )
    message(STATUS "ColPack_ROOT_DIR dir ${ColPack_ROOT_DIR}" )
    message(STATUS "IPOPT_LIBDIR dir ${IPOPT_LIBDIR}" )
    
    get_filename_component(gcc_libdir "${pkgcfg_lib_IPOPT_gfortran}" DIRECTORY)

    file(GLOB gfortran "${gcc_libdir}/../../../x86_64-linux-gnu/libgfortran*.so*")
    file(GLOB quadmath "${gcc_libdir}/../../../x86_64-linux-gnu/libquadmath*.so*")
    message(STATUS "gfortran list ${gfortran}" )
    message(STATUS "quadmath list ${quadmath}" )

    install(FILES
            ${ADOLC_DIR}/lib64/libadolc.so.2
            ${ADOLC_DIR}/lib64/libadolc.so.2.1.0
            ${ADOLC_DIR}/lib64/libadolc.so
            # /usr/local/opt/boost/lib/libboost_system.so
            ${ColPack_ROOT_DIR}/lib/libColPack.so.0
            ${ColPack_ROOT_DIR}/lib/libColPack.so
            ${IPOPT_LIBDIR}/libipopt.so.3.14.16
            ${IPOPT_LIBDIR}/libipopt.so.3
            ${IPOPT_LIBDIR}/libipopt.so
            ${IPOPT_LIBDIR}/libcoinmumps.so.3.0.5
            ${IPOPT_LIBDIR}/libcoinmumps.so.3
            ${IPOPT_LIBDIR}/libcoinmumps.so
            ${IPOPT_LIBDIR}/libmetis.so

            ${gfortran}
            ${quadmath}

            DESTINATION ${CMAKE_INSTALL_LIBDIR})
        set(script
            ${CMAKE_CURRENT_BINARY_DIR}/tropter_install_linux_dependency_libraries.cmake)
        configure_file(tropter_install_linux_dependency_libraries.cmake.in
                   "${script}" @ONLY)
        install(SCRIPT "${script}")
endif()


