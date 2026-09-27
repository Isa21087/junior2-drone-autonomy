# Uso de los bridges

Antes de usar un bridge debe ejecutarse la variante correspondiente de PX4.

## Cámara e IMU

- PX4: `make px4_sitl gz_x500_mono_cam`
- Bridge: `ros2 launch junior2_drone_gz camera_imu_bridge.launch.py`
- Tópicos: `/camera/image_raw`, `/camera/camera_info` y `/imu`.

## LiDAR 2D

- PX4: `make px4_sitl gz_x500_lidar_2d`
- Bridge: `ros2 launch junior2_drone_gz lidar_bridge.launch.py`
- Tópico: `/scan`.
- Resultado observado: frame `link` y aproximadamente 30.3 Hz.
