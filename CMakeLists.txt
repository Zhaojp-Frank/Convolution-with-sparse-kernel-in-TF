cmake_minimum_required(VERSION 3.5)

execute_process(COMMAND python -c "import tensorflow as tf; print(tf.sysconfig.get_include())" OUTPUT_VARIABLE Tensorflow_INCLUDE_DIRS)
execute_process(COMMAND python -c "import tensorflow as tf; print(tf.sysconfig.get_link_flags())" OUTPUT_VARIABLE Tensorflow_LIB_DIRS)

include_directories(${Tensorflow_INCLUDE_DIRS})
#link_directories("/usr/local/lib/python3.6/dist-packages/tensorflow")

find_package(CUDA)

set (CMAKE_CXX_FLAGS "--std=c++11 -fPIC -O2 -D_GLIBCXX_USE_CXX11_ABI=0 -DGOOGLE_CUDA=1 -I/opt/tensorflow/tensorflow-source/ -Wl,-rpath,/usr/local/lib/python3.6/dist-packages/tensorflow -L/usr/local/lib/python3.6/dist-packages/tensorflow -ltensorflow_framework")
SET(CUDA_NVCC_FLAGS "--expt-relaxed-constexpr")

set(CMAKE_BUILD_TYPE Debug)

#pass flags to c++ compiler
SET(CUDA_PROPAGATE_HOST_FLAGS ON)

#create library
cuda_add_library(
	conv_sparse SHARED
	conv_sparse.cu
	conv_sparse.cc)

#copy python files to build folder (for easy testing)
file(GLOB PY_FILES
  "src/*.py"
)
file(COPY ${PY_FILES} DESTINATION .)
