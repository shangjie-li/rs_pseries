2020.10.22


rs_pseries感知算法输出的ROS话题有：
`    /car
`    /car_array
`    /clicked_point
`    /initialpose
`    /move_base_simple/goal
`    /percept_background_rviz
`    /percept_cloud_rviz
`    /percept_cluster_rviz
`    /percept_ground_rviz
`    /percept_info_rviz
`    /percept_map_rviz
`    /percept_non_ground_rviz
`    /percept_topic
`    /percept_road_flag_rviz
`    /percept_roi_cloud_rviz
`    /rs_fusion_cloud
`    /rs_pose
`    /rslidar_packets
`    /rslidar_packets_difop
`    /rslidar_points
`    /tf
`    /tf_static
其中/percept_topic为感知信息，需借助ROS功能包rs_sdk_percept_msg_v2.0使用，终端显示感知信息：
`    cd ${rs_sdk_percept_msg_v2.0}
`    source devel/setup.bash
`    rostopic echo /percept_topic


记录原始LiDAR信息以备离线使用：
可通过记录/rslidar_packets：
`    rosbag record /rslidar_packets
可通过记录/rslidar_points：
`    rosbag record /rslidar_points


在rs_pseries/module/rs_perception/perception/auxiliary/com_aux/include/rs_perception/communicater/perception_msg/PerceptionMsg.h中定义了消息类型：
`    perception_msg::PerceptionMsg
在rs_pseries/module/rs_perception/perception/auxiliary/com_aux/include/rs_perception/communicater/rsroscomm.hpp中发布话题：
`    _pub = _rosHandler->advertise<perception_msg::PerceptionMsg>(_commConfig.rosConfig.perception_topic, 2);
在rs_pseries/module/rs_perception/perception/auxiliary/com_aux/include/rs_perception/communicater/rscommon.hpp中设置话题名：
`    robosense::perception::yamlReadAbort<std::string>(rosNode, "ros_topic", _commConfig.rosConfig.perception_topic, "ros_topic");
在rs_pseries/module/rs_perception/distribute/include/rs_perception/common/common.h中定义了函数：
`    yamlReadAbort
