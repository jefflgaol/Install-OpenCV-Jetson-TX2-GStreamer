# Install-OpenCV-Jetson-TX2-GStreamer
This guide will help you to build OpenCV (3.4.0) for Jetson TX2 (Ubuntu 18.04 - Python 3.6 - GStreamer Enabled).

## Step 1: Purging
```
$ sudo apt-get purge libopencv*
$ sudo apt-get purge python-numpy
$ sudo apt autoremove
$ sudo apt-get update
$ sudo apt-get dist-upgrade
```
## Step 2: Install Dependencies
```
$ sudo apt-get install --only-upgrade g++-5 cpp-5 gcc-5
$ sudo apt-get install build-essential make cmake cmake-curses-gui \
                       g++ libavformat-dev libavutil-dev \
                       libswscale-dev libv4l-dev libeigen3-dev \
                       libglew-dev libgtk2.0-dev
$ sudo apt-get install libdc1394-22-dev libxine2-dev \
                       libgstreamer1.0-dev \
                       libgstreamer-plugins-base1.0-dev
$ sudo apt-get install libjpeg8-dev libjpeg-turbo8-dev libtiff5-dev libavcodec-dev
$ sudo add-apt-repository “deb http://security.ubuntu.com/ubuntu xenial-security main”
$ sudo apt-get update
$ sudo apt-get install libjasper1 libjasper-dev
$ sudo apt-get install libpng-dev
$ sudo apt-get install libxvidcore-dev libx264-dev libgtk-3-dev \
                       libatlas-base-dev gfortran
$ sudo apt-get install libopenblas-dev liblapack-dev liblapacke-dev
$ sudo apt-get install qt5-default
$ sudo apt-get install python3-dev python3-pip python3-tk
$ sudo apt-get install python-dev python-pip python-tk
```
## Step 3: Install Matplotlibrc
```
$ sudo pip3 install numpy
$ sudo pip3 install matplotlib
```
Now, you need to modify the matplotlibrc and change line below accordingly (Psst! Some python installation might be different.)
```
$ sudo nano /usr/local/lib/python3.6/dist-packages/matplotlib/mpl-data/matplotlibrc
```
Change matplotlibrc relevant line (~41) to 'backend      : TkAgg'

## Step 4: Set OpenGL
Now, we need to fix OpenGL compilation problems according to this: https://devtalk.nvidia.com/default/topic/1007290/jetson-tx2/building-opencv-with-opengl-support-/post/5141945/#5141945
```
$ sudo nano /usr/local/cuda/include/cuda_gl_interop.h
```
Change cuda_gl_interop.h relevant line (~62-68) to look like this:
```
//#if defined(__arm__) || defined(__aarch64__)
//#ifndef GL_VERSION
//#error Please include the appropriate gl headers before including cuda_gl_interop.h
//#endif
//#else
#include <GL/gl.h>
//#endif
```
```
$ cd /usr/lib/aarch64-linux-gnu/
$ sudo ln -sf tegra/libGL.so libGL.so
````
## Step 5: Download OpenCV and Enable GStreamer
```
$ cd ~
$ wget https://github.com/opencv/opencv/archive/3.4.0.zip -O opencv-3.4.0.zip
$ unzip opencv-3.4.0.zip
$ cd opencv-3.4.0
$ mkdir build
$ cd build
```
For the CUDA_ARCH_BIN, I used 6.2 for TX2, and 5.3 for TX1.
```
$ cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D WITH_CUDA=ON -D CUDA_ARCH_BIN="6.2" -D CUDA_ARCH_PTX="" \
        -D WITH_CUBLAS=ON -D ENABLE_FAST_MATH=ON -D CUDA_FAST_MATH=ON \
        -D ENABLE_NEON=ON -D WITH_LIBV4L=ON -D BUILD_TESTS=OFF \
        -D BUILD_PERF_TESTS=OFF -D BUILD_EXAMPLES=OFF \
        -D WITH_GSTREAMER=ON -D WITH_GSTREAMER_0_10=OFF \
        -D WITH_QT=ON -D WITH_OPENGL=ON ..
$ make -j4
$ sudo make install
$ sudo ldconfig
```
