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


参数文件解读：
参数文件分为system_config以及usr_config。
（1）system_config
rs_perception.yaml是system_config中较为重要的参数文件。
在rs_pseries/config/system_config/perception_config/rs_perception.yaml中定义：
`    pseries_config感知算法检测范围
`        xmin: -200.0向后
`        xmax: 200.0向前
`        ymin: -80.0向右
`        ymax: 80.0向左
communication.yaml和core_config.yaml中的参数会加载至rs_perception.yaml中。
（1.1）communication.yaml
在rs_pseries/config/system_config/perception_config/communication_config/communication.yaml中定义：
`    messages发送消息内容
`        objects: true目标
`        attention_objects: false一度目标
`        object_supplement: false目标增补
`        freespaces: true可行域
`        curbs: false路沿
`        lanes: false车道
`        non_ground_indices: false非地面点云索引
`        ground_indices: false地面点云索引
`        background_indices: false背景点云索引
`        pointcloud: false点云
`        pose: false姿态
`    ros话题及节点等命名
`        frame_id: percept_frame_id坐标系ID
`        ros_topic: percept_topic感知信息话题
`        snd_nodename: percept_snd发送节点
`        rcv_nodename: percept_rcv接收节点
（1.2）core_config.yaml
在rs_pseries/config/system_config/perception_config/core_config/core_config.yaml中定义：
`    prefusion_config融合预处理参数
`    basic_detection_config基础检测参数（可行域检测、一度目标检测、检测范围等）
`    tracking_config跟踪参数（使能、匹配距离等）
middle_lidar_config.yaml中的参数会加载至rs_pseries/config/system_config/perception_config/core_config/core_config.yaml中。
在rs_pseries/config/system_config/perception_config/core_config/multi_lidar/middle_lidar_config.yaml中定义：
`    preprocess_config前处理过程参数
`    deep_detection_config深度学习检测参数（使能、检测范围等）
`    ground_filter_config地面滤波参数（使能、滤波范围、角度阈值等）
`    segmentor_config分割器参数（使能、范围等）
`    road_detection_config道路检测参数（使能、车道线检测、车道宽度等）

（2）usr_config
calibration.yaml和usr_config_only_perception.yaml是usr_config中较为重要的参数文件。
在rs_pseries/config/usr_config/calibration.yaml中定义：
`    lidar激光雷达坐标系与车体坐标系位置关系
`        x: 0向前偏移
`        y: 0向左偏移
`        z: 2.0向上偏移
`        roll: 0横滚角
`        pitch: 0俯仰角
`        yaw: 0偏行角
在rs_pseries/config/usr_config/usr_config_only_perception.yaml中定义：
`    perception感知算法输出
`        with_debug_info: true输出信息至终端
`    common输出信息
`        msg_source: 1读取在线雷达则为1 读取离线packets则为2 读取离线points则为3
`        send_packets_ros: true输出original lidar packets
`        send_points_ros: true输出original lidar points


记录原始LiDAR信息以备离线使用：
可通过记录/rslidar_packets：
`    rosbag record /rslidar_packets
可通过记录/rslidar_points：
`    rosbag record /rslidar_points




