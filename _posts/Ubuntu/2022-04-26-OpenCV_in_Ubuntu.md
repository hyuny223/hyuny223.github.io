---
title: "[Ubuntu] 우분투에서 OpenCV 사용하기"
categories: Ubuntu
tag: [Linux, Ubuntu, OpenCV]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-04-26 00:00:01
last_modified_at: 2022-04-26
---
<br>
<br>


# 1. 기존 OpenCV 제거
---

```bash
$ sudo apt purge -y libopencv* python-opencv

$ sudo apt autoremove
```

<br>
<br>

# 2. 패키지 업그레이드
---

```bash
$ sudo apt update
$ sudo apt -y upgrade
```

<br>
<br>

# 3. 필요 패키지 설치
---

```bash
$ sudo apt install -y build-essential cmake

$ sudo apt install -y pkg-config

$ sudo apt install -y ffmpeg libavcodec-dev libavformat-dev libswscale-dev libxvidcore-dev libx264-dev libxine2-dev

$ sudo apt install -y libv4l-dev v4l-utils

$ sudo apt install -y libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev

$ sudo apt install libgtk-3-dev
# libgtk2.0-dev, libqt4-dev, libqt5-dev

$ sudo apt install -y mesa-utils libgl1-mesa-dri libgtkgl2.0-dev libgtkglext1-dev

$ sudo apt install -y libatlas-base-dev gfortran libeigen3-dev

$ sudo apt install -y python3-dev python3-numpy
```

<br>
<br>

# 4. OpenCV 설정, 컴파일, 설치
---

```bash
$ cd
$ mkdir opencv
$ cd opencv

$ wget -O opencv.zip https://github.com/opencv/opencv/archive/x.x.xzip
$ unzip opencv.zip

# 최신 버전을 다운 받아주면 된다

$ wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/x.x.x.zip
$ unzip opencv_contrib.zip

$ cd opencv-x.x.x
$ mkdir build
$ cd build

# cmake를 사용하여 OpenCV 컴파일 설정
# 컴파일에 필요한 부분을 추가하거나 제거하여 cmake를 실행하자.

$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D WITH_GRK=ON \ # 또는 -D WITH_QT=ON
-D WITH_OPENGL=ON \
-D BUILD_opencv_python3=ON
-D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-x.x.0/modules \
-D OPENCV_ENABLE_NONFREE=ON \
-D BUILD_NEW_PYTHON_SUPPORT=ON \
-D OPENCV_GENERATE_PKGCONFIG=ON ../

$ time make -j$(nproc)

$ sudo make install

$ cat/etc/ld.so.confg.d/*
# /usr/local/lib이 설치되었는지 확인
# 추가되지 않았다면 다음의 명령 실행
# sudo sh -c 'echo '/isr/local/lib' > /etc/ld.so.conf.d/opencv.confg'

$ sudo ldconfig # compile시 opencv 라이브러리를 찾을 수 있도록
```


<br>
<br>

# 5. cmake 파일 설정
---

ROS2를 기준으로 한다. 빌드로 colcon을 사용한다.

## 1. package.xml
```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>car</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="hyuny223@gmail.com">chl</maintainer>
  <license>TODO: License declaration</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <depend>rclcpp</depend>
  <depend>std_msgs</depend>
  <depend>OpenCV</depend>
  <depend>cv_bridge</depend>


  <test_depend>ament_lint_auto</test_depend>
  <test_depend>ament_lint_common</test_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>

```

<br>
<br>

## 2. CMakeLists.txt

```txt
#############################################################
cmake_minimum_required(VERSION 3.5)
project(car)
#############################################################

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

##############################################################
# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(OpenCV REQUIRED)
find_package(cv_bridge REQUIRED)

include_directories(
  include
)

add_executable(subProject src/main.cpp)
ament_target_dependencies(subProject
  "rclcpp"
  "std_msgs"
  "OpenCV"
  "cv_bridge"
)

install(TARGETS subProject
  DESTINATION lib/${PROJECT_NAME})
###############################################################

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()

```

find_package, include_directorires, add_executable, ament_target_dependencies, install을 필수적으로 설정해야 한다.

그런데 cv_bridge를 사용하려면 문제가 생기던데 어떻게 해결해야 할까?
