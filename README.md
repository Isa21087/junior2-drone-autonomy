# Proyecto de dron autónomo — RAS Junior 2

Proyecto de Isabela y Felipe para integrar PX4 Autopilot, ROS 2 Jazzy y Gazebo Harmonic sobre un cuadricóptero de la familia X500.

## Meta del proyecto

Construir una arquitectura reproducible que permita simular el dron, comunicar sus sensores y estado con ROS 2 y avanzar progresivamente hacia vuelo autónomo, mapeo, navegación y una misión de búsqueda y rescate.

El objetivo inmediato de la clase **Del CAD a ROS 2** es aplicar al dron el flujo:

```text
descripción del robot → TF/RViz → Gazebo → sensores → ros_gz_bridge → ROS 2
```

La lista de comprobación de la clase incluye:

- El robot aparece correctamente en Gazebo.
- LiDAR, IMU y cámara producen datos en Gazebo Transport.
- `ros_gz_bridge` lleva esos datos a tópicos de ROS 2.
- Se verifican los mensajes y sus frecuencias desde ROS 2.
- La descripción del robot permite representar sus frames y TF en RViz.

En PX4, el modelo de vuelo usado por Gazebo está descrito principalmente en **SDF**. Para aplicar lo visto en clase, el proyecto necesitará además una descripción **URDF/Xacro** coherente para `robot_description`, TF y RViz. Ambas descripciones deben representar los mismos frames y posiciones de sensores.

## Estado actual

La base técnica fue validada el 27 de septiembre de 2026 en el computador de Isabela:

- Ubuntu 24.04.5 LTS.
- ROS 2 Jazzy.
- Gazebo Harmonic, `gz sim` 8.15.0.
- `ros_gz_sim` y `ros_gz_bridge` instalados.
- PX4 clonado con submódulos.
- Revisión PX4: `4ce66eb4662b809d88296ffab42c00cf26775f73`.
- PX4 SITL inicia el modelo X500 en Gazebo.
- IMU del X500 publica en Gazebo.
- El LiDAR 2D publica 1080 mediciones a aproximadamente 30 Hz.
- El LiDAR fue comunicado a ROS 2 en `/scan` mediante `ros_gz_bridge`.
- La cámara monocular genera imagen en Gazebo y fue comunicada a ROS 2.
- La IMU fue comunicada a ROS 2.

La cámara y la IMU ya fueron comunicadas y registradas en ROS 2. Tampoco existe aún un modelo combinado propio, un mundo guardado, un `bridge.yaml`, un launch general o la visualización completa en RViz.

## Organización local recomendada

```text
~/Universidad/RAS/
├── PX4-Autopilot/          # repositorio externo de PX4; no se copia aquí
├── px4_venv/               # entorno virtual local; no se sube a Git
└── drone_ws/               # workspace ROS 2 y repositorio del equipo
    ├── README.md
    ├── docs/
    └── src/
```

Los paquetes ROS 2 se crearán dentro de `drone_ws/src/` cuando se defina y pruebe cada responsabilidad. La estructura prevista es:

```text
src/
├── junior2_drone_description/  # URDF/Xacro, meshes, RViz y TF
├── junior2_drone_gz/           # modelos SDF, mundos, sensores y bridge
└── junior2_drone_bringup/      # launch y configuración de integración
```

No se deben modificar directamente los modelos originales de `PX4-Autopilot/Tools/simulation/gz/models`. Los cambios del equipo se harán en este repositorio, conservando la procedencia de los archivos adaptados.

## Crear el repositorio del equipo a partir de esta base

Después de descargar y descomprimir la carpeta en `~/Universidad/RAS/`, los archivos se integran con el `drone_ws/src` vacío que ya existe:

```bash
cd ~/Universidad/RAS/drone_ws
git init -b main
git add .
git commit -m "docs: crear base reproducible del proyecto"
```

Cuando el equipo cree el repositorio remoto, se conecta con:

```bash
git remote add origin URL_DEL_REPOSITORIO
git push -u origin main
```

No se incluye una URL inventada: debe usarse la dirección real del repositorio creado por Isabela o Felipe. Después, el otro integrante podrá clonarlo y seguir `docs/GUIA_FELIPE.md`.

## Documentación

- [`docs/REGISTRO_BASE_ISA.md`](docs/REGISTRO_BASE_ISA.md): instalación, comandos ejecutados, resultados y explicación de cada prueba.
- [`docs/GUIA_FELIPE.md`](docs/GUIA_FELIPE.md): pasos para reproducir la base y tareas asignadas.
- [`docs/PLAN_TRABAJO.md`](docs/PLAN_TRABAJO.md): división del trabajo, criterios de terminado y próximos pasos.
- [`docs/DECISIONES_Y_PENDIENTES.md`](docs/DECISIONES_Y_PENDIENTES.md): decisiones técnicas, diferencias entre la clase y PX4, y asuntos que aún deben validarse.

## Regla de trabajo

Una capacidad solo se marca como terminada cuando:

1. Está guardada en el repositorio.
2. Tiene instrucciones para ejecutarla.
3. Otro integrante puede reproducirla.
4. Existe una comprobación observable.
5. Ambos integrantes pueden explicar qué componente produce, transporta y consume los datos.
