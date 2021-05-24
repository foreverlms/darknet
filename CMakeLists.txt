cmake_minimum_required(VERSION 3.14)

project(darknet)

set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g2 -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
set(CMAKE_C_FLAGS_DEBUG  "$ENV{CFLAGS} -g2 -O0 -Wall -Wno-unused-result -Wno-unknown-pragmas -Wfatal-errors -fPIC")
set(CMAKE_C_FLAGS_RELEASE "$ENV{CFLAGS} -Ofast -Wall -Wno-unused-result -Wno-unknown-pragmas -Wfatal-errors -fPIC")

set(CMAKE_CXX_STANDARD 14)


include_directories(
        include
        src
)

set(SRC_STRING "gemm.c utils.c cuda.c deconvolutional_layer.c convolutional_layer.c list.c image.c activations.c im2col.c col2im.c blas.c crop_layer.c dropout_layer.c maxpool_layer.c softmax_layer.c data.c matrix.c network.c connected_layer.c cost_layer.c parser.c option_list.c detection_layer.c route_layer.c upsample_layer.c box.c normalization_layer.c avgpool_layer.c layer.c local_layer.c shortcut_layer.c logistic_layer.c activation_layer.c rnn_layer.c gru_layer.c crnn_layer.c demo.c batchnorm_layer.c region_layer.c reorg_layer.c tree.c  lstm_layer.c l2norm_layer.c yolo_layer.c iseg_layer.c image_opencv.cpp")
string(REPLACE " " ";" DARKNET_SOURCES ${SRC_STRING})
list(TRANSFORM DARKNET_SOURCES PREPEND "./src/")

set(EXEC_STRING "captcha.c lsd.c super.c art.c tag.c cifar.c go.c rnn.c segmenter.c regressor.c classifier.c coco.c yolo.c detector.c nightmare.c instance-segmenter.c darknet.c")
string(REPLACE " " ";" EXEC_SOURCES ${EXEC_STRING})
list(TRANSFORM EXEC_SOURCES PREPEND "./examples/")

option(USE_CUNDNN "Build with cuda." OFF)
option(USE_OPENCV "Process image with opencv." OFF)
option(USE_OPENMP "Build with openmp." OFF)

set(CMAKE_THREAD_PREFER_PTHREAD TRUE)
set(THREADS_PREFER_PTHREAD_FLAG TRUE)
find_package(Threads REQUIRED)

if (USE_OPENMP)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fopenmp")
endif ()

if (USE_OPENCV)
    add_definitions(-DOPENCV)
endif ()

add_library(darknet_static STATIC ${DARKNET_SOURCES})
add_library(darknet_shared SHARED ${DARKNET_SOURCES})


add_executable(darknet ${EXEC_SOURCES})
target_link_libraries(darknet
        ${Threads_LIBRARY}
        darknet_static)