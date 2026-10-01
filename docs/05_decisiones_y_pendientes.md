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

## Capacidades comprobadas

- Bridge de imagen y CameraInfo mediante YAML y launch.
- Bridge de IMU mediante YAML y launch.
- Bridge de LiDAR mediante YAML y launch.
- Compilación del paquete en la estructura nueva de `ras_ws`.

Los sensores se probaron en variantes separadas de PX4.
Todavía no se verificó su visualización completa mediante TF/RViz.

La cámara produjo aproximadamente 18.6 Hz en la prueba inicial,
aunque está configurada a 30 Hz. La causa de esa diferencia
no está determinada; se debe revisar el tiempo de simulación,
rendimiento y medición antes de atribuirla a un componente.

## Próximos pasos de la clase

- Reproducir la base en el computador de Felipe.
- Revisar juntos tópicos, frames y archivos de configuración.
- Crear y validar la descripción URDF/Xacro y TF/RViz.
- Preparar un mundo SDF guardado mediante tareas compartidas.

## Pendientes posteriores

- Revisar las poses del LiDAR y las propiedades físicas de los sensores.
- Preparar un modelo combinado propio.
- Crear un launch general.
- Integrar ROS 2 con PX4 mediante DDS y px4_msgs.
- Incorporar QGroundControl a las pruebas de vuelo.
- Avanzar hacia mapeo, navegación y misión según la propuesta aprobada.