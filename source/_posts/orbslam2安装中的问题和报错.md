---
title: ORBslam2安装中的问题和报错
tags:
  - slam
date: 2020-09-18 00:00:00
update: 
---

# 安装中出现的报错问题以及解决方案

## 无法找到lib
  - 报错信息
    ```shell
    make[1]: *** Waiting for unfinished jobs....
    collect2: error: ld returned 1 exit status
    CMakeFiles/Stereo.dir/build.make:203: recipe for target '../Stereo' failed
    make[2]: *** [../Stereo] Error 1
    CMakeFiles/Makefile2:104: recipe for target 'CMakeFiles/Stereo.dir/all' failed
    make[1]: *** [CMakeFiles/Stereo.dir/all] Error 2
    Makefile:129: recipe for target 'all' failed
    make: *** [all] Error 2
    ```
  - 解决方案
  在`Examples/ROS/ORB_SLAM2/CMakeLists.txt`文件中添加编译信息-lboost_systema
  ```cmake
  	set(LIBS 
		${OpenCV_LIBS} 
		${EIGEN3_LIBS}
		${Pangolin_LIBRARIES}
		${PROJECT_SOURCE_DIR}/../../../Thirdparty/DBoW2/lib/libDBoW2.so
		${PROJECT_SOURCE_DIR}/../../../Thirdparty/g2o/lib/libg2o.so
		${PROJECT_SOURCE_DIR}/../../../lib/libORB_SLAM2.so
		-lboost_systema ## 添加这一项
    )
  ```
## 无法找到ros package 或者ros
  - 测试方式
    ```shell
        echo $ROS_ROOT
          /opt/ros/melodic/share/ros
        echo $ROS_PACKAGE_PATH
          /opt/ros/melodic/share:/home/test/slam/ORB_SLAM2-master/Examples/ROS/ORB_SLAM2
    ```
  - 解决方案
  
    用户目录的.bashrc中添加
    ```
    # 有顺序要求 添加ros_root path
    source /opt/ros/melodic/setup.bash  
    # 添加ros_package_path
    export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:/home/test/slam/ORB_SLAM2-master/Examples/ROS/ORB_SLAM2
    ```
    应用添加的环境变量
    source ~/.bashrc

## 编译pongoline出错
  > https://github.com/raulmur/ORB_SLAM2/issues/22

  - 解决方案
    ```shell
      git clone https://github.com/stevenlovegrove/Pangolin.git
      cd Pangolin
      mkdir build
      cd build
      cmake -DCPP11_NO_BOOST=1 ..
      make -j1
      Then,
      cd ORB_SLAM2
      chmod +x build.sh
      ./build.sh
    ```

## Resource not found: rgbd_launch
  - 解决方案
    ```
      sudo apt-get install ros-melodic-rgbd-launch
    ```


