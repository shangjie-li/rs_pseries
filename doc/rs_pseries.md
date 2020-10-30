注：本文档主要描述Ubuntu16.04中安装由速腾聚创提供的rs_pseries工作空间的过程



（一）前提
安装NVIDIA显卡驱动：
详情参见https://blog.csdn.net/Scythe666/article/details/84817959

安装ros-kinetic-desktop-full：
详情参见http://wiki.ros.org/cn/kinetic/Installation/Ubuntu

安装cuda10.0：
驱动下载地址https://developer.nvidia.com/cuda-toolkit-archive

安装cudnn7.6.5：
驱动下载地址https://developer.nvidia.com/rdp/cudnn-archive

安装其他依赖：
sudo apt-get update
sudo apt-get install -y libyaml-cpp-dev
sudo apt-get install -y libpcap-dev
sudo apt-get install -y libprotobuf-dev protobuf-compiler



（二）激光雷达驱动ros_rslidar-develop-ruby
创建ROS工作空间：
mkdir -p ruby_ws/src
拷贝功能包ros_rslidar-develop-ruby

设置文件访问权限：
cd ../ros_rslidar-develop-ruby/rslidar_drvier
chmod 777 cfg/*
cd ../ros_rslidar-develop-ruby/rslidar_pointcloud
chmod 777 cfg/*

编译：
cd ../ruby_ws
catkin_make
source devel/setup.bash



（三）工作空间rs_pseries
创建rs_pseries工作空间：
解压rs_pseries.zip

编译：
cd ../rs_pseries
mkdir build
cd ../rs_pseries/build
cmake ../
make -j8

编译通过后：
拷贝80-hasp.rules文件
sudo cp 80-hasp.rules /etc/udev/rules.d



（四）配置及运行
配置：
IP 192.168.1.102
subnet 255.255.255.0

运行（需插入由速腾聚创提供的U盘）：
cd ../rs_pseries/build
./rs_sdk_demo
cd ..
rviz -d rviz/perception.rviz




