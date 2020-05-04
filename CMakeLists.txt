cmake_minimum_required(VERSION 2.8.3)
project(fftw3_catkin)

add_definitions(-std=c++14)

find_package(catkin_simple REQUIRED)
catkin_simple(ALL_DEPS_REQUIRED)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

SET(FFTW_SRC_PATH "fftw3_src")
SET(FFTW_BUILD_PATH "fftw3_build")
ExternalProject_Add(fftw3_src
  URL http://fftw.org/fftw-3.3.8.tar.gz
  URL_MD5 8aac833c943d8e90d51b697b27d4384d
  SOURCE_DIR ${FFTW_SRC_PATH}
  BINARY_DIR ${FFTW_BUILD_PATH}
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND
      # Configuring FFTW3
      #mkdir -p ../fftw3_src/build/ &&
      cd ../${FFTW_BUILD_PATH} &&
      cmake ../${FFTW_SRC_PATH}
	-DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
        -DCMAKE_BUILD_TYPE=Release
        -LA
        -DDISABLE_FORTRAN=ON
        -DENABLE_AVX2=ON
        -DENABLE_SSE=ON
        -DENABLE_SSE2=ON
        -DENABLE_AVX=ON
        -DENABLE_THREADS=ON
        -DENABLE_OPENMP=ON
  BUILD_COMMAND
      # Building FFTW3
      cd ../${FFTW_BUILD_PATH} && make -j8
  INSTALL_COMMAND
      cp ../${FFTW_BUILD_PATH}/libfftw3.so ${CATKIN_DEVEL_PREFIX}/lib/ &&
      cp ../${FFTW_BUILD_PATH}/libfftw3_threads.so ${CATKIN_DEVEL_PREFIX}/lib/ &&
      cp ../${FFTW_BUILD_PATH}/libfftw3_omp.so ${CATKIN_DEVEL_PREFIX}/lib/ &&
      mkdir -p ${CATKIN_DEVEL_PREFIX}/include/fftw3 &&
      cp  ../${FFTW_SRC_PATH}/api/fftw3.h ${CATKIN_DEVEL_PREFIX}/include/fftw3
)

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        FILES_MATCHING PATTERN "libfftw3*")

cs_add_library(${PROJECT_NAME} src/wrap.cc)
add_dependencies(${PROJECT_NAME} fftw3_src)
target_link_libraries(${PROJECT_NAME}
  ${CATKIN_DEVEL_PREFIX}/lib/libfftw3${CMAKE_SHARED_LIBRARY_SUFFIX}
  ${CATKIN_DEVEL_PREFIX}/lib/libfftw3_threads${CMAKE_SHARED_LIBRARY_SUFFIX}
  ${CATKIN_DEVEL_PREFIX}/lib/libfftw3_omp${CMAKE_SHARED_LIBRARY_SUFFIX})
cs_install()
cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
	  LIBRARIES fftw3 fftw3_threads fftw3_omp)
