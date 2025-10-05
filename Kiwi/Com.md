sudo apt-get update && sudo apt-get install -y ros-iron-rtcm-msgs ros-iron-nmea-msgs ros-iron-diagnostic-updater libasio-dev ros-iron-geographic-msgs


export NTRIP_SERVER=sbc.igac.gov.co 
export NTRIP_USER=pedro 
export NTRIP_MOUNTPOINT=MEDE_RTCM3 
export NTRIP_PASS=airobotics export NTRIP_PORT=2102
export NTRIP_SEND_NMEA=0


	ros2 run ntrip_client ntrip_client_node

ros2 launch ublox_gps ublox_gps_node-launch.py




Ardu WO RTK
![[Pasted image 20250924092145.png]]

ARDU W RTK
![[Pasted image 20250924093204.png]]

Chino no RTK
![[Pasted image 20250924095427.png]]

