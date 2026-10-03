# Uso de los bridges

Estos comandos corresponden a pruebas ya realizadas y validadas por el equipo. No es necesario repetirlas salvo que se quiera reproducir la base en otro computador o verificar cambios posteriores.

Para la preparación del entorno, compilación del workspace y organización de terminales, seguir `01_instalacion_y_guia_felipe.md`.

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
