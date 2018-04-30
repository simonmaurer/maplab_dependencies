cmake_minimum_required(VERSION 2.8.3)
project(hdf5_catkin)

find_package(catkin_simple REQUIRED)

catkin_simple()

#add_definitions(-fPIC -shared)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

SET(HDF5_SRC_PATH "hdf5_src")
ExternalProject_Add(hdf5_src
        URL https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.10/hdf5-1.10.2/src/hdf5-1.10.2.tar.gz
        # URL_MD5 8D4EAE84E533EFA57496638FD0DCA8C3
        # URL https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.10/hdf5-1.10.2/src/hdf5-1.10.2.tar.zip
        # URL MD5 78DA32F160388C9F026B6C151B115CC5
        # URL https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-1.10/hdf5-1.10.2/src/hdf5-1.10.2.tar.bz2
        # URL MD5 41FB9347801B546FBA323523A1C1AF51
        UPDATE_COMMAND ""
        # SOURCE_DIR ${HDF5_SRC_PATH}
        # BINARY_DIR hdf5_build
        CONFIGURE_COMMAND cd ../${HDF5_SRC_PATH} && ./configure
        --prefix=${CATKIN_DEVEL_PREFIX}
        --enable-shared=yes
        --enable-static=yes
        --enable-fortran=yes
        --enable-cxx=yes
        # --enable-parallel
        BUILD_COMMAND cd ../${HDF5_SRC_PATH} && make -j8
        INSTALL_COMMAND cd ../${HDF5_SRC_PATH} && make install -j8
        )

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} hdf5_src)
target_link_libraries(${PROJECT_NAME}
        ${CATKIN_DEVEL_PREFIX}/lib/libhdf5${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(
        INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
        CFG_EXTRAS hdf5-extras.cmake)
