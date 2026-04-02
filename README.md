# Laser Scan Matcher for ROS2
Ported to ros2 version of laser-scan-matcher by [scan_tools](https://github.com/ccny-ros-pkg/scan_tools).


## Installation
* Install modified version of [csmlib](https://github.com/AlexKaravaev/csm)

## Topics

### Subscribed topics
- `/scan` ([sensor_msgs/LaserScan](http://docs.ros.org/melodic/api/sensor_msgs/html/msg/LaserScan.html))
- `/tf` ([tf2_msgs/TFMessage](http://docs.ros.org/melodic/api/tf2_msgs/html/msg/TFMessage.html))
### Published topics
- `/tf` ([tf2_msgs/TFMessage](http://docs.ros.org/melodic/api/tf2_msgs/html/msg/TFMessage.html)) with transform odom->base_link
- `/odom` ([nav_msgs/Odometry](https://github.com/ros2/common_interfaces/blob/master/nav_msgs/msg/Odometry.msg)) Optional. Parameter publish_odom must be set to the name of the topic. If topic is empty, odom will not be published.

## Will be released features:
- [x] Support of pure laserscan
- [ ] Support of IMU
- [ ] Support of odometry
- [ ] Support of PointCloud msgs

## Example usage:
`ros2 run ros2_laser_scan_matcher laser_scan_matcher --ros-args -p publish_odom:=/odom -p publish_tf:=true`

## Changes in this fork

### ICP covariance output
The node can compute and publish the ICP covariance matrix (Censi's method) in the odometry message. The published covariance reflects the per-step uncertainty of the current scan relative to the reference scan — accumulation across keyframes is intentionally omitted. If covariance computation is disabled or fails, a diagonal fallback `0.01·I` is used.

New parameter:
- `do_compute_covariance` (int, default `0`) — set to `1` to enable covariance computation.

### Keyframe update policy
New parameter:
- `kf_update_policy` (string, default `"fixed"`) — controls when the reference scan is replaced:
  - `"fixed"` — reference scan is never updated (fixed to the first scan);
  - `"distance"` — reference scan is updated when displacement exceeds `kf_dist_linear` / `kf_dist_angular`.

### Reference scan averaging
New parameter:
- `n_reference_scans` (int, default `1`) — number of scans to average when building the initial reference scan. Values greater than 1 reduce noise in the reference scan at startup.
