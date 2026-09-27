# Decisiones y asuntos pendientes

## Decisiones tomadas

### 1. No modificar PX4 directamente

Los archivos de `PX4-Autopilot` se usan como referencia y dependencia externa. Las adaptaciones del equipo se guardarán en el repositorio propio. Esto permite actualizar, comparar y reproducir el proyecto sin perder el origen de los modelos.

### 2. Fijar la revisión de PX4

Revisión de trabajo:

```text
4ce66eb4662b809d88296ffab42c00cf26775f73
```

Ambos integrantes deben usarla mientras se construye la base. Un cambio de revisión debe quedar documentado y probarse en ambos computadores.

### 3. Separar responsabilidades de paquetes

- `junior2_drone_description`: qué es el robot para ROS 2, TF y RViz.
- `junior2_drone_gz`: cómo se simula, sus sensores, modelos y mundo.
- `junior2_drone_bringup`: cómo se inicia e integra el sistema.

### 4. Validar por capas

Orden de diagnóstico:

1. El sensor existe y publica en Gazebo.
2. El tipo de mensaje de Gazebo es correcto.
3. El bridge está ejecutándose.
4. El tópico aparece en ROS 2.
5. Los datos y la frecuencia son coherentes.
6. El frame permite visualizar correctamente los datos.

## Diferencias que deben manejarse conscientemente

### URDF/Xacro frente a SDF de PX4

La clase presenta un flujo donde el mismo URDF alimenta visualización y simulación. La integración oficial de PX4 con Gazebo emplea modelos SDF. El equipo necesita una representación URDF/Xacro para TF/RViz que permanezca alineada con el SDF, sin reemplazar precipitadamente el modelo de vuelo de PX4.

### Frecuencias de la presentación frente al modelo real

La lista final de la clase muestra aproximadamente 10 Hz para LiDAR, 100 Hz para IMU y 30 Hz para cámara. El LiDAR de PX4 probado está configurado a 30 Hz. Se documentará la frecuencia configurada y observada de cada sensor; los números de la diapositiva no se copiarán automáticamente.

### Alcance inmediato frente al proyecto final

La práctica actual valida descripción, simulación, sensores y bridge. SLAM, Nav2, trayectorias, misión autónoma y el mundo completo de búsqueda y rescate pertenecen a niveles posteriores de la propuesta. La base actual debe prepararlos, pero no se declararán implementados.

## Pendientes técnicos

- Confirmar bridge de imagen y `camera_info` hacia ROS 2.
- Crear y probar bridge de IMU.
- Determinar los nombres finales de tópicos y frames.
- Revisar la pose del LiDAR en el include y en su joint fijo.
- Decidir si se añade una colisión simple al cuerpo de la cámara.
- Crear el modelo combinado propio.
- Crear el mundo SDF guardado.
- Crear URDF/Xacro para TF y RViz.
- Crear `bridge.yaml` y launch general.
- Integrar ROS 2 con los tópicos de PX4 mediante DDS y `px4_msgs`.
- Verificar el papel de QGroundControl en la siguiente demostración.

