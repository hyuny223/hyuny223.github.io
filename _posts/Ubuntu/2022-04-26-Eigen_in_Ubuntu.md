---
title: "[Ubuntu] 우분투에서 Eigen 사용하기"
categories: Ubuntu
tag: [Linux, Ubuntu, Eigen]

toc: true
toc_sticky: true
toc_label : 목차


date: 2022-04-26 00:00:02
last_modified_at: 2022-04-26
---
<br>
<br>

# 0. 목적
---
사실,

```bash
$ sudo apt install -y libeigen3-dev
```

를 사용하면 쉽게 설치할 수 있지만, 다양한 외부 라이브러리를 사용할 필요가 있을 경우,
위와 같은 방식만으로는 설치할 수 없을 것이다.

따라서 OpenCV와 같이 직접 파일을 다운받아 CMake를 해봄으로써, 우분투에서 외부 라이브러리를 손쉽게 사용하는 방법을
익히고자 하는 것이 목적이다.

<br>
<br>

# 1. Eigen 설치
---

[다운로드 링크크(https://eigen.tuxfamily.org/index.php?title=Main_Page)

나는 recent & stable한 3.4.0 버전을 설치하였다.


<br>
<br>

# 2. Eigen 압축 풀기
---

압축을 푼 후 "/usr/include/"에 압축을 풀어주자. zip파일을 사용할 경우 다음과 같이 한다.

```bash
# 현재 폴더에 압축을 해제한다

$ sudo mv eigen-3.4.0 /usr/include
```

<br>
<br>


# 3. Cmake 실행
---

```bash
$ cd /usr/include/eigen-3.4.0
$ mkdir build
$ cd build
$ cmake . ../

# eigen-3.4.0 폴더에 CMakeCache.txt 파일이 있으면 제거하라는 설명이 나온다. 없애주면 된다.
```

<br>
<br>

# 4. ROS2 패키지 설정
---

## 1. 패키지 생성

```bash
$ ros2 pkg create <pkg 이름> --build-type ament_cmake --dependencies rclcpp std_msgs OpenCV Eigen3

# Eigen에 3을 붙여주어야 한다
```

<br>
<br>


## 2. package.xml

```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>turtle_sliding</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="hyuny223@gmail.com">chl</maintainer>
  <license>TODO: License declaration</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <depend>rclcpp</depend>
  <depend>std_msgs</depend>
  <depend>OpenCV</depend>
  <depend>Eigen3</depend>

  <test_depend>ament_lint_auto</test_depend>
  <test_depend>ament_lint_common</test_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>
```

<br>
<br>


## 3. CMakeLists.txt

```txt
cmake_minimum_required(VERSION 3.5)
project(turtle_sliding)

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

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Eigen3 REQUIRED)

include_directories(include)

add_executable(turtle_sliding src/main.cpp)
ament_target_dependencies(turtle_sliding
	"rclcpp"
	"std_msgs"
	"OpenCV"
    "Eigen3"
)

install(TARGETS turtle_sliding
		DESTINATION lib/${PROJECT_NAME})

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

<br>
<br>

## 4. include하는 법

source 파일에서는 다음과 같이 include한다.

```cpp
#include "eigen-3.4.0/Eigen/<라이브러리>"
```

빨간 줄이 나타날 것이다. path를 include해주지 않아서 그런데, 전구표시를 눌러서 include 해주거나,

c_cpp_properties.json 파일에서 다음과 같이 추가해준다.

```json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${default}",
                "${workspaceFolder}/**",
                "/opt/ros/foxy/include/**",
                "/usr/local/include/opencv4",
                "/opt/ros/foxy/include",
                "/usr/include/eigen-3.4.0" // 이 부분!
            ],
            "defines": [],
            "compilerPath": "/usr/bin/g++",
            "cStandard": "c99",
            "cppStandard": "c++14",
            "intelliSenseMode": "linux-gcc-x64"
        }
    ],
    "version": 4
}
```
