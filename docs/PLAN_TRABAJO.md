# Plan de trabajo y división del equipo

## Fuentes utilizadas

1. Presentación de clase `Clase_ROS2_Gazebo_CAD_a_Bridge.pptx.pdf`.
2. Propuesta aprobada `isabela_felipe_drone_project_proposal_final.pdf`.
3. Modelos y comportamiento observados en la revisión fijada de PX4.

La presentación describe un flujo general basado en URDF/Xacro. PX4 utiliza modelos SDF para su simulación en Gazebo. El equipo adaptará el flujo manteniendo SDF para la simulación de PX4 y una representación URDF/Xacro coherente para TF y RViz.

## Entrega inmediata de la clase

La presentación no contiene una página titulada “tarea” con un enunciado independiente. Por ello, el objetivo inmediato se toma de su objetivo y lista final de verificación:

```text
descripción → TF/RViz → física/Gazebo → sensores → bridge → ROS 2
```

Aplicado al proyecto del equipo significa demostrarlo con el X500, no con el robot diferencial de ejemplo.

## Estado por capacidad

| Capacidad | Estado | Evidencia actual | Falta |
|---|---|---|---|
| PX4 SITL + X500 | Validada manualmente | X500 inicia en Gazebo | Convertir la ejecución en instrucciones finales |
| IMU en Gazebo | Validada | Mensajes `gz.msgs.IMU` | Bridge y medición de frecuencia en ROS 2 |
| LiDAR en Gazebo | Validada | Rangos finitos ante una caja | Guardarlo en modelo propio |
| LiDAR en ROS 2 | Validada manualmente | `/scan` a ~30.3 Hz | `bridge.yaml` y launch |
| Cámara en Gazebo | Validada | Imagen con obstáculo y sombra | Bridge hacia ROS 2 |
| Modelo combinado | Pendiente | Variantes separadas de PX4 | X500 + LiDAR + cámara del equipo |
| Mundo guardado | Pendiente | Objetos manuales | Archivo SDF reproducible |
| URDF/Xacro y TF | Pendiente | — | Descripción coherente del X500 y sensores |
| RViz | Pendiente | — | RobotModel, TF y sensores |
| Launch general | Pendiente | — | Arranque reproducible |
| ROS 2 ↔ PX4 DDS | Pendiente | — | Estado del vehículo desde `px4_msgs` |

## División del ciclo actual

### Isabela — integración ROS 2 y documentación

1. Terminar el bridge de cámara y registrar ancho, tópico y frecuencia.
2. Probar el bridge de IMU y documentar su tópico ROS 2.
3. Convertir los bridges manuales en `bridge.yaml`.
4. Preparar el URDF/Xacro inicial para TF/RViz, coordinando los frames con el SDF.
5. Mantener actualizado el README y el registro de pruebas.

### Felipe — modelo físico y mundo

1. Reproducir la instalación con la misma revisión de PX4.
2. Analizar la composición del X500, LiDAR y cámara.
3. Crear el mundo SDF de pruebas guardado.
4. Preparar el modelo combinado X500 + LiDAR + cámara dentro del repositorio.
5. Documentar masas, inercias, colisiones, joints y poses.

### Integración conjunta

1. Revisar mutuamente el trabajo de cada rama.
2. Alinear los nombres de frames entre SDF, URDF/Xacro y mensajes.
3. Crear `simulation.launch.py`.
4. Ejecutar la lista completa de comprobación.
5. Grabar una demostración corta.

## Criterios de terminado del ciclo

- Un comando documentado inicia la simulación.
- El X500 aparece estable en el mundo guardado.
- LiDAR, IMU y cámara publican en Gazebo.
- ROS 2 recibe `/scan`, `/imu` y `/camera/image_raw` mediante configuración guardada.
- Se registran las frecuencias reales y se comparan con los `update_rate` configurados.
- RViz muestra RobotModel, TF y al menos los datos del LiDAR.
- Felipe puede ejecutar el trabajo de Isabela e Isabela puede ejecutar el de Felipe.

## Flujo de Git propuesto

Ramas iniciales:

```text
main
├── isa/ros-bridge-documentation
└── felipe/model-world
```

Reglas:

- `main` debe mantenerse ejecutable.
- No subir `build/`, `install/`, `log/`, el repositorio completo de PX4 ni el entorno virtual.
- Hacer commits pequeños que indiquen la capacidad añadida.
- Antes de fusionar, la otra persona debe seguir las instrucciones y comprobar el resultado.
- Registrar limitaciones y fallos conocidos; no ocultarlos en capturas.

Ejemplos de commits:

```text
docs: registrar validacion inicial de PX4 y sensores
feat(gz): agregar mundo controlado de pruebas
feat(gz): combinar x500 con lidar y camara
feat(bridge): configurar lidar imu y camara
feat(description): agregar frames del x500 para rviz
```

