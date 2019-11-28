cmake_minimum_required(VERSION 2.8.3)
project(fftw3_catkin)

add_definitions(-std=c++14)

find_package(catkin_simple REQUIRED)
catkin_simple()

include_directories(${Eigen_INCLUDE_DIRS})

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(fftw3_src
  URL http://fftw.org/fftw-3.3.8.tar.gz
  URL_MD5 8aac833c943d8e90d51b697b27d4384d
  SOURCE_DIR fftw3_src
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND
      # Configuring FFTW3
      mkdir -p ../fftw3_src/build/ &&
      cd ../fftw3_src/build &&
      cmake ..
        -DCMAKE_BUILD_TYPE=Release
        -DCMAKE_BUILD_TYPE=Release
        -LA
        -G"Unix Makefiles"
        -DDISABLE_FORTRAN=ON
        -DENABLE_AVX2=ON
        -DENABLE_SSE=ON
        -DENABLE_SSE2=ON
        -DENABLE_AVX=ON
        -DENABLE_THREADS=ON
        -DENABLE_OPENMP=ON
  BUILD_COMMAND
      # Building FFTW3
      cd ../fftw3_src/build/ &&
      make -j8
  INSTALL_COMMAND
      cp ../fftw3_src/build/libfftw3* ${CATKIN_DEVEL_PREFIX}/lib/ &&
      cp  ../fftw3_src/api/*.h ${CATKIN_DEVEL_PREFIX}/include/fftw3 &&
)

cs_install()
cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include)
