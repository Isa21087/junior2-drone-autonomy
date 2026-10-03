# Registro de la base preparada por Isabela

Fecha de las pruebas: 27 de septiembre de 2026.

Este documento registra qué se hizo, para qué se hizo y qué resultado se observó. Los comandos aislados sirven como diagnóstico; todavía deben convertirse en archivos de configuración y launch dentro del proyecto.

## 1. Entorno comprobado

```text
Sistema: Ubuntu 24.04.5 LTS
Shell: zsh
ROS 2: Jazzy
Gazebo: Harmonic, gz sim 8.15.0
Workspace previsto: ~/Universidad/RAS/drone_ws
PX4: ~/Universidad/RAS/PX4-Autopilot
Entorno Python: ~/Universidad/RAS/px4_venv
```

ROS 2 no estaba cargado automáticamente en la terminal. Para activarlo en zsh se utilizó:

```bash
source /opt/ros/jazzy/setup.zsh
```

Se comprobaron los paquetes `rviz2`, `xacro`, `robot_state_publisher`, `joint_state_publisher_gui`, `ros_gz_sim` y `ros_gz_bridge`. También se confirmó que `colcon` y Git están instalados.

## 2. PX4 clonado y fijado a una revisión

El repositorio se clonó con sus submódulos en:

```text
~/Universidad/RAS/PX4-Autopilot
```

La revisión comprobada fue:

```text
4ce66eb4662b809d88296ffab42c00cf26775f73
```

Comandos de verificación:

```bash
cd ~/Universidad/RAS/PX4-Autopilot
git describe --tags --always
git rev-parse HEAD
git submodule status --recursive
```

Felipe debe trabajar con la misma revisión para reducir diferencias entre computadores.

## 3. Dependencias de PX4

Se creó un entorno virtual de Python:

```bash
python3 -m venv ~/Universidad/RAS/px4_venv
source ~/Universidad/RAS/px4_venv/bin/activate
```

Después de instalar las dependencias se comprobó:

```bash
python3 -m pip check
```

Resultado observado:

```text
No broken requirements found.
```

También se instalaron las dependencias auxiliares de simulación necesarias para PX4 y Gazebo. No se instaló una segunda copia de Gazebo porque Harmonic ya estaba disponible mediante ROS 2.

## 4. Prueba del X500 base

Con ROS 2 cargado y el entorno virtual activo:

```bash
source /opt/ros/jazzy/setup.zsh
source ~/Universidad/RAS/px4_venv/bin/activate
cd ~/Universidad/RAS/PX4-Autopilot
make px4_sitl gz_x500
```

Resultado: PX4 SITL compiló e inició el X500 en Gazebo.

Se inspeccionaron los tópicos de Gazebo con:

```bash
gz topic -l
```

La IMU activa apareció en:

```text
/world/default/model/x500_0/link/base_link/sensor/imu_sensor/imu
```

Su tipo fue `gz.msgs.IMU`. Los mensajes incluyeron orientación, velocidad angular y aceleración lineal. Con el dron quieto se observó una aceleración vertical cercana a `9.8 m/s²`, coherente con la gravedad medida por una IMU estacionaria.

También aparecieron nombres de tópicos de LiDAR sin publicador. Esto enseñó que `gz topic -l` por sí solo no demuestra que un sensor esté produciendo datos. Se debe comprobar el publicador:

```bash
gz topic -i -t NOMBRE_DEL_TOPICO
```

## 5. Prueba del X500 con LiDAR 2D

Se inició la variante existente de PX4:

```bash
make px4_sitl gz_x500_lidar_2d
```

El tópico activo fue:

```text
/world/default/model/x500_lidar_2d_0/link/link/sensor/lidar_2d_v2/scan
```

Se verificó que tenía un publicador `gz.msgs.LaserScan`. Parámetros observados:

| Parámetro | Valor |
|---|---:|
| Muestras | 1080 |
| Ángulo mínimo | -2.356195 rad |
| Ángulo máximo | 2.356195 rad |
| Campo horizontal aproximado | 270° |
| Rango mínimo | 0.1 m |
| Rango máximo | 30 m |
| Frecuencia configurada | 30 Hz |
| Frame reportado | `link` |

Al principio todas las distancias eran `inf` porque no había obstáculos dentro del alcance del plano del sensor. Después de colocar una caja en Gazebo se obtuvieron valores finitos, por ejemplo entre aproximadamente `0.70 m` y `1.55 m`. Esto confirmó que el LiDAR respondía al escenario.

### Bridge del LiDAR

Con la simulación activa, en otra terminal se ejecutó:

```bash
source /opt/ros/jazzy/setup.zsh

ros2 run ros_gz_bridge parameter_bridge \
  '/world/default/model/x500_lidar_2d_0/link/link/sensor/lidar_2d_v2/scan@sensor_msgs/msg/LaserScan[gz.msgs.LaserScan' \
  --ros-args \
  -r /world/default/model/x500_lidar_2d_0/link/link/sensor/lidar_2d_v2/scan:=/scan
```

ROS 2 recibió el mensaje en `/scan`. La frecuencia medida fue aproximadamente `30.3 Hz`:

```bash
ros2 topic hz /scan
```

La presentación muestra frecuencias de ejemplo, pero el modelo de PX4 usado aquí tiene `update_rate` de 30 Hz. La verificación debe compararse con la configuración real del sensor del proyecto.

## 6. Revisión física del modelo de LiDAR

El archivo original revisado fue:

```text
Tools/simulation/gz/models/lidar_2d_v2/model.sdf
```

Hallazgos:

- Masa: `0.37 kg`.
- Centro del bloque inercial: `0 0 0.0435`.
- El visual utiliza una malla DAE.
- Las colisiones usan formas simples: dos cajas y un cilindro.
- Esto coincide con la recomendación de la clase: malla detallada para visual y geometrías sencillas para colisión.

El sensor está unido al `base_link` mediante un joint fijo en el modelo `x500_lidar_2d`. Se observó que la pose del include y la pose del joint no son idénticas; debe estudiarse antes de copiar o modificar el modelo. No se debe “corregir” sin comprobar primero cómo resuelve SDF esas poses.

## 7. Prueba del X500 con cámara monocular

Se detuvo la variante anterior y se inició:

```bash
make px4_sitl gz_x500_mono_cam
```

Los tópicos encontrados fueron:

```text
/world/default/model/x500_mono_cam_0/link/camera_link/sensor/camera/image
/world/default/model/x500_mono_cam_0/link/camera_link/sensor/camera/camera_info
```

En el panel **Image Display** de Gazebo se seleccionó el tópico de imagen. Al colocar una caja delante del sensor, la imagen mostró la caja y su sombra. Esto confirmó el funcionamiento de la cámara dentro de Gazebo.

Configuración observada en el modelo:

| Parámetro | Valor |
|---|---:|
| Resolución | 1280 × 960 |
| FOV horizontal | 1.74 rad, aproximadamente 100° |
| Plano cercano | 0.1 m |
| Plano lejano | 3000 m |
| Frecuencia | 30 Hz |
| Frame | `camera_link` |

El modelo de cámara tiene masa e inercia, pero no contiene un bloque de colisión. Esto queda como punto para analizar cuando se prepare el modelo propio.

### Bridge de cámara hacia ROS 2

Se ejecutó y registró el bridge hacia ROS 2:

```bash
source /opt/ros/jazzy/setup.zsh

ros2 run ros_gz_bridge parameter_bridge \
  '/world/default/model/x500_mono_cam_0/link/camera_link/sensor/camera/image@sensor_msgs/msg/Image[gz.msgs.Image' \
  --ros-args \
  -r /world/default/model/x500_mono_cam_0/link/camera_link/sensor/camera/image:=/camera/image_raw
```

Comprobaciones previstas:

```bash
ros2 topic echo /camera/image_raw --once --field width
ros2 topic hz /camera/image_raw
```

El ancho esperado por la configuración es 1280 y la frecuencia esperada es cercana a 30 Hz.

## 8. Qué permanece y qué fue temporal

Permanece en el computador:

- La instalación de ROS 2, Gazebo y dependencias.
- El repositorio de PX4.
- El entorno virtual.
- Las capacidades conocidas y los comandos registrados aquí.

Fue temporal:

- Cada ejecución de PX4/Gazebo.
- Los bridges iniciados manualmente.
- Los obstáculos añadidos desde la interfaz si el mundo no se guardó.

Por eso el siguiente trabajo es convertir las pruebas en modelo, mundo, configuración YAML y launch reproducibles dentro del repositorio del equipo.


## 9. Resultados del bridge de cámara e IMU

- Tópico de imagen: `/camera/image_raw`.
- Información de cámara: `/camera/camera_info`.
- Tópico de IMU: `/imu`.
- Resolución observada: 1280 × 960.
- Frame de cámara: `camera_link`.
- Frame de IMU: `base_link`.
- Frecuencia observada de cámara: aproximadamente 18.6 Hz.
- Frecuencia observada de IMU: aproximadamente 233.8 Hz.
- Evidencia: `evidence/2026-09-27/`.

La cámara está configurada a 30 Hz, pero la frecuencia observada fue menor.
Esto debe analizarse junto con el Real Time Factor y el rendimiento del computador.
