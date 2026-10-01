# Proyecto de dron autónomo — RAS Junior 2

Proyecto de Isabela y Felipe con PX4, ROS 2 Jazzy y Gazebo Harmonic.

## Objetivo inmediato

Aplicar al X500 el flujo presentado en clase:

Descripción → TF/RViz → Gazebo → sensores → bridge → ROS 2.

El alcance se basa en el objetivo y checklist de la presentación
Clase_ROS2_Gazebo_CAD_a_Bridge. La misión autónoma de la propuesta
aprobada corresponde a etapas posteriores.

## Estado comprobado

- PX4 SITL inicia el X500 en Gazebo.
- LiDAR, cámara e IMU publican datos.
- ROS 2 recibe `/scan`, `/camera/image_raw`, `/camera/camera_info` y `/imu`.
- Los bridges tienen configuración YAML y launch guardados.
- Cámara: 1280 × 960; frecuencia observada aproximada de 18.6 Hz.
- IMU: frecuencia observada aproximada de 233.8 Hz.
- LiDAR: frecuencia observada aproximada de 30.3 Hz.

Las pruebas utilizan dos variantes separadas: `x500_mono_cam` y
`x500_lidar_2d`. Todavía no existe un modelo combinado propio.

Pendientes: URDF/Xacro, TF/RViz, mundo guardado y launch general.
La comunicación de sensores mediante ros_gz_bridge no demuestra
todavía la integración ROS 2 con el autopiloto PX4.

## Organización

El workspace local es `~/Universidad/RAS/ras_ws`.

El repositorio se clona dentro de su carpeta `src` y contiene:

- `junior2_drone_gz/`: paquete ROS 2 de configuración y launch.
- `docs/`: documentación del proyecto.
- `evidence/`: resultados de pruebas.
- `README.md`: entrada a la documentación.

PX4 y el entorno virtual permanecen fuera del workspace:
`~/Universidad/RAS/PX4-Autopilot` y `~/Universidad/RAS/px4_venv`.

## Clonar en otro computador

Después de instalar las dependencias y obtener acceso al repositorio:

```bash
mkdir -p ~/Universidad/RAS/ras_ws/src
cd ~/Universidad/RAS/ras_ws/src
git clone https://github.com/Isa21087/junior2-drone-autonomy.git
```

## Compilar

En una terminal nueva:

```bash
source /opt/ros/jazzy/setup.zsh
cd ~/Universidad/RAS/ras_ws
colcon build --symlink-install --packages-select junior2_drone_gz
source install/setup.zsh
```

## Documentación en orden de lectura

1. [Instalación y guía de incorporación](docs/01_instalacion_y_guia_felipe.md)
2. [Ejecución de los bridges](docs/02_ejecucion_bridges.md)
3. [Registro histórico de pruebas](docs/03_registro_de_pruebas.md)
4. [Plan de trabajo compartido](docs/04_plan_de_trabajo.md)
5. [Decisiones y pendientes](docs/05_decisiones_y_pendientes.md)

La numeración indica orden de lectura. Las fechas del registro indican
cuándo se realizaron las pruebas.

## Método del equipo

Trabajamos por pilares compartidos. Cada tarea tiene una persona que
implementa y otra que reproduce y revisa. Los roles se intercambian
para que ambos comprendan cada componente.

Una capacidad se considera terminada cuando está guardada,
documentada, probada y ambos integrantes pueden explicarla.