# Guía de incorporación para Felipe

## Propósito

Reproducir y comprender la base validada por Isabela. Esta guía sirve
para incorporar a Felipe al proyecto. Las responsabilidades posteriores
se comparten por pilares según `04_plan_de_trabajo.md`.

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

## 6. Clonar y compilar el proyecto del equipo

Después de aceptar la invitación y tener ROS 2 Jazzy, Gazebo Harmonic
y colcon disponibles:

```bash
mkdir -p ~/Universidad/RAS/ras_ws/src
cd ~/Universidad/RAS/ras_ws/src
git clone https://github.com/Isa21087/junior2-drone-autonomy.git
```

Si el repositorio ya está clonado, no repetir el comando.

En una terminal nueva, sin activar el entorno virtual de PX4:

```bash
source /opt/ros/jazzy/setup.zsh
cd ~/Universidad/RAS/ras_ws
colcon build --symlink-install --packages-select junior2_drone_gz
source install/setup.zsh
```

Estos comandos usan zsh. Si se utiliza bash, cargar `setup.bash`
en lugar de `setup.zsh`.

## 7. Reproducir un bridge

Seguir `02_ejecucion_bridges.md`:

1. Iniciar la variante correspondiente de PX4 en una terminal.
2. Iniciar el launch del bridge en otra.
3. Comprobar los tópicos ROS 2 en una tercera.

Elegir inicialmente cámara/IMU o LiDAR. Las pruebas actuales utilizan
modelos separados; no es necesario combinarlos para reproducir la base.

## 8. Primera actividad compartida

Felipe reproduce una prueba y registra sus resultados.
Isabela explica los archivos YAML y launch existentes.
Después, ambos revisan el recorrido:

Sensor → tópico de Gazebo → bridge → tópico de ROS 2.

Revisar también estos archivos originales de PX4:

- `Tools/simulation/gz/models/x500/model.sdf`
- `Tools/simulation/gz/models/x500_lidar_2d/model.sdf`
- `Tools/simulation/gz/models/x500_mono_cam/model.sdf`

El objetivo inicial es identificar qué modelo incluyen y qué sensor añaden.
La creación del mundo y del modelo combinado se planificará después,
con tareas complementarias para ambos.

## 9. Evidencia

Registrar:

- Revisión de PX4 utilizada.
- Variante ejecutada.
- Comando del launch.
- Tópico recibido y frame.
- Resultado de la prueba y dificultades encontradas.

Trabajar en una rama propia. Antes de fusionar, revisar el resultado
entre ambos.

