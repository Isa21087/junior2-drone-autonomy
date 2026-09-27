# Guía de incorporación para Felipe

## Propósito

Reproducir la base que ya fue validada por Isabela y comenzar la parte del modelo y del mundo. Al terminar esta guía, ambos computadores deben usar las mismas versiones y deben poder iniciar el X500.

## 1. Entender qué componente hace qué

| Componente | Responsabilidad |
|---|---|
| PX4 SITL | Simula el autopiloto y controla la dinámica de vuelo. |
| Gazebo Harmonic | Simula mundo, gravedad, contactos y sensores. |
| Gazebo Transport | Lleva los mensajes internos de Gazebo. |
| `ros_gz_bridge` | Convierte mensajes entre Gazebo Transport y ROS 2. |
| ROS 2 Jazzy | Integra percepción, TF, visualización y autonomía. |
| RViz | Visualiza lo que ROS 2 conoce; no simula física. |
| QGroundControl | Supervisa y controla el vehículo mediante MAVLink. |

## 2. Requisitos del computador

Base usada por el equipo:

```text
Ubuntu 24.04
ROS 2 Jazzy
Gazebo Harmonic
Git con soporte para submódulos
colcon
```

Comprobación inicial:

```bash
lsb_release -ds
ls /opt/ros
source /opt/ros/jazzy/setup.zsh
printenv ROS_DISTRO
command -v ros2
command -v gz
gz sim --versions
command -v colcon
git --version
```

No continúes si ROS 2 no indica `jazzy` o si `gz` no está disponible. Registra la salida y habla con Isabela para comparar instalaciones.

## 3. Clonar PX4 correctamente

Ubicación acordada:

```bash
mkdir -p ~/Universidad/RAS
cd ~/Universidad/RAS
git clone --recursive https://github.com/PX4/PX4-Autopilot.git
cd PX4-Autopilot
git checkout 4ce66eb4662b809d88296ffab42c00cf26775f73
git submodule update --init --recursive
git rev-parse HEAD
```

La salida del último comando debe ser exactamente:

```text
4ce66eb4662b809d88296ffab42c00cf26775f73
```

Si ya tienes PX4 clonado, no vuelvas a clonarlo. Entra al directorio, revisa si tienes cambios con `git status` y coordina antes de cambiar de revisión.

## 4. Preparar Python y dependencias

```bash
sudo apt install python3-venv
python3 -m venv ~/Universidad/RAS/px4_venv
source ~/Universidad/RAS/px4_venv/bin/activate
cd ~/Universidad/RAS/PX4-Autopilot
bash Tools/setup/ubuntu.sh --no-nuttx --no-sim-tools
python3 -m pip check
```

Se omite NuttX porque la base actual usa SITL y se evita reinstalar Gazebo desde el script. Si tu instalación no coincide con la de Isabela, registra el error antes de cambiar paquetes al azar.

Dependencias auxiliares utilizadas en la base:

```bash
sudo apt install \
  bc libunwind-dev cppzmq-dev \
  gstreamer1.0-plugins-bad gstreamer1.0-plugins-base \
  gstreamer1.0-plugins-good gstreamer1.0-plugins-ugly \
  gstreamer1.0-libav libeigen3-dev \
  libgstreamer-plugins-base1.0-dev libimage-exiftool-perl \
  libopencv-dev libxml2-utils pkg-config protobuf-compiler
```

## 5. Probar la base

```bash
source /opt/ros/jazzy/setup.zsh
source ~/Universidad/RAS/px4_venv/bin/activate
cd ~/Universidad/RAS/PX4-Autopilot
make px4_sitl gz_x500
```

Criterio de terminado: aparece el X500 en Gazebo y PX4 SITL permanece ejecutándose sin cerrar por error.

Después prueba únicamente uno de los modelos de sensores por vez:

```bash
make px4_sitl gz_x500_lidar_2d
```

o:

```bash
make px4_sitl gz_x500_mono_cam
```

Detén la simulación anterior con `Ctrl+C` antes de iniciar otra variante.

## 6. Tarea asignada a Felipe: modelo y mundo

### Parte A — comprender los modelos existentes

Revisar sin modificar:

```text
Tools/simulation/gz/models/x500/model.sdf
Tools/simulation/gz/models/x500_base/model.sdf
Tools/simulation/gz/models/x500_lidar_2d/model.sdf
Tools/simulation/gz/models/lidar_2d_v2/model.sdf
Tools/simulation/gz/models/x500_mono_cam/model.sdf
Tools/simulation/gz/models/mono_cam/model.sdf
```

Entregar una tabla breve con:

- Modelo incluido por cada archivo.
- Links y joints añadidos.
- Parent y child de los joints de sensores.
- Pose de cada sensor respecto a `base_link`.
- Masa, centro de masa e inercia.
- Diferencia entre visual y collision.
- Nombre y parámetros principales del sensor.

### Parte B — preparar el escenario reproducible

Crear, dentro del paquete `junior2_drone_gz` cuando el equipo lo inicialice, un mundo SDF de pruebas que contenga como mínimo:

- Luz.
- Ground plane con colisión.
- Gravedad y física explícitas.
- Obstáculos con geometrías simples.
- Espacio suficiente para probar LiDAR y cámara.

El primer mundo puede ser pequeño y controlado. El mundo de búsqueda y rescate con mayor complejidad se construirá progresivamente después de validar la integración.

### Parte C — modelo combinado del equipo

Después de terminar A y B, crear una copia adaptada dentro del repositorio del equipo que combine el X500 con LiDAR 2D y cámara. No editar los originales de PX4.

Antes de declarar terminado el modelo combinado, verificar:

- El dron aparece sin temblar ni desplazarse solo.
- Los links de sensores tienen posición coherente.
- Las masas e inercias están definidas.
- Las colisiones usan geometrías simples.
- Los sensores publican en Gazebo.
- Los nombres de frames están documentados.

## 7. Evidencia que Felipe debe dejar

- Comandos usados y versiones.
- Captura del X500 ejecutándose.
- Salida de `git rev-parse HEAD` para PX4.
- Tabla de análisis de modelos.
- Archivo del mundo, cuando esté listo.
- Instrucciones para que Isabela reproduzca su trabajo.

Cada cambio debe realizarse en una rama propia y luego revisarse entre ambos.

